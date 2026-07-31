# The Snug Protocol

> **MCP connects agents to tools. Snug connects agents to apps.**

Snug is an open protocol for **user-built micro apps that think through their host's AI agent at runtime**. A Snug app is a small sandboxed app (often a single HTML file) that a user creates through conversation with a product's AI assistant. At runtime the app exchanges versioned JSON **envelopes** with the host agent over a strict boundary — the agent is the app's mind; the app is a body. Users own their apps and their data (a per-app database exportable as a real `.sqlite` file).

- **[SPEC.md](SPEC.md)** — the protocol specification
- **[schemas/](schemas/)** — JSON Schemas for every message type (published from the reference implementation)
- **[whitepaper/](whitepaper/)** — design rationale, threat model, security properties
- **[implementations.md](implementations.md)** — known implementations

## Status

**v0.0 — pre-release skeleton.** The v0.1 draft is being extracted from a production-proven implementation. Watch this repo.

## Contributing

This spec is maintained through the reference implementation's engineering process — protocol proposals and discussion happen in [`snugprotocol/snug`](https://github.com/snugprotocol/snug) issues/discussions; every spec change lands here as a single traceable commit. Direct PRs to this repo are welcome for typos/clarity only.

## License

[MIT](LICENSE) · Security contact: security@snugprotocol.org

---
Maintainer: [Jeetu Maker](https://jeetu.tech.voyage)
