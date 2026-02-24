---
description: Draft a board paper collaboratively through structured Q&A and iterative markdown refinement
argument-hint: "<topic or brief description>"
---

# Write Board Paper

> If you see unfamiliar placeholders or need to check which tools are connected, see [CONNECTORS.md](../CONNECTORS.md).

Draft a board paper through a collaborative, iterative process. The output is a governance-grade paper structured around AICD best practices, written in active voice with clear recommendations and honest risk assessment.

## Usage

```
/write-board-paper $ARGUMENTS
```

## Workflow

This is a multi-phase, collaborative workflow. Each phase produces a visible markdown artifact that the user can review. Move through phases quickly — the goal is rapid iteration, not perfection on the first pass.

---

### Phase 1: Scoping (2-3 questions max)

Start by understanding the paper's purpose. Use the Q&A tool to present options with a recommended default where you have enough context to form a view.

**Question 1 — Engagement type:**
Present these options and recommend one based on any context provided:
- **For Decision / Approval** — The board will vote on a resolution
- **For Discussion / Endorsement** — Management seeks the board's input or strategic steer
- **For Noting / Information** — Keeping the board informed, no action required
- **For Assurance** — Providing evidence that governance or compliance obligations are being met

**Question 2 — Topic and context:**
If the user hasn't already provided the topic, ask:
- What is the subject of this paper?
- In 1-2 sentences, what's the core message or ask?

**Question 3 — Audience calibration:**
Present options for the board's familiarity with this topic:
- **New topic** — Board has not seen this before, needs full context
- **Ongoing matter** — Board has prior papers on this, needs an update
- **Returning for decision** — Previously discussed, now needs approval

If connected tools are available, search for prior board papers on the topic to inform this.

**Do NOT ask all questions at once.** Ask Question 1, then based on the answer, ask Questions 2-3 together. Be conversational.

---

### Phase 2: Strategic Framing (1-2 questions)

Based on the engagement type, ask targeted questions. Again, use the Q&A tool and always include a recommendation.

**For Decision papers, ask:**
- What is the specific decision the board is being asked to make? (open text)
- Present options for decision complexity:
  - **Binary** — Approve or reject a single proposal
  - **Multi-option** — Choose between 2-3 alternatives
  - **Staged** — Approve in principle, with conditions or a phased approach

**For Discussion papers, ask:**
- What specific input does management want from the board? (open text)
- Present options for discussion framing:
  - **Strategic direction** — "Which way should we go?"
  - **Risk appetite** — "How much risk are we comfortable with?"
  - **Prioritisation** — "What should we focus on first?"
  - **Stakeholder perspective** — "What are we missing?"

**For Noting papers, ask:**
- What is the key takeaway the board should retain? (open text)
- Is there a future decision this is building towards? (yes/no)

**For Assurance papers, ask:**
- Which governance obligation or framework does this relate to? (open text)
- Present options for assurance level:
  - **Full assurance** — All controls effective, no material gaps
  - **Partial assurance** — Generally effective, some areas need attention
  - **Limited assurance** — Significant gaps requiring board awareness

---

### Phase 3: Content Gathering (rapid-fire)

Gather the substance of the paper. This should feel like a structured interview, not a form. Ask 3-5 questions maximum, tailored to the paper type. Use the Q&A tool for bounded choices, open text for substance.

