# Attestation — Workday ATS Connector

Human signer: _____________  Date: _____________

## Runs performed

| Ran | Saw | Expected |
|---|---|---|
| `--company "Wayfair" --tenant "wayfair"` (original path-based URL spec) | Connection failures; wd3.myworkdayjobs.com resolves to 127.0.0.1 | JUDGMENT: does this count as the connector working as specified, or as the spec being wrong? |
| DNS sweep of wd1/wd3/wd5 + tenant-subdomain forms | wayfair.wd1 and wayfair.wd5 resolve to real IPs; bare wdN hosts don't | JUDGMENT: |
| `--company "City of Aurora" --careers-url "https://auroragov.wd1.myworkdayjobs.com/Careers"` | 39 postings found, extraction_status success, 0 validation errors | JUDGMENT: is 39 real postings sufficient evidence the connector works, or is one tenant too few? |
| Live fetch of 2 generated source_urls | Both HTTP 200, ~31-33KB | JUDGMENT: |
| `--careers-url` pointing at unknown tenant (fakecorpxyz123) | Initially misclassified as `errors` (HTTP 422); reclassified to `not_found` after live confirmation on 2 bogus tenants | JUDGMENT: is two confirmations enough to trust 422 always means "unknown tenant" on every Workday deployment, or could 422 mean something else on a different tenant? |
| `--careers-url` pointing at non-Workday URL | invalid_careers_url, zero HTTP requests confirmed | JUDGMENT: |
| pytest test_scraper.py | 94/94 passed | JUDGMENT: |
| node scripts/conformance.mjs | clean | JUDGMENT: |
| npm run doctor | exit 0, but privacy check didn't see real scrape data (zip output never copied into clone) | JUDGMENT: does an exit-0 doctor run count as a cleared privacy gate here, given that caveat? |

## Did not test

- No tenant other than City of Aurora was scraped live. The 422-means-unknown-tenant
  finding rests on 2 bogus subdomains against 1 real pod (wd1) — not verified across
  wd3, wd5, wd12, wd108, or any tenant with a non-English career site name.
- bulletFields structure was observed on exactly one tenant. Whether [req_id, location]
  ordering holds elsewhere is unknown and undecoded by design.
- No tenant with populated jobPostingId, locationsText, jobFamilyGroup, or
  jobScheduleType was found during this session — every field-mapping line for those
  four fields is untested against a payload that actually contains them.
- The doctor.mjs frontmatter-parsing bug (logged in RUN_LOG) was not fixed; its
  downstream effects on other tooling that reads recipe status were not investigated.

## Judgment

JUDGMENT: [Your own assessment here — does this evidence support RUNNABLE-LIVE?
What would you want to see before calling it VERIFIED? Sign only what you've
actually reviewed.]
