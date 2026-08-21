# Snug Protocol — Spec v0.2 (DRAFT) — Portable User Database Format

> **Status: DRAFT.** Published for review alongside [spec v0.1](SPEC.md); not yet
> normative — it finalizes as spec v0.2. The wire protocol (frames + envelope) is
> UNCHANGED at v1: v0.2 adds a storage and hub-behavior layer on top of v0.1. Source
> of truth for constants and DDL: the reference implementation's
> `packages/protocol/src/userdb-schema.ts` (locked by a DDL snapshot test).
>
> **Version note (2026-08-06):** the reference implementation additionally carries an
> **internal draft of storage schema v3** (`PRAGMA user_version = 3`), which adds a
> Dynamic Auth storage surface. **This document describes schema v2 and is published
> as such**; the auth surface is deliberately excluded from this publication and
> appears no earlier than a later spec revision. A v3 file is a superset of a v2 file:
> the v2→v3 migration is purely additive.
>
> **Revision 2026-08-20:** adds §6 (file naming) and §7 (the optional `SNUGENC1`
> protected form). Both are storage-surface additions; the wire protocol is still
> UNCHANGED at v1 and the tables in §2 are still schema v2 as published. §7 is
> normative for a reason worth stating plainly: a hub that does not recognise a
> protected file will conclude its bytes are corrupt and quarantine — or overwrite —
> a database that is perfectly healthy.

## 1. The three actors

- **LLM provider** — serves model calls. Reached browser-direct (BYOK key or local
  OpenAI-compatible endpoint) or through a hub's subscription path.
- **Hub provider** — a multi-tenant service that provisions Snug apps per user. A hub
  is a *convenience*, never a requirement: app execution must work with no hub backend.
- **End user** — owns apps and data as ONE SQLite file, portable across hubs, LLM
  providers, and devices.

## 2. The user database

One SQLite file per user (`user.snug` — see §6) is the canonical artifact. `PRAGMA
user_version` carries the schema version (currently **2**); migrations are
forward-only (v1→v2 is structural; v1 blob app data does not survive). Size cap:
`MAX_USERDB_BYTES` (64 MiB v1).

### 2.1 Hub-namespace tables (normative DDL in `userdb-schema.ts`)

| Table | Holds |
|---|---|
| `snug_meta` | db uuid, created-at (key/value) |
| `snug_profile` | display profile (key/value, JSON values) |
| `snug_settings` | mode, provider, model, endpoints (key/value, JSON values) |
| `snug_secrets` | BYOK keys, personal-origin tokens (opaque strings; see §4) |
| `snug_apps` | one row per app: display metadata, `current_version`, `install_source` (unique when present — a marketplace/starter identity may be installed at most once) |
| `snug_app_versions` | complete HTML per version; hubs retain ≥ `VERSIONS_RETAINED` (5) unpinned versions, pruning oldest; the factory version (v1 of a build or install) is `pinned` and NEVER pruned; revert/reset = copy-forward as a NEW version |
| `snug_app_schemas` | one row per app with data: the app's runtime `sqlite_master` DDL **verbatim** (`schema_json`: objects in creation order + AUTOINCREMENT sequence counters) and its namespace `token` |
| `snug_app_migrations` | append-only DDL audit per app (`seq`, statement, applied-at) |
| `snug_app_docs` | per-app knowledge wiki: `(app_id, slug)` → markdown (`vision`, `requirements`, `plan`, `lessons`, `memory`, `next-tasks` are advisory slug values; the table shape is normative) |
| `snug_chat_threads` / `snug_chat_messages` | every chat surface's history; messages carry `pinned` (bootstrap turns survive any pruning for the life of the app) and `meta` (JSON sidecar) |
| `snug_sync` | sync-origin CONFIG only (self-describing when ported) |

### 2.2 Per-app data: native namespaced tables (schema v2)

Each app's data lives as REAL tables in the same file under
`app_<token>__<name>`, where `token = appDataToken(namespace)` — a **normative,
total, injective** function of the host-assigned namespace: UUID-shaped →
32 lowercase hex (dashes stripped); anything else → `'x' + hex(utf8(namespace))`.

Rules (all normative):
- **Reserved prefixes** (case-insensitive): `snug_`, `sqlite_`, `app_`. App object
  names must match `^[A-Za-z][A-Za-z0-9_]{0,40}$` and carry no reserved prefix; the
  single exemption is the driver-internal `snug_kv` (at rest `app_<token>__snug_kv`).
  A conforming hub REFUSES to persist (fails closed, prior state retained) any runtime
  whose object names violate the rule — unvalidated names are never interpolated.