**Always ask:**
- What are the key facts or data points that support this paper?
- What are the main risks? (and the risk of NOT acting?)
- What is the financial impact? (ballpark figures are fine — we'll refine)

**For Decision papers, also ask:**
- What alternatives were considered?
- Who has reviewed this internally? (executive team, legal, finance?)
- What approvals are needed after the board? (regulator, JV partner, etc.)

**For Discussion papers, also ask:**
- What does management's current thinking look like?
- Where are the genuine uncertainties or disagreements?

**Pull context from connected tools:**
If **~~knowledge base** (Confluence/Notion) is connected, search for prior board papers and strategy documents on this topic.
If **~~cloud storage** (Google Drive) is connected, search for financial models, supporting analyses, or prior presentations.
Do not ask the user to connect tools — work with what you have.

---

### Phase 4: First Draft (markdown artifact)

Generate the first draft as a **markdown artifact**. Follow the structure defined in the **board-writing** skill. This draft should be:
- Complete in structure (all sections present, even if thin)
- Written in active voice throughout
- Honest about risks and gaps
- 3-5 pages equivalent in length (main body, excluding appendices)

**Visual placeholders:** As you draft, identify sections that would benefit from visual elements (dashboards, charts, risk matrices, financial tables, process diagrams). Insert placeholders using this format:

```
> 📊 **[VISUAL PLACEHOLDER]** — {Description of what visual would go here}
> *Suggested format: {chart type / table / dashboard / diagram}*
> *Data needed: {what data would populate this}*
```

After presenting the draft, ask the user:
- "Here's the first draft. What jumps out as needing work?"
- "I've flagged X sections where a visual would strengthen the paper — do you have any existing charts, dashboards, or data you'd like to use?"

---

### Phase 5: Iteration (as many rounds as needed)

This is where the paper gets sharp. Each round should:

1. **Present specific improvement options using Q&A.** For example:
   - "The recommendation section could go two ways — which feels right?"
     - Option A: Lead with the financial case (recommended for this audience)
     - Option B: Lead with the strategic alignment
     - Option C: Lead with the risk mitigation

2. **Make the changes and regenerate the full artifact** so the user always sees the complete current state.

3. **Proactively flag issues.** Don't wait to be asked. Examples:
   - "The risk section is light — I'd suggest adding the risk of inaction"
   - "This executive summary is 8 lines — it needs to be 4-5 max"
   - "The recommendation is hedged — for a decision paper, be more direct"
   - "This section uses passive voice — let me tighten it"

4. **After each iteration, ask one focused question** about the next most important improvement. Don't ask "is this OK?" — ask "should we strengthen the financial case or tighten the risk section next?"

**Keep iterating until the user signals they're happy.** Typical board papers take 2-4 iterations.

---

### Phase 6: Polish and Visual Integration

Once the content is stable:

1. **Run a final quality check** against the checklist in the board-writing skill. Report the results conversationally:
   - "Active voice: ✓ throughout"
   - "Executive summary: ✓ 4 lines, stands alone"
   - "Risk section: ⚠ could be stronger on the 'risk of inaction'"
   - "Length: ✓ 4 pages equivalent"

2. **Revisit visual placeholders.** Ask the user:
   - "We have X visual placeholders. Do you have existing charts or data for any of these, or should I leave them as placeholders for you to fill in?"
   - If the user provides data, generate markdown tables or describe the visual. If not, keep the placeholders clearly marked.

3. **Generate a final polished markdown artifact** with all changes incorporated.

---

### Phase 7: Export (only when user is ready)

Ask the user: **"Would you like to export this to a branded Word template?"**

Present options:
- **Upload a template** — "Upload your organisation's .docx template and I'll flow the content into it"
- **Standard format** — "I'll create a clean .docx with the standard board paper structure"
- **Keep as markdown** — "Keep the markdown artifact as-is for now"
- **Publish to Confluence** — "Create a Confluence page with this content" (if Atlassian is connected)

If the user uploads a branded template:
- Map board paper sections to the template's structure
- Preserve the template's formatting, headers, footers, and branding
- Insert the content using the docx skill
- Flag any sections that don't map cleanly to the template

If no template, generate a clean professional .docx using standard board paper formatting.

---

## Key Principles Throughout

- **Always recommend.** When presenting options, include "recommended" on the option you'd pick and briefly say why. Directors value a clear point of view from management.
- **Active voice by default.** If you catch yourself writing passive voice, rewrite immediately. "Management recommends..." not "It is recommended that..."
- **Lead with the "so what?"** Every section should answer why the board should care.
- **Be honest about gaps.** If information is missing, flag it explicitly rather than papering over it.
- **Keep it scannable.** Headers, bold key points, short paragraphs. A director should be able to read just the headers and bold text and grasp the paper.
- **Iterate fast.** Quick cycles with visible artifacts beats one long drafting session.

## Output Format

All drafts as markdown artifacts. Final export as .docx (with or without branded template) or Confluence page. Visual placeholders use the 📊 format defined above.
