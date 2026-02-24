# claude-plugins-by-caps

A personal plugin marketplace for [Claude Cowork](https://claude.com/blog/cowork-plugins) and [Claude Code](https://code.claude.com/docs/en/plugins).

## Installation

Add this marketplace, then install any plugin from it:

```bash
# Add the marketplace
claude plugin marketplace add caps/claude-plugins-by-caps

# Install a plugin
claude plugins add board-paper-writer@caps-plugins
```

## Available Plugins

| Plugin | Description |
|---|---|
| [board-paper-writer](board-paper-writer/) | Draft governance-grade board papers through collaborative Q&A and iterative refinement. Covers decision, discussion, noting, and assurance papers — structured around AICD, GGI, and Board Intelligence QDI best practices. |

## What Are Claude Plugins?

Plugins bundle skills, commands, and agents into reusable packages that extend how Claude works. They're primarily markdown — no code required. See the official docs:

- [Create plugins](https://code.claude.com/docs/en/plugins) — how plugins work and how to build them
- [Discover and install plugins](https://code.claude.com/docs/en/discover-plugins) — browse and install from marketplaces
- [Plugin marketplaces](https://code.claude.com/docs/en/plugin-marketplaces) — create and distribute your own
- [Plugins reference](https://code.claude.com/docs/en/plugins-reference) — full technical specification
- [anthropics/knowledge-work-plugins](https://github.com/anthropics/knowledge-work-plugins) — Anthropic's official open-source plugins

## License

MIT
