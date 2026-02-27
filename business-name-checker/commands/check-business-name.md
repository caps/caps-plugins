---
description: Generate Australian business name ideas and check availability across ASIC, domains, Etsy, and Instagram
argument-hint: "<brief business description>"
---

# Check Business Name

Generate creative business name suggestions and check their availability across multiple platforms. Each check is optional — you choose what matters.

## Usage

```
/check-business-name $ARGUMENTS
```

## Workflow

This is a multi-phase, collaborative workflow. Move through phases conversationally — keep it snappy and helpful.

---

### Phase 1: Understand the Business (2-3 questions)

**Question 1 — Business description:**
If `$ARGUMENTS` contains a business description, acknowledge it and move to Question 2. Otherwise ask:
- "What does your business do? A couple of sentences is perfect."

**Question 2 — Keywords (optional):**
- "Any words you'd love to have in the name? These are optional — I'll also generate names without them."
- Accept multiple keywords or none

**Question 3 — How many names:**
Use the Q&A tool with these options:
- **10 names** (recommended — good balance of variety and speed)
- **5 names** — quick check
- **20 names** — cast a wider net

**Do NOT ask all questions at once.** If the user provided a good description in the arguments, skip straight to Questions 2-3 together.

---

### Phase 2: Choose What to Check (1 question)

Use the Q&A tool with multi-select. Present these options:

- **ASIC Business Names Register** (recommended) — checks if the name is already registered in Australia
- **Domain: .com** — the global standard
- **Domain: .com.au** — the Australian standard
- **Domain: other TLDs** — check .au, .net, .io, .co, etc.
- **Etsy shop name** — if you're selling on Etsy
- **Instagram handle** — for social media branding

Recommend ASIC + .com + .com.au as a minimum for any Australian business.

If the user selects "other TLDs", ask which ones. Suggest: `.au`, `.net`, `.io`, `.co`, `.org`. Let them type additional ones.

---

### Phase 3: Generate Names

Using the business description, keywords, and your expertise from the **business-naming** skill:

1. Generate the requested number of names
2. Use a mix of naming styles (descriptive, abstract, compound, portmanteau, metaphor, location-flavoured)
3. If keywords were provided, incorporate them in roughly half the names — the rest should be creative alternatives
4. Avoid ASIC restricted words (see skill)
5. For each name, also note the derived domain-friendly version and Instagram handle

Present the names to the user as a numbered list:

```
Here are your 10 name suggestions:

1. GreenStone Advisory
2. Koalify Solutions
3. Harbour & Co
4. BrightPath Consulting
...
```

Ask: **"Happy with these, or want me to tweak the direction before I start checking?"**

Give options:
- **Check these** (recommended)
- **Regenerate with different style** — "too corporate", "too quirky", etc.
- **Add/remove specific names** — swap individual names in or out

If the user wants changes, regenerate and present again. Only proceed to Phase 4 when the user approves the list.

---

### Phase 4: Run Availability Checks

This is where Claude writes and executes Python scripts. Follow the patterns defined in the **business-naming** skill exactly.

**Step 1: Install dependencies (first run only)**

Write and execute:
```bash
pip install requests beautifulsoup4 python-whois 2>/dev/null || pip3 install requests beautifulsoup4 python-whois 2>/dev/null
```

**Step 2: Run each selected check**

For each check the user selected, follow the approach defined in the skill file. Run checks in this order:

1. **ASIC** (slowest — downloads ~100MB CSV on first run, fast after caching) — Python script
2. **Domains** (moderate — WHOIS lookups take 1-2 seconds each) — Python script
3. **Etsy** (fast — parallel Google searches via WebSearch tool, no HTTP delays)
4. **Instagram** (slowest per-request — 2-second delays, most likely to be blocked) — Python script

Note: Etsy uses Google search via the WebSearch tool (not a Python script). Run one WebSearch per name, all in parallel.

**Progress updates:** After each check completes, tell the user what's done:
- "ASIC check complete — 7 of 10 names are available on the register."
- "Domain checks complete — checking Etsy next..."

**Error handling:** If a check fails for all names (e.g., network blocked), report it clearly and continue with the remaining checks. Do NOT stop the entire workflow because one check failed.

---

### Phase 5: Present Results

Compile all results into a single markdown summary table. Follow the format defined in the **business-naming** skill.

**Table structure:**
- One row per business name
- One column per selected check
- Sorted by availability score (most available first)
- Use status labels: Available, Likely available, Taken, Found (Cancelled), Error, —

**After the table, provide:**

1. **Top picks** — highlight the top 1-3 names with best overall availability
2. **Near misses** — names that are available on most platforms but taken on one (with suggestions for variations)
3. **Watch outs** — any names with ASIC "similar names" warnings or uncertain results

Example output:

```
## Results

| Name | ASIC | .com | .com.au | Etsy | Instagram |
|---|---|---|---|---|---|
| GreenStone Advisory | Available | Available | Available | Likely available | Likely available (@greenstoneadvisory) |
| Koalify | Available | Taken | Available | Likely available | Taken (@koalify) |
| Harbour & Co | Available (3 similar) | Taken | Taken | — | — |

### Top Picks

1. **GreenStone Advisory** — available across all platforms checked. Strong, professional name.
2. ...

### Near Misses

- **Koalify** — available on ASIC and .com.au but taken on .com and Instagram. Consider "koalify.au" for the domain and "@koalify_au" for Instagram.

### Watch Outs

- **Harbour & Co** — available on ASIC but 3 similar names exist (Harbour Co, Harbour and Company, The Harbour Co). Check these won't cause confusion.
```

---

### Phase 6: Iterate (optional)

After presenting results, ask:

**"Want to explore more names, or are you happy with these results?"**

Options:
- **Generate more names** — "I'll create another batch and check them too"
- **Refine direction** — "Let me know what style worked and I'll lean into that"
- **Check specific names** — "Got a name in mind? I'll check just that one"
- **I'm done** — wrap up

If the user wants more names:
- Ask for any new keywords or direction changes
- Generate a new batch
- Run the same checks
- Present new results alongside previous results so the user can compare

---

## Key Principles Throughout

- **Plain language.** This plugin is for non-technical users. No jargon, no error codes without explanation.
- **Always recommend.** When presenting options, include a recommendation with brief rationale.
- **Progress updates.** The checks take time — keep the user informed as each step completes.
- **Graceful failures.** If a check fails, explain why in simple terms and suggest a fix. Never let one failure stop the whole workflow.
- **Honest about limitations.** Etsy and Instagram checks are best-effort. WHOIS can be slow. Say so upfront rather than over-promising accuracy.
