<h1 align="center">The Snug Protocol</h1>

<p align="center"><strong>An open protocol for portable, agent-backed personal software.</strong></p>

<p align="center">
  <img src="https://img.shields.io/badge/specification-1.1-orange.svg" alt="Specification 1.1" />
  <img src="https://img.shields.io/badge/status-NORMATIVE-brightgreen.svg" alt="Status: normative" />
  <img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="License: MIT" />
</p>

<p align="center">
  <a href="SPEC.md"><strong>Read the spec</strong></a> ·
  <a href="https://snugprotocol.org/docs/spec/">Read it on the web</a> ·
  <a href="https://github.com/snugprotocol/snug">Reference implementation</a> ·
  <a href="https://snugprotocol.org">snugprotocol.org</a>
</p>

---

Snug is an open protocol for **user-built micro apps that think through their host's AI agent at runtime**. A Snug app is a small sandboxed app (often a single HTML file) that a user creates through conversation with a product's AI assistant. At runtime the app exchanges versioned JSON **envelopes** with the host agent over a strict boundary — the agent is the app's *mind*; the app is a *body*. Apps reach the user's own services only through the host, inside a human-approved, host-frozen ceiling. Users own their apps and their data as **one portable `.snug` file** — a SQLite database they can open with ordinary tools, optionally sealed with a passphrase only they hold.

## What's in this repo

| | |
|---|---|
| **[SPEC.md](SPEC.md)** | **Specification 1.1** — the complete normative specification in one document |
| **[schemas/](schemas/)** | JSON Schemas for every message type — published **byte-identical** from the reference implementation |
| **[whitepaper/](whitepaper/)** | [The whitepaper](whitepaper/snug-protocol-whitepaper.pdf) (PDF, edition 4 — the 1.1 edition): design rationale, threat model, security properties |
| **[implementations.md](implementations.md)** | Known implementations |

## What the spec covers

**Specification 1.1 — NORMATIVE** (2026-10-10). One document, seven parts:

- **The wire protocol** — 15 frames (nine core plus the net, open-url and access pairs), the chat envelope, and rules R1–R7. The core has been published and stable since v0.1.
- **The Portable User Database Format** — storage schema 6, `.snug` naming, and the `SNUGENC1` protected (passphrase-sealed) form.
- **Connected applications** — requirements, grants, credential custody, and the host executor: how apps reach a user's services without credentials ever entering the app or the LLM.
- **Runtime contracts and the app chat surface** — the compact per-app turn assembly that makes runtime thinking cheap enough for small local models.
- **Linked-device connections** — hosts that bridge a personal device session (e.g. WhatsApp) without an LLM in the loop.
- **Access between apps** — one app reads another's tables only under an access grant the user gives on host chrome: read-only, scoped to tables with their columns frozen, timed, revocable, logged on the source; the two frames, the record a hub persists, and the host's obligations.
- **Conformance** — what a host must, should, and may implement.

One section is explicitly **provisional** and so marked: §17 (standing approvals). All 16 schemas in [schemas/](schemas/) are byte-identical exports from the reference implementation.

## Versioning

Post-1.0, additive changes bump the minor; a change that breaks a conforming implementation bumps the major. v0.1 (the wire-protocol core) and the v0.2/v0.3 drafts were finalised into 1.0 — the retired draft filenames are pointer stubs; their full text lives in git history. Every change lands as a single commit traceable to its task in the [reference repo](https://github.com/snugprotocol/snug).

## Contributing

This spec is maintained through the reference implementation's engineering process — protocol proposals and discussion happen in [`snugprotocol/snug`](https://github.com/snugprotocol/snug) issues/discussions; every spec change lands here as a single traceable commit. Direct PRs to this repo are welcome for typos and clarity only.

## License

[MIT](LICENSE) · Security contact: security@snugprotocol.org

---

<p align="center">Maintainer: <a href="https://jeetu.tech.voyage">Jeetu Maker</a></p>
