---
name: business-naming
description: Generate creative Australian business names and check availability across ASIC registers, domain names, Etsy, and Instagram. Use this skill whenever helping someone brainstorm, validate, or research business name availability in Australia.
---

# Business Naming Skill

You are an expert in Australian business naming — creative name generation, ASIC registration rules, domain strategy, and brand consistency across platforms. You help users go from a rough business idea to a shortlist of available names.

## Name Generation Guidelines

### What Makes a Good Australian Business Name
- **Memorable and pronounceable** — easy to say over the phone, easy to spell
- **Not too long** — ASIC allows up to 200 characters but shorter is better (2-4 words ideal)
- **Domain-friendly** — works as a .com.au or .com without awkward hyphens
- **Distinctive** — avoids generic terms that are hard to protect or differentiate
- **Culturally appropriate** — consider Australian English spelling and cultural context

### Name Styles to Generate (vary across these)
- **Descriptive** — says what the business does (e.g., "Outback Solar Solutions")
- **Abstract/Invented** — made-up words that sound good (e.g., "Koalify", "Brisca")
- **Compound** — two real words combined (e.g., "GreenStone", "BrightPath")
- **Portmanteau** — blends of words (e.g., "Instravel" from inspire + travel)
- **Metaphor** — evokes a quality (e.g., "Anchor Advisory", "Summit Legal")
- **Acronym-based** — if the full name is long, ensure the acronym works
- **Location-flavoured** — Australian place names or slang (e.g., "Harbour", "Billabong", "Red Centre")

### ASIC Restricted Words
These words require special approval or are restricted in business names:
- "Anzac" — prohibited without permission from the Minister for Veterans' Affairs
- "Bank", "Banking", "Building Society", "Credit Union" — restricted to licensed ADIs
- "Royal" — requires approval
- "Trust", "Trustee" — may imply trust company status
- "University", "College" — restricted to registered education providers
- "Chamber of Commerce", "Chartered" — restricted usage

Avoid these in generated names unless the user's business genuinely qualifies.

### Generating Names
When generating names:
1. Read the user's business description carefully — understand the industry, values, and tone
2. If keywords are provided, incorporate them naturally (not forced)
3. Generate a mix of styles — at least 3 different styles per batch
4. For each name, mentally check: can I make a domain from this? An Instagram handle?
5. Avoid names that are too similar to well-known Australian brands (Woolworths, Telstra, Qantas, etc.)

---

## Availability Checking

### Important: First-Run Setup

Before running any check scripts, create a virtual environment and install dependencies:

```bash
python3 -m venv /tmp/namechecker-venv 2>/dev/null; /tmp/namechecker-venv/bin/pip install requests beautifulsoup4 python-whois 2>&1 | tail -1
```

<<<<<<< Updated upstream
If any check fails with a network/connection error, tell the user:

> "It looks like network access to [domain] is blocked. In Cowork, go to **Settings > Capabilities > Additional allowed domains** and add `[domain]`. Then try again."
=======
Then verify the installation:
```bash
/tmp/namechecker-venv/bin/python3 -c "import requests, whois; print('OK')"
```

All Python scripts below must be run with `/tmp/namechecker-venv/bin/python3` instead of `python3`.

**Do NOT launch parallel checks until dep installation has completed and been verified. The dep install must be its own sequential step before any parallel work.**

If any check fails with a network/connection error, tell the user clearly which service failed and suggest they check their internet connection or try again later.
>>>>>>> Stashed changes

### Check 1: ASIC Business Names Register

**Approach:** Download and search the official ASIC Business Names dataset from data.gov.au. This is a weekly-updated CSV containing all registered Australian business names with their status.

**Why this approach:** The ASIC ConnectOnline web interface uses JSF (JavaServer Faces) with ViewState tokens and potential CAPTCHAs, making it unreliable for programmatic access. The data.gov.au CSV is the official open dataset and is much more reliable.

**Network allowlist required:** `data.gov.au`

**Python script pattern:**

