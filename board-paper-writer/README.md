# Board Paper Writer Plugin

A governance writing plugin primarily designed for [Cowork](https://claude.com/product/cowork), Anthropic's agentic desktop application — though it also works in Claude Code. Guides you through drafting board papers collaboratively, using structured Q&A, iterative markdown artifacts, and governance best practices from AICD, the Governance Institute of Australia, and the Board Intelligence QDI methodology.

## Installation

```
# Add the marketplace first
claude plugin marketplace add caps/caps-plugins

# Add the plugin
claude plugins add board-paper-writer@caps-plugins
```

## What It Does

This plugin gives you an AI-powered governance writing partner that:

- **Scopes the paper collaboratively** — Uses structured Q&A to determine paper type (decision, discussion, noting, assurance), audience familiarity, and strategic framing before writing a single word.
- **Drafts iteratively in markdown** — Produces visible artifacts at each stage so you can review, redirect, and refine quickly. Typical papers take 2-4 iterations.
- **Recommends, doesn't just ask** — When presenting options, always includes a recommended choice with rationale. Mirrors the decisiveness expected in board writing.
- **Flags visual opportunities** — Identifies sections where charts, dashboards, or diagrams would strengthen the paper and inserts structured placeholders for you to fill.
- **Exports to branded templates** — When the paper is ready, exports to your organisation's .docx template, a clean standard format, or directly to Confluence.

## Commands

| Command | What It Does |
|---|---|
| `/write-board-paper` | Draft a board paper from scratch through collaborative Q&A and iterative refinement |

## Skills

| Skill | What It Covers |
|---|---|
| `board-writing` | Paper types, AICD structure, active voice rules, QDI methodology, risk analysis, executive summaries, quality checklist, anti-patterns |

## Example Workflow

```
You: /write-board-paper
Claude: What type of board paper are we writing?
       → For Decision (recommended if you need the board to approve something)
       → For Discussion
       → For Noting
       → For Assurance

You: For Decision

Claude: Got it — a decision paper. What's the topic and core ask?

You: We need board approval to migrate our cloud infrastructure from AWS to GCP.
     Budget is $2.4M over 18 months.

Claude: [Asks about alternatives considered, risks, who's reviewed internally]

You: [Provides context]

Claude: [Generates first draft as markdown artifact with visual placeholders]
        "Here's the first draft. The risk section could be stronger —
         should we add the risk of staying on AWS, or focus on migration risks?"
       → Add risk of inaction (recommended)
       → Focus on migration risks only
       → Cover both equally

You: Both equally

Claude: [Regenerates full artifact with strengthened risk section]
        "I've flagged 3 spots for visuals — a cost comparison chart,
         a migration timeline, and a risk matrix. Do you have any
         existing data for these?"

You: I have a cost model spreadsheet

Claude: [Incorporates cost data, continues iterating]

... 2-3 more rounds ...

Claude: "The paper passes the quality checklist. Ready to export?"
       → Upload a branded .docx template
       → Standard format .docx
       → Keep as markdown
       → Publish to Confluence
```

## Governance Standards

This plugin is grounded in:

- **AICD (Australian Institute of Company Directors)** — Annotated board paper template, director tools, reporting standards
- **Governance Institute of Australia** — Board paper guidance and governance frameworks
- **Board Intelligence QDI Principle** — Question-Driven Insight methodology for critical thinking and clear communication
- **Good Governance Institute (GGI)** — Plain English rules, paper length guidance, paper types
- **Clare Chalmers** — Six steps to excellent board papers, NED-focused positioning

## Key Writing Principles

1. **Active voice always.** "Management recommends..." not "It is recommended that..."
2. **Inverted pyramid.** Most important information first. Resolution on page one.
3. **3-5 pages max.** Main body only. Everything else in appendices.
4. **Executive summary stands alone.** 4-5 lines. A director who reads nothing else gets the message.
5. **Honest risk assessment.** Include the risk of inaction. No whitewashing.
6. **The "so what?" test.** Every section answers why the board should care.

## Customisation

- **Add your organisation's context** — Drop your strategic plan, risk framework, or governance charter into the skill references for more targeted drafting
- **Branded templates** — Upload your .docx template at export time, or add it to the plugin's assets folder for automatic use
- **Custom sections** — Modify the SKILL.md to add organisation-specific sections (e.g., modern slavery, environmental reporting, Indigenous consultation)
