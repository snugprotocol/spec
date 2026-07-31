# Snug Protocol — Specification

- **Version:** v0.0 (skeleton — v0.1 draft in progress)
- **Status:** pre-release
- **Versioning:** spec versions (`v0.x`) are independent of implementation package versions. Breaking envelope changes bump the minor pre-1.0. Every published change is a single commit referencing its origin task.

## 1. Overview

Snug defines the contract between a **host** (a web product embedding an AI assistant) and a **micro app** (a small sandboxed app authored by an end user through that assistant), such that the app can use the host's agent as its runtime intelligence without ever gaining access to the host page, the user's credentials, or the network.

## 2. Roles

- **Host / bridge** — embeds the runner, relays envelopes between app and agent endpoint.
- **App** — sandboxed iframe (`allow-scripts` only; no direct network).
- **Agent endpoint** — host-side service that forwards envelopes to an LLM and returns JSON-only replies.

## 3. Envelope (to be specified in v0.1)

Message wrapper for app ↔ agent traffic: `snug/app_request`, `snug/app_response`, versioning and correlation fields, JSON-only reply contract, retry/backoff semantics, parse-failure budget. Normative schemas: [`schemas/`](schemas/).

## 4. Sandbox & security model (to be specified in v0.1)

Iframe sandbox requirements, CSP profile, CDN allowlist, the **token boundary rule** (credentials never enter the app, the LLM, or app-visible payloads). Threat model: [`whitepaper/`](whitepaper/).

## 5. Per-app data (to be specified in v0.1)

Isolated per-app database semantics, export/import as SQLite, ownership guarantees.

## 6. Authenticated connections (reserved — v0.2)

The `auth_required` message and the dual-layer (publisher + user) credential model are reserved for a post-v0.1 revision.
