# ASIC Business Name Registration — Reference Notes

## What is a Business Name?

A business name is a name (or title) under which a person or entity conducts business in Australia. If you conduct business under a name other than your own legal name, you must register it with ASIC under the *Business Names Registration Act 2011*.

## Registration Rules

- Business names are registered nationally (since 2012, replacing state-based systems)
- A name must not be identical to an existing registered name
- A name must not be identical to a registered company name
- "Identical" includes minor variations (punctuation, spacing, plurals, "The", "Australia/n", state abbreviations, Pty/Ltd suffixes)
- ASIC considers these identical: "ABC Solutions" = "A.B.C. Solutions" = "ABC Solutions Pty Ltd"

## Identical Name Rules (ASIC criteria)

Two names are considered identical if the only differences are:
- Punctuation or spacing (e.g., hyphens, full stops, apostrophes)
- Adding or removing "The"
- Adding or removing "Australia" / "Australian"
- Adding or removing state/territory names or abbreviations
- Adding or removing "Pty", "Ltd", "Limited", "Proprietary"
- Adding or removing "and" / "&"
- Plural vs singular

## Restricted Words

See the SKILL.md for the full list. Key restrictions:
- **Anzac** — requires Minister for Veterans' Affairs approval
- **Bank/Banking/Building Society/Credit Union** — APRA-licensed ADIs only
- **Royal** — requires approval
- **Trust/Trustee** — may imply trust company
- **University/College** — registered education providers only

## The data.gov.au Dataset

- URL: https://data.gov.au/data/dataset/asic-business-names
- Updated weekly (every Wednesday)
- Format: CSV
- Fields: BN_NAME, BN_STATUS, BN_REG_DT, BN_CANCEL_DT, BN_RENEW_DT, BN_STATE_NUM, BN_STATE_OF_REG, BN_ABN
- BN_STATUS values: "Registered", "Cancelled", "Deregistered"
- File size: ~100-150MB
- The download URL includes a date suffix that changes monthly

## Checking Availability

1. Download the CSV from data.gov.au
2. Search for exact match (case-insensitive) on BN_NAME
3. If exact match found and status is "Registered" — name is taken
4. If exact match found but status is "Cancelled" or "Deregistered" — name may be available for re-registration
5. If no exact match — name is likely available (but still check ASIC identical name rules above)
6. Also flag close matches where the candidate name is a substring of a registered name or vice versa