- **Isolation is physical at runtime**: app SQL executes only against a materialized
  database containing that app's own objects under natural names; hub-namespace and
  other apps' tables are unreachable.
- **DDL is stored verbatim** in `snug_app_schemas.schema_json` (tables, indexes,
  triggers, views, in creation order) and replayed on materialization; DDL bodies are
  never rewritten. At-rest table names are produced by SQLite `ALTER TABLE … RENAME`
  (a pure name swap), not by editing statement text.
- Per-app export = materialize + export: a standalone `.snug` with natural names.
- Hub-namespace tables are `snug_`-prefixed; apps can never reach them.
- Push-state (last pushed revision/hash) lives OUTSIDE the image (sidecar) so the file
  never contains its own revision.

### 2.3 Client-authoritative writes

The user DB is the single source of truth in every mode. In subscription mode the hub
may cache artifacts and thread history server-side, but the client fetches artifact
content and writes it into the user DB itself; hub stores are transient caches.

## 3. Hub provider obligations

A conforming hub:
1. **Never requires its backend for app execution** — the hub client is static files;
   app reads/writes hit the browser copy (OPFS) of the user DB.
2. **Offers Export/Import** — one-click download/upload of the canonical `.snug`
   (default export strips `snug_secrets` and VACUUMs; including secrets is explicit
   opt-in). Import treats the file's endpoint settings as executable config and
   requires user re-confirmation before agent turns run.
3. **May host the user DB as the default sync origin** via:
   - `GET /userdb` → `200` bytes + `ETag` revision (`application/octet-stream`,
     `nosniff`, `no-store`) or `404` when none.
   - `PUT /userdb` with `If-Match: <revision>` (or `If-None-Match: *` first write) →
     `204` + new `ETag`; mismatch → `412` + current `ETag`; missing precondition →
     `428`; over-quota → `413`. Cookie auth requires CSRF double-submit
     (`x-snug-csrf`). Unauthenticated → `401`; CORS is fail-closed (explicit origin,
     credentialed).
   - First login provisions the user record only — a hub never creates an empty DB
     image that could clobber local state; the client pushes up.
4. **Supports pluggable origins** through the `SyncProvider` contract
   (`info/pull/push(bytes, baseRevision)`); personal origins (e.g. Dropbox) may carry
   secrets on explicit opt-in. Conflict policy v1: revision-token CAS; divergence is
   surfaced to the user; last-writer-wins only on explicit user action.

## 4. Secrets posture

Secrets (`snug_secrets`) exist in the local runtime copy. They are stripped from
hub-origin pushes and default exports (VACUUMed so freed pages leak nothing) and never
enter browser `localStorage`/`sessionStorage`, any frame posted to an app iframe, or
any hub request. Persistent at-rest storage is a documented trade-off for portability;
no KMS/host-blind claim is made until a KeyProvider ships.

## 5. Execution modes

- `byok` — browser-direct frontier API; key from `snug_secrets`.
- `local` — browser-direct OpenAI-compatible endpoint (e.g. Ollama).
- `subscription` — hub-mediated `/invoke` (opt-in); body may carry a validated
  `model`; artifacts still land client-authoritatively (§2.3).

C1/C2 are unchanged in every mode: app iframes stay `sandbox="allow-scripts"` with
`connect-src` blocked; LLM calls originate from the HOST page only; credentials never
enter the iframe.

## 6. File naming

The canonical user file is **`user.snug`**; the artifact a hub offers for download is
**`snug-user.snug`**. `.snug` is the Snug Protocol's extension for the one portable
file a user owns.

The extension is a **naming convention, not a format claim**. A conforming
implementation determines a file's format from its leading bytes, never from its name:

| Leading bytes | Format |
|---|---|
| `SQLite format 3\0` | a plain user database (§2) |
| `SNUGENC1\n` | a protected user database (§7) |

- Implementations SHOULD accept the historical `.sqlite` extension on input — users
  hold exports and backups made before this revision — and MUST NOT reject a file on
  its extension alone.
- A hub that finds a pre-existing `user.sqlite` and no `user.snug` MUST read it and
  adopt the canonical name on its next write, **without renaming, copying or deleting
  the original**. The old file remains the user's own backup; once the canonical file
  exists it takes precedence.

