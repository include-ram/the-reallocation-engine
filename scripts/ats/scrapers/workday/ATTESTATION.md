# Attestation — Workday ATS Connector

Human signer: _____Sriram________  Date: ____08/13/2026_________

## Runs performed
| Ran | Saw | Judgment |
|---|---|---|
| `--company "Wayfair" --tenant "wayfair"` (original path-based URL spec) | Connection failures; wd3.myworkdayjobs.com resolves to 127.0.0.1 | The original spec was wrong, not the connector — confirmed by DNS sweep and a failure that reproduced independent of the sandbox. Fixing it required a real architectural change (auto-discovery from a careers URL), which I reviewed and approved. |
| DNS sweep of wd1/wd3/wd5 + tenant-subdomain forms | wayfair.wd1 and wayfair.wd5 resolve to real IPs; bare wdN hosts don't | Confirms Workday's real addressing scheme is tenant-subdomain-based, not path-based as originally assumed. This is now correctly implemented in the connector. |
| `--company "City of Aurora" --careers-url "https://auroragov.wd1.myworkdayjobs.com/Careers"` | 39 postings found, extraction_status success, 0 validation errors | One tenant is enough to prove the mechanism works end-to-end, but not enough to claim it works universally. I'd want 2-3 more tenants on different pods (wd3, wd5, wd12) before calling this VERIFIED. |
| Live fetch of 2 generated source_urls | Both HTTP 200, ~31-33KB | Confirms the URL construction is correct, at least for this tenant's externalPath format. Not yet confirmed on a tenant with a different externalPath shape. |
| `--careers-url` pointing at unknown tenant (fakecorpxyz123) | Initially misclassified as `errors` (HTTP 422); reclassified to `not_found` after live confirmation on 2 bogus tenants | Confirmed on 2 bogus tenants against 1 pod (wd1) only. I'm treating this as a strong signal, not a certainty — a different pod or a future Workday API change could return a different code for the same condition. Accepting this as a known limitation for RUNNABLE-LIVE, not VERIFIED. |
| `--careers-url` pointing at non-Workday URL | invalid_careers_url, zero HTTP requests confirmed | Straightforward and well-tested — this is the gate I'm most confident in. |
| pytest test_scraper.py | 94/94 passed | These tests are a 1:1 port of manual smoke tests, not independently designed test cases. They confirm the code does what I already observed by hand, not that testing itself surfaced new edge cases. |
| node scripts/conformance.mjs | clean | Confirms formatting/structural adherence only — says nothing about whether the connector's logic is correct. |
| npm run doctor | exit 0, but privacy check didn't see real scrape data (zip output never copied into clone) | I'm not treating this as a real privacy-gate pass. The real scrape output was never present in the checked directory, so the check didn't actually exercise the thing it's supposed to catch. This needs re-verification with real data/ats/workday/ contents present before I'd trust it. |

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
This evidence supports **RUNNABLE-LIVE**, not VERIFIED. The connector works
end-to-end against a real tenant, the failure buckets behave correctly under
deliberate breaking, and the one real bug found during testing (422
misclassification) was caught and fixed with live confirmation before I signed
off. But I would not call this VERIFIED yet. Before that, I'd want: a second
live tenant on a different pod (wd3, wd5, or wd12) to confirm the 422 finding
and the URL-construction logic generalize beyond wd1; the doctor privacy check
re-run against actual scrape output on disk, not an empty data/ats/ directory;
and at least one tenant whose payload populates jobPostingId, locationsText,
jobFamilyGroup, or jobScheduleType, so those four field mappings are exercised
against real data instead of only against test fixtures.