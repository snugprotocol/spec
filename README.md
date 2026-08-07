# The Snug Protocol

> **MCP connects agents to tools. Snug connects agents to apps.**

Snug is an open protocol for **user-built micro apps that think through their host's AI agent at runtime**. A Snug app is a small sandboxed app (often a single HTML file) that a user creates through conversation with a product's AI assistant. At runtime the app exchanges versioned JSON **envelopes** with the host agent over a strict boundary — the agent is the app's mind; the app is a body. Users own their apps and their data (a per-app database exportable as a real `.sqlite` file).

- **[SPEC.md](SPEC.md)** — the protocol specification
- **[schemas/](schemas/)** — JSON Schemas for every message type (published from the reference implementation)
- **[whitepaper/](whitepaper/)** — [the whitepaper](whitepaper/snug-protocol-whitepaper.pdf) (PDF): design rationale, threat model, security properties
- **[implementations.md](implementations.md)** — known implementations

## Status

**v0.1 — published.** The wire protocol — 9 postMessage frames, the chat envelope, and normative rules R1–R6 — is specified in [SPEC.md](SPEC.md), with JSON Schemas in [schemas/](schemas/) published byte-identical from the production reference implementation.

**v0.2 — DRAFT.** The Portable User Database Format (one user-owned SQLite file: hub tables, native per-app tables, sync origins, export/import) is published for review in [SPEC-v0.2-draft.md](SPEC-v0.2-draft.md); the wire protocol is unchanged by it.

## Contributing

This spec is maintained through the reference implementation's engineering process — protocol proposals and discussion happen in [`snugprotocol/snug`](https://github.com/snugprotocol/snug) issues/discussions; every spec change lands here as a single traceable commit. Direct PRs to this repo are welcome for typos/clarity only.

## License

[MIT](LICENSE) · Security contact: security@snugprotocol.org

---
Maintainer: [Jeetu Maker](https://jeetu.tech.voyage)