```python
import csv
import datetime
import io
import os
import requests

def check_asic_names(names_to_check):
    """Check business names against the ASIC Business Names Register (data.gov.au)."""

    # Download the dataset (or use cached version)
    cache_path = "/tmp/asic_business_names.csv"
    base_url = "https://data.gov.au/data/dataset/bc515135-4bb6-4d50-957a-3713709a76d3/resource/55ad4b1c-5eeb-44ea-8b29-d410da431be3/download/business_names_{}.csv"

    # Dynamically compute month suffixes to try (current, previous, month before)
    today = datetime.date.today()
    months_to_try = []
    for offset in range(3):
        d = today.replace(day=1) - datetime.timedelta(days=offset * 28)
        months_to_try.append(d.strftime('%Y%m'))

    if not os.path.exists(cache_path):
        print("Downloading ASIC Business Names Register (this may take a moment)...")
        try:
            resp = None
            for month in months_to_try:
                url = base_url.format(month)
                resp = requests.get(url, timeout=120)
                if resp.status_code == 200:
                    print(f"Found dataset for {month}.")
                    break
                print(f"No dataset for {month}, trying earlier month...")
            resp.raise_for_status()
            with open(cache_path, 'wb') as f:
                f.write(resp.content)
            print("Download complete.")
        except requests.exceptions.ConnectionError:
            return {name: "Error: Network blocked. Add data.gov.au to Cowork allowlist." for name in names_to_check}
        except Exception as e:
            return {name: f"Error: {e}" for name in names_to_check}

    # Load and search
    print("Searching ASIC register...")
    results = {}
    names_upper = {name: name.upper().strip() for name in names_to_check}

    try:
        with open(cache_path, 'r', encoding='utf-8', errors='replace') as f:
            reader = csv.DictReader(f)
            registered = {}
            for row in reader:
                bn = row.get('BN_NAME', '').upper().strip()
                status = row.get('BN_STATUS', '').strip()
                if bn:
                    registered[bn] = status

        for name, name_up in names_upper.items():
            if name_up in registered:
                status = registered[name_up]
                if status.upper() in ('REGISTERED', 'REGISTRATION'):
                    results[name] = "Taken (Registered)"
                else:
                    results[name] = f"Found ({status})"
            else:
                # Also check for close matches (contains)
                close = [k for k in registered if name_up in k or k in name_up]
                if close:
                    results[name] = f"Available (but {len(close)} similar names exist)"
                else:
                    results[name] = "Available"
    except Exception as e:
        results = {name: f"Error: {e}" for name in names_to_check}

    return results

# Usage:
names = ["EXAMPLE NAME ONE", "EXAMPLE NAME TWO"]
results = check_asic_names(names)
for name, status in results.items():
    print(f"  {name}: {status}")
```

**Important notes:**
- The CSV URL changes monthly. The script dynamically computes the current month suffix and tries the current month, previous month, and the month before that, so it stays up to date automatically.
- The CSV is large (~100MB+). Cache it at `/tmp/asic_business_names.csv` so it's only downloaded once per session.
- Search is case-insensitive exact match first, then flag close matches.

### Check 2: Domain Name Availability

**Approach:** Use the `python-whois` library for a zero-config WHOIS lookup. No API key required.

**Network allowlist required:** WHOIS servers vary by TLD but typically use port 43. If WHOIS is blocked, fall back to DNS resolution as a rough check.

**Python script pattern:**

```python
import whois
import socket

def check_domains(base_name, tlds):
    """Check domain availability for a business name across specified TLDs."""
    # Convert name to domain-friendly format
    domain_base = base_name.lower().replace(' ', '').replace("'", '').replace('&', 'and')
    domain_base = ''.join(c for c in domain_base if c.isalnum())

    results = {}
    for tld in tlds:
        domain = f"{domain_base}.{tld}"
        try:
            w = whois.whois(domain)
            if w.domain_name:
                results[domain] = "Taken"
            else:
                results[domain] = "Available"
        except whois.parser.PywhoisError:
            results[domain] = "Available"
        except socket.timeout:
            results[domain] = "Error: Timeout"
        except ConnectionRefusedError:
            results[domain] = "Error: WHOIS blocked. Try adding whois servers to allowlist."
        except Exception as e:
            # Fallback: try DNS resolution
            try:
                socket.getaddrinfo(domain, 80)
                results[domain] = "Likely taken (DNS resolves)"
            except socket.gaierror:
                results[domain] = "Likely available (no DNS)"
            except Exception:
                results[domain] = f"Error: {e}"

    return results

# Usage:
results = check_domains("Example Business", ["com", "com.au", "au", "net", "io"])
for domain, status in results.items():
    print(f"  {domain}: {status}")
```

### Check 3: Etsy Shop Name Availability

**Approach:** HTTP GET to the public Etsy shop URL. A 404 response indicates the shop name is not taken. Etsy uses aggressive bot detection (DataDome), so this check is best-effort.

**Network allowlist required:** `www.etsy.com`

**Python script pattern:**