The same read-and-adopt rule applies to any name a hub derives from the user file —
sync sidecars, quarantine copies, remote sync paths. Renaming a file an implementation
looks for is a data-loss operation unless every derived name moves with it: the new
name is absent, the "no file yet" branch runs, and the user is handed an empty
database while their real one sits on disk, unreferenced and unmentioned.

## 7. Protected user files — the `SNUGENC1` container

A user MAY protect their file with a passphrase. A protected file is not a SQLite
database; it is a container carrying one.

**This section is normative because misidentifying a protected file destroys data.** A
hub that does not recognise the magic concludes the bytes are corrupt. A conforming hub
MUST detect it and prompt for a secret; it MUST NOT treat a protected file as
corruption, MUST NOT quarantine or overwrite it, and MUST NOT create a fresh empty
database beside it.

Protection is **optional and reversible**. A conforming hub never requires it, opens an
unprotected file exactly as §2 describes, and can return a protected file to plaintext
on the user's instruction.

### 7.1 Layout

```
offset  size      field
0       9         magic            "SNUGENC1\n"
9       1         version          0x01
10      2         kdf id           0x0001 = PBKDF2-HMAC-SHA256
12      4         iterations       u32 big-endian
16      16        salt
32      2         slot count       u16 big-endian
34      4         header checksum  FNV-1a/32 over the header with this field zeroed
38      …         slot table       slot count × { kind:u8, iv:12 }
…       …         wrapped keys     slot count × 48 (AES-256-GCM of the 32-byte file key)
…       12        payload IV
…       …         payload          AES-256-GCM of the SQLite bytes of §2
```

Slot kinds: `0x01` passphrase, `0x02` recovery key.

### 7.2 Rules (all normative)

1. **Key wrapping.** A random 32-byte *file key* encrypts the payload; each slot
   independently wraps that file key under a key derived from its own secret. Changing
   one secret MUST NOT require re-encrypting the payload or invalidate other slots.
2. **Two slots minimum.** An implementation MUST NOT create a container with only a
   passphrase slot. A single point of loss with no recovery path is not an acceptable
   shape for a user's only copy of their data.
3. **Recovery-key entropy** MUST be at least 128 bits. Mind the arithmetic when the
   alphabet excludes ambiguous glyphs: 26 symbols of a 30-glyph alphabet is 127.6 bits,
   and a base-32 assumption hides the shortfall.
4. **AAD.** The header — offset 0 through the end of the slot table — MUST be supplied
   as GCM additional authenticated data for every slot unwrap and for the payload.
5. **Nonces.** Every IV MUST be 12 fresh CSPRNG bytes per encryption operation.
   Counters and derived nonces are forbidden: an implementation may write one logical
   save into two physical slots, so a repeat is reachable in ordinary operation, and a
   repeated GCM nonce discloses plaintext and forges the authentication key.
6. **Failure reporting.** Implementations MUST distinguish **locked** (a structurally
   valid container that no supplied secret opened) from **corrupt** (malformed,
   truncated, or a failed header checksum). Reporting damage as a wrong passphrase
   sends a user hunting for a secret that was never the problem; reporting a wrong
   passphrase as damage invites them to destroy a healthy file. The header checksum is
   an integrity *hint* for this purpose — it is unkeyed and does not resist a
   tamperer; rule 4 is what makes tampering fail.
7. **A locked file is never quarantined, rewritten or replaced.** It is healthy.
8. **Portability.** The container MUST be self-opening: everything needed to unwrap it,
   apart from the secret, travels inside it. No implementation may require state held
   outside the file, so any device holding the file and the secret can open it — which
   is what keeps §1's portability promise true for protected files.
9. **Size limits** (`MAX_USERDB_BYTES`, §2) apply to the PLAINTEXT the container
   carries, not to the container. Charging a user for envelope overhead would make a
   database that fits become un-importable the moment they protected it.

### 7.3 Custody, unchanged

Protection changes where the file is readable, not who holds custody. §4 stands: the
key was always the user's and still is. The claim this supports is exactly *"the file
can be encrypted with a passphrase only the user holds"* — it is **not** zero-knowledge,
**not** end-to-end encryption, and it says nothing about a host page that has already
unlocked the file. A hub-origin copy remains the secrets-stripped plaintext of §3.

**A file whose passphrase and recovery key are both lost is unrecoverable.** There is
no escrow and no reset. That is the property, not a gap in it, and an implementation
MUST state it plainly to the user before protection is enabled rather than afterwards.

