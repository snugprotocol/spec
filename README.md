# The Snug Protocol

> **MCP connects agents to tools. Snug connects agents to apps.**

Snug is an open protocol for **user-built micro apps that think through their host's AI agent at runtime**. A Snug app is a small sandboxed app (often a single HTML file) that a user creates through conversation with a product's AI assistant. At runtime the app exchanges versioned JSON **envelopes** with the host agent over a strict boundary — the agent is the app's mind; the app is a body. Apps reach the user's own services only through the host, inside a human-approved, host-frozen ceiling. Users own their apps and their data as ONE portable `.snug` file (a SQLite database they can open with ordinary tools, optionally sealed with a passphrase only they hold).

- **[SPEC-v0.3-draft.md](SPEC-v0.3-draft.md)** — the consolidated specification (v0.3 draft — the working document for 1.0)
- **[SPEC.md](SPEC.md)** — spec v0.1, the published wire-protocol core
- **[SPEC-v0.2-draft.md](SPEC-v0.2-draft.md)** — the v0.2 storage draft (published record; consolidated into v0.3)
- **[schemas/](schemas/)** — JSON Schemas for every message type (published byte-identical from the reference implementation)
- **[whitepaper/](whitepaper/)** — [the whitepaper](whitepaper/snug-protocol-whitepaper.pdf) (PDF, edition 2): design rationale, threat model, security properties
- **[implementations.md](implementations.md)** — known implementations

## Status

**v0.3 — DRAFT (the 1.0 release candidate).** [SPEC-v0.3-draft.md](SPEC-v0.3-draft.md) consolidates the whole protocol into one document: the wire protocol (13 frames — the nine core frames plus the net and open-url pairs — the chat envelope, and rules R1–R7), the Portable User Database Format at storage schema 6 (including `.snug` naming and the `SNUGENC1` protected form), connected applications (requirements, grants, credential custody, and the host executor), runtime contracts and the app chat surface, and linked-device connections. The wire protocol's core is normative and unchanged at v1; the draft surfaces are published for review and finalise as spec 1.0.

**v0.1 — published.** The wire-protocol core — 9 postMessage frames, the chat envelope, and normative rules R1–R6 — as specified in [SPEC.md](SPEC.md). All 13 frame schemas in [schemas/](schemas/) are published byte-identical from the production reference implementation.

**v0.2 — DRAFT (consolidated into v0.3).** The Portable User Database Format as published for review in [SPEC-v0.2-draft.md](SPEC-v0.2-draft.md); its content carries forward into v0.3 Part II.

## Contributing

This spec is maintained through the reference implementation's engineering process — protocol proposals and discussion happen in [`snugprotocol/snug`](https://github.com/snugprotocol/snug) issues/discussions; every spec change lands here as a single traceable commit. Direct PRs to this repo are welcome for typos/clarity only.

## License

[MIT](LICENSE) · Security contact: security@snugprotocol.org

---
Maintainer: [Jeetu Maker](https://jeetu.tech.voyage)