<<<<<<< Updated upstream
```python
import requests
import time
=======
1. Convert the business name to a shop handle: lowercase, remove spaces and punctuation, replace `&` with `and`, keep only alphanumeric characters
2. Use the WebSearch tool to search: `Etsy shop "[shophandle]"`
3. Analyse the results:
   - If an exact Etsy shop URL appears (e.g., `etsy.com/shop/trovejewellery` or `etsy.com/au/shop/trovejewellery`) — **Taken**
   - If no exact match but a very similar shop name appears (e.g., searching "gildedthread" finds "Gildedthreadsshop") — **Likely available (but similar shop "[name]" exists)**
   - If no Etsy shop URL matches or is similar — **Likely available**
>>>>>>> Stashed changes

def check_etsy_names(names):
    """Check Etsy shop name availability via public URL."""
    results = {}
    headers = {
        'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36'
    }

    for name in names:
        shop_name = name.lower().replace(' ', '').replace("'", '').replace('&', 'and')
        shop_name = ''.join(c for c in shop_name if c.isalnum())
        url = f"https://www.etsy.com/shop/{shop_name}"

        try:
            resp = requests.get(url, headers=headers, timeout=10, allow_redirects=True)
            if resp.status_code == 404:
                results[name] = "Likely available"
            elif resp.status_code == 200:
                results[name] = f"Taken ({url})"
            elif resp.status_code == 403:
                results[name] = "Error: Blocked by Etsy bot detection"
            else:
                results[name] = f"Uncertain (HTTP {resp.status_code})"
        except requests.exceptions.ConnectionError:
            results[name] = "Error: Network blocked. Add www.etsy.com to Cowork allowlist."
        except Exception as e:
            results[name] = f"Error: {e}"

        time.sleep(1)  # Rate limit: 1 request per second

    return results

# Usage:
results = check_etsy_names(["Example Business", "Another Name"])
for name, status in results.items():
    print(f"  {name}: {status}")
```

### Check 4: Instagram Handle Availability

**Approach:** HTTP GET to the public Instagram profile URL. Instagram aggressively blocks automated requests — the `requests` library's TLS fingerprint is often detected and blocked. This check is best-effort and may return "Uncertain" frequently.

**Network allowlist required:** `www.instagram.com`

**Python script pattern:**

```python
import requests
import time

def check_instagram_handles(names):
    """Check Instagram handle availability via public profile URL. Best-effort."""
    results = {}
    headers = {
        'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36',
        'Accept': 'text/html,application/xhtml+xml',
        'Accept-Language': 'en-AU,en;q=0.9',
    }

    for name in names:
        handle = name.lower().replace(' ', '').replace("'", '').replace('&', 'and')
        handle = ''.join(c for c in handle if c.isalnum() or c == '_')
        handle = handle[:30]  # Instagram max handle length
        url = f"https://www.instagram.com/{handle}/"

        try:
            resp = requests.get(url, headers=headers, timeout=10, allow_redirects=True)
            if resp.status_code == 404:
                results[name] = f"Likely available (@{handle})"
            elif resp.status_code == 200:
                if '"user":null' in resp.text or 'page isn' in resp.text.lower():
                    results[name] = f"Likely available (@{handle})"
                else:
                    results[name] = f"Taken (@{handle})"
            elif resp.status_code in (301, 302):
                results[name] = f"Taken (@{handle})"
            elif resp.status_code == 429:
                results[name] = "Error: Rate limited. Try again later."
                break  # Stop checking further names
            else:
                results[name] = f"Uncertain (HTTP {resp.status_code}, @{handle})"
        except requests.exceptions.ConnectionError:
            results[name] = "Error: Network blocked. Add www.instagram.com to Cowork allowlist."
            break
        except Exception as e:
            results[name] = f"Error: {e}"

        time.sleep(2)  # Rate limit: 1 request per 2 seconds (Instagram is aggressive)

    return results

# Usage:
results = check_instagram_handles(["Example Business", "Another Name"])
for name, status in results.items():
    print(f"  {name}: {status}")
```

---

## Presenting Results

Always present results as a markdown table. Sort names by availability score (most available first).

### Table Format

```
| Business Name | ASIC | .com | .com.au | .io | Etsy | Instagram |
|---|---|---|---|---|---|---|
| Green Stone Co | Available | Available | Taken | Available | Likely available | Taken (@greenstoneco) |
| BrightPath | Taken (Registered) | Taken | Taken | Available | Taken | Taken (@brightpath) |
```

### Status Labels
- **Available** — confirmed not registered/taken
- **Likely available** — best-effort check suggests not taken, but verify manually
<<<<<<< Updated upstream
=======
- **Likely available (but similar shop "[name]" exists)** — Etsy handle not taken, but a similar shop name was found in search results
- **Probably taken** — the name is a word-subset of an existing registered name (e.g., "Trove Jewellery" vs "Trove Jewellery Studio"). ASIC will likely reject this.
>>>>>>> Stashed changes
- **Taken** — confirmed registered or in use
- **Found (Cancelled)** — was registered with ASIC but cancelled (may be reusable)
- **Error: [reason]** — check failed, explain why and how to fix
- **—** — not checked (user didn't select this platform)

### Scoring
Count "Available" and "Likely available" as points. Sort by highest score first.

### Recommendation
After the table, write a brief recommendation:
- Highlight the top 1-3 names with the best availability
- Note any names that are available on ASIC but taken on key digital platforms (or vice versa)
- If a name is available everywhere except one platform, suggest a handle variation (e.g., "greenstoneco_au" for Instagram)
