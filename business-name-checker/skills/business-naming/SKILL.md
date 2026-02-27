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

Before running any check scripts, ensure dependencies are installed:

```bash
pip install requests beautifulsoup4 python-whois 2>/dev/null || pip3 install requests beautifulsoup4 python-whois 2>/dev/null
```

If any check fails with a network/connection error, tell the user clearly which service failed and suggest they check their internet connection or try again later.

### Check 1: ASIC Business Names Register

**Approach:** Download and search the official ASIC Business Names dataset from data.gov.au. This is a weekly-updated CSV containing all registered Australian business names with their status.

**Why this approach:** The ASIC ConnectOnline web interface uses JSF (JavaServer Faces) with ViewState tokens and potential CAPTCHAs, making it unreliable for programmatic access. The data.gov.au CSV is the official open dataset and is much more reliable.

**Requires network access to:** `data.gov.au`

**Python script pattern:**

```python
import csv
import io
import os
import requests

def check_asic_names(names_to_check):
    """Check business names against the ASIC Business Names Register (data.gov.au)."""

    # Download the dataset (or use cached version)
    cache_path = "/tmp/asic_business_names.csv"
    dataset_url = "https://data.gov.au/data/dataset/bc515135-4bb6-4d50-957a-3713709a76d3/resource/55ad4b1c-5eeb-44ea-8b29-d410da431be3/download/business_names_202512.csv"

    if not os.path.exists(cache_path):
        print("Downloading ASIC Business Names Register (this may take a moment)...")
        try:
            resp = requests.get(dataset_url, timeout=120)
            resp.raise_for_status()
            with open(cache_path, 'wb') as f:
                f.write(resp.content)
            print("Download complete.")
        except requests.exceptions.ConnectionError:
            return {name: "Error: Network blocked. Cannot reach data.gov.au. Check internet connection." for name in names_to_check}
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
- The CSV URL changes monthly (the `202512` suffix is the date). The script should try the URL and if it 404s, fetch the dataset landing page to find the current download link.
- The CSV is large (~100MB+). Cache it at `/tmp/asic_business_names.csv` so it's only downloaded once per session.
- Search is case-insensitive exact match first, then flag close matches.

### Check 2: Domain Name Availability

**Approach:** Use the `python-whois` library for a zero-config WHOIS lookup. No API key required.

**Requires network access to:** WHOIS servers (port 43). Falls back to DNS resolution if WHOIS is unavailable.

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
            results[domain] = "Error: WHOIS connection refused."
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

**Requires network access to:** `www.etsy.com`

**Python script pattern:**

```python
import requests
import time

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
            results[name] = "Error: Network blocked. Cannot reach www.etsy.com. Check internet connection."
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

**Requires network access to:** `www.instagram.com`

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
            results[name] = "Error: Network blocked. Cannot reach www.instagram.com. Check internet connection."
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
