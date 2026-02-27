# Business Name Checker Plugin

A plugin for [Cowork](https://claude.com/product/cowork) (and Claude Code) that helps you find available Australian business names. Describe your business idea, and it generates creative name suggestions then checks if they're available across multiple platforms.

## Installation

```
# Add the marketplace first
claude plugin marketplace add caps/caps-plugins

# Add the plugin
claude plugins add business-name-checker@caps-plugins
```

## What It Does

- **Generates name ideas** — Describe your business and optionally provide keywords. The plugin generates 5-20 creative name suggestions using a mix of naming styles.
- **Checks ASIC** — Searches the official Australian Business Names Register to see if a name is already registered.
- **Checks domains** — Looks up .com, .com.au, and other TLDs to see if a matching domain is available.
- **Checks Etsy** — Sees if a matching Etsy shop name is taken (for e-commerce businesses).
- **Checks Instagram** — Sees if a matching Instagram handle is available (for social media branding).
- **Every check is optional** — You choose which platforms to check based on what matters to you.

## Commands

| Command | What It Does |
|---|---|
| `/check-business-name` | Generate business name ideas and check availability |

## Skills

| Skill | What It Covers |
|---|---|
| `business-naming` | Australian naming conventions, ASIC rules, name generation strategies, availability check patterns |

## Setup for Cowork

The plugin runs Python scripts to check external services. For these to work in Cowork, you need to allow network access to the relevant domains.

<<<<<<< Updated upstream
**Go to Settings > Capabilities > Additional allowed domains** and add whichever domains you need:
=======
- **Python 3** installed and available on your PATH
- A virtual environment is auto-created at `/tmp/namechecker-venv` on first run — no manual setup needed
>>>>>>> Stashed changes

| Check | Domain to Allow |
|---|---|
| ASIC Business Names | `data.gov.au` |
| Domain availability | _(WHOIS uses port 43 — may work without allowlist changes)_ |
| Etsy shop names | `www.etsy.com` |
| Instagram handles | `www.instagram.com` |

You only need to add the domains for the checks you plan to use. The plugin will tell you exactly which domain to add if a check fails.

## Example Workflow

```
You: /check-business-name I'm starting a sustainable packaging consultancy in Melbourne

Claude: Great — sustainable packaging consultancy in Melbourne. Any words you'd
       love to have in the name? (optional)

You: Something with "green" or "earth" would be nice

Claude: How many names should I generate?
       → 10 names (recommended)
       → 5 names
       → 20 names

You: 10

Claude: Which platforms should I check?
       → ASIC Business Names Register (recommended)
       → Domain: .com
       → Domain: .com.au
       → Etsy shop name
       → Instagram handle

You: ASIC, .com, .com.au, Instagram

Claude: Here are your 10 name suggestions:
       1. GreenWrap Consulting
       2. EarthPack Advisory
       3. Canopy Packaging Co
       ...

       Happy with these, or want me to tweak the direction?
       → Check these (recommended)
       → Regenerate
       → Add/remove names

You: Check these

Claude: Installing dependencies... done.
       Downloading ASIC register... done.
       Checking ASIC... 8 of 10 available.
       Checking domains... done.
       Checking Instagram... done.

       | Name | ASIC | .com | .com.au | Instagram |
       |---|---|---|---|---|
       | GreenWrap Consulting | Available | Available | Available | Likely available |
       | EarthPack Advisory | Available | Taken | Available | Likely available |
       | ...

       Top pick: **GreenWrap Consulting** — available everywhere!
```

## Limitations

- **ASIC data is weekly** — The register is updated every Wednesday. A name registered since the last update won't show as taken.
- **Etsy and Instagram checks are best-effort** — These platforms block automated lookups. Results may show "Uncertain" — always verify manually before committing to a name.
- **Domain WHOIS can be slow** — Each lookup takes 1-2 seconds. Checking 10 names across 3 TLDs = ~30 seconds.
- **This is not legal advice** — The plugin checks registration databases, not trademarks. Consult a business advisor before finalising your name.
