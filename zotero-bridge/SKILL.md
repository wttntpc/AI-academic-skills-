---
name: zotero-bridge
description: Check whether papers already exist in the user's Zotero library, and (with explicit confirmation) add new items to it. Two backends — read-only local SQLite snapshot (no setup, no API key, works only on the machine running Zotero desktop) and the Zotero Web API (needs an API key, works from anywhere, supports writes). Trigger on "is this already in my Zotero", "check my Zotero library", "add these papers to Zotero", "存進 Zotero", "我的 Zotero 裡有沒有這篇", "同步到 Zotero". Not for Obsidian/PKM notes (that's knowledge-base) — this is specifically the Zotero reference-manager library.
license: MIT
metadata:
  version: "1.0"
  skill-author: wttntpc
---

# Zotero Bridge

Bridges the literature-search skills (`litpilot`, `paper-lookup`) in this collection to a
user's actual Zotero library, so you can answer "is this new, or do I already have it?"
and, on request, file new finds into Zotero.

## Two backends — pick based on what's available

| Backend | Setup | Can write? | Works when |
|---|---|---|---|
| **Local SQLite snapshot** | None — just needs Zotero desktop installed on this machine | No (read-only) | Only on the machine running Zotero desktop |
| **Zotero Web API** | User provides an API key + library ID | Yes | Any machine, any time |

Ask the user which they have. If they say "I have Zotero desktop installed," use the local
snapshot for lookups. If they want to *add* items, or don't have Zotero installed locally,
use the Web API.

## Backend 1 — Local SQLite snapshot (read-only lookup)

**Never query the live `zotero.sqlite` directly — it is locked while Zotero is running and
concurrent reads can corrupt an in-progress write.** Always copy it first, query the copy,
then delete the copy.

1. Locate the data directory. Common locations:
   - Windows: `%USERPROFILE%\Zotero\zotero.sqlite` (default) or check
     `%APPDATA%\Zotero\Zotero\Profiles\*.default\prefs.js` for a custom `dataDir`.
   - macOS: `~/Zotero/zotero.sqlite`
   - Linux: `~/Zotero/zotero.sqlite`
2. Confirm Zotero is running and reachable (optional sanity check):
   ```bash
   curl -s -m 3 http://127.0.0.1:23119/connector/ping
   # expect: "Zotero is running"
   ```
3. Copy `zotero.sqlite` (and `zotero.sqlite-wal` / `-shm` if present — WAL-mode libraries need
   these alongside the main file for a consistent read) to a scratch/temp directory.
4. Query the **copy** with plain SQL (read-only, no write attempted):
   ```sql
   SELECT itemData.itemID, itemDataValues.value
   FROM itemData
   JOIN fields ON itemData.fieldID = fields.fieldID
   JOIN itemDataValues ON itemData.valueID = itemDataValues.valueID
   WHERE fields.fieldName = 'DOI';
   ```
   Normalize before comparing: lowercase, strip a leading `https://doi.org/`.
5. **Delete the copy when done** — it's a full snapshot of the user's personal library;
   don't leave it lying around in a scratch directory after the comparison is reported.

This backend answers "is X already in my library" but **cannot add anything** — Zotero's
own sync/writer needs to own writes to its local database.

## Backend 2 — Zotero Web API (lookup + write)

Needs from the user: an API key (read-only is enough for lookups; write scope needed to add
items) from https://www.zotero.org/settings/keys, and their numeric User ID or Group ID from
the same page.

**Lookup** (check for existing items by DOI):
```bash
curl -s "https://api.zotero.org/users/<user-id>/items?q=<title-or-doi>&qmode=everything" \
  -H "Zotero-API-Key: $ZOTERO_API_KEY"
```

**Add an item** — build a minimal JSON item and POST it. Confirm the exact fields with the
user first (at minimum: itemType, title, creators, DOI, date, url) rather than guessing at a
schema:
```bash
curl -s -X POST "https://api.zotero.org/users/<user-id>/items" \
  -H "Zotero-API-Key: $ZOTERO_API_KEY" \
  -H "Content-Type: application/json" \
  -d '[{"itemType":"journalArticle","title":"...","creators":[...],"DOI":"...","date":"..."}]'
```

Rate limit: respect a `Backoff` or `Retry-After` header if returned; don't retry
immediately on 429.

## Workflow: after a litpilot / paper-lookup run

1. Collect the DOIs of the papers found in this run.
2. Check them against the user's Zotero library (backend 1 or 2, per availability).
3. Report which are already in the library and which are new — **do not add anything without
   asking**. Adding references is a standing change to the user's personal library; always
   confirm which items (all / a subset) before writing.
4. Only after explicit confirmation, add the new ones via the Web API (backend 1 can't write).

## Safety notes

- Never write to the live `zotero.sqlite` file directly — always go through the Web API for
  writes, or let Zotero's own UI/connector handle it.
- Delete any local SQLite snapshot copy after use — it contains the user's full personal
  library, not just the papers relevant to the current task.
- The API key is a credential — never print it in full, never commit it to a repo, read it
  from an environment variable or a prompt, not from a hardcoded value.
