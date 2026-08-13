# Recipe Run Log

Human-readable history for recipe-driven work.

Use this file to record what was run, what worked, what failed, and what should
be tested next. Keep entries short. Do not include secrets, real phone numbers,
private emails, or sensitive application notes.

## 2026-06-12 — Wrote Tutorial 00 (Exercise Zero) in full

- **Recipe:** manual
- **Inputs:** `docs/search-profile-design.md` v3, `data/bls/compact/soc_occupation_compact.csv` schema, MYCROFT.md attestation rules
- **Outputs:** `docs/tutorials/00-personal-layer.md` — privacy-first setup (gitignore gate), paste-ready agent prompt (3 tasks with hard stops), resume.json schema with evidence fields and attested timestamp, full 8-question conditional intake tree (visa block only after a "no" at Q4), gaps draft with 3 required student edits (kill a row / rewrite a row / write one startable plan), migration rule, 4 exercises (delete test, inflation hunt, multi-model SOC second opinion, six-hour test), what-can-go-wrong table; index updated to ready
- **Result:** Exercise Zero runnable today in Claude Code with zero new scripts. Extraction-corrections count gives each student a personal fluency-vs-truth measurement on day one.
- **Open issues:** SOC matching quality from free text untested against real student answers; the agent prompt should be re-tested once the config generator lands (Tutorial 01 hand-edit note then changes); per-section attestation flags deliberately omitted in favor of one file-level timestamp — revisit if students attest carelessly.

## 2026-06-12 — Dropped personas for conditional intake; added gaps file + Exercise Zero

- **Recipe:** manual (human design input: don't invent personas, ask for relevant facts — past for resume, future for wants/constraints; generate an editable gaps file; gaps migrate to resume with evidence; this is the first exercise)
- **Inputs:** `docs/search-profile-design.md` v3, `data/bls/compact/soc_occupation_compact.csv` (skill_*_lv / ability_*_lv columns)
- **Outputs:** design doc revised — personas replaced by conditional intake over canonical fields (question tree branches on answers, no labels); new gaps-file section (generated draft from O*NET levels + scanned-posting keyword frequency, human-owned thereafter, regenerate-as-diff, migration-to-resume.json only with evidence); Exercise Zero defined; tutorials index gains Tutorial 00 (planned)
- **Result:** Personal layer is three files with distinct epistemic status: resume.json (attested past), profile.yml (declared future), gaps.md (managed delta). Aspirational resume entries become structurally impossible. Engine extends from application allocator to development allocator (gaps = what to build in the six hours).
- **Open issues:** gap computation quality unknown until tried against a real resume + SOC pair; Tutorial 00 not yet written; intake question tree not yet drafted.

## 2026-06-12 — Added profile metadata + personas to the design

- **Recipe:** manual (human design input: resume facts ≠ search metadata; US citizens shouldn't see visa questions; start with a few personas, add later)
- **Inputs:** `docs/search-profile-design.md` v2
- **Outputs:** new "Profile metadata" section — four starting personas (international student, US new grad, employed-and-quietly-looking, flexible/part-time); two-layer model: canonical profile fields + personas as intake routing/defaults only; scorer weights become a function of profile fields (sponsorship weight ≈ 0 for citizens, timeline gate persona-conditional)
- **Result:** Persona proliferation avoided — combinations are field combinations, not new personas; engine reads fields, never labels. New-persona criterion is evidence-driven (recurring intake combinations the presets serve badly).
- **Open issues:** discretion mechanics for the employed persona (employer-affiliate exclusion list source?); whether persona presets live in data/ as a versioned file (recommended) or in the intake recipe prose.

## 2026-06-12 — Revised profile design: roles-first intake + resume.json layer

- **Recipe:** manual (human design input: never ask for companies; ask what they want to do → SOC codes → suggest companies; resume converted to verified JSON, generated docs derive from it)
- **Inputs:** `docs/search-profile-design.md` v1, `data/bls/compact/soc_occupation_compact.csv`, `scripts/resumes/`
- **Outputs:** design doc revised — 5-step pipeline (intake → SOC resolution with human confirmation gate → generate → review/prune → scan); new resume.json section (upload → extract → field-by-field human attestation → all resume artifacts generated as projections, never rewrites); search/ layout extended; course-project mapping section (student H-1B probability tools and resume-JSON converters are the engine's missing components)
- **Result:** Companies are now an output of the engine, not an input. Resume claims become machine-checkable against attested source fields — verified-data contract extended to the student's own history.
- **Open issues:** unchanged 4 open decisions + new: JSON Resume schema as-is vs extended; where extraction-hallucination checking lives (conformance script vs gate checklist).

## 2026-06-12 — Designed profile-driven config generation

- **Recipe:** manual (triggered by human design question: why does a human hand-edit portals.yml?)
- **Inputs:** `recipes/_profile.template.md`, `scripts/ats/detect-ats.py`, `data/bls/compact/soc_occupation_compact.csv` (alternate_titles columns), `data/80-days-to-stay/`, `scripts/ats/scan.mjs` (REALLOCATION_ENGINE_PORTALS env var)
- **Outputs:** `docs/search-profile-design.md` (intake → search/profile.yml → generated portals.yml with O*NET + curated synonym expansion, evidence-derived company suggestions, gitignored personal `search/` folder); Tutorial 01 Step 1 reframed (hand-editing = looking under the hood, not steady state)
- **Result:** Design connects four existing-but-disconnected pieces: profile template, detect-ats.py, O*NET alternate titles, sponsorship data. Human reads/judges generated config; never writes it.
- **Open issues:** Generator script, synonyms seed file, profile recipe, and `.gitignore` entry not yet built — design awaits approval on 4 open decisions (folder name, intake form, suggestion cap, fate of personal files in data/ats/).

## 2026-06-12 — Verified Greenhouse API liveness; fixed tutorial browser-check step

- **Recipe:** manual (triggered by human observation: `job-boards.greenhouse.io/databricks` redirects to the company careers site; same pattern at Duolingo)
- **Inputs:** `scripts/ats/providers/greenhouse.mjs` (read: provider derives `boards-api.greenhouse.io/v1/boards/<slug>/jobs` and fetches with `redirect: 'error'`); fetched the Databricks and Duolingo API endpoints directly
- **Outputs:** `docs/tutorials/01-first-scan.md` Step 6 rewritten (verify against the API JSON, not the board page, with explanation of the redirect pattern); new what-can-go-wrong row
- **Result:** Both APIs live and serving: Databricks ≈97 KB payload, 100+ postings; Duolingo ≈78 KB, ≈80 postings. The browser redirect is a UI relocation, not an API removal — human-facing board pages increasingly redirect to branded careers sites while the JSON API keeps serving. Scanner unaffected by design (API-only + redirect:'error'). This partially closes the prior open issue "provider fetch not verified": the endpoint is confirmed reachable and serving from this environment's fetch tool, though still not via the scanner process itself.
- **Open issues:** Job counts are approximate (counted from match listings, not a parsed total). If Greenhouse ever moves or gates boards-api, the provider fails loudly (`fetch failed` / redirect error) — correct behavior, but worth a tutorial-02 liveness note. Some companies may genuinely leave Greenhouse; a redirected board page plus a 404 from boards-api is the signature of that case.

## 2026-06-12 — Added tutorials layer (docs/tutorials/)

- **Recipe:** manual
- **Inputs:** `data/ats/portals.example.yml`, `scripts/ats/scan.mjs` (usage header, flags, default paths), `package.json`, MYCROFT.md conventions
- **Outputs:** `docs/tutorials/01-first-scan.md` (full predict→run→inspect→judge→record walkthrough with 6 graduated exercises and a what-can-go-wrong table), `docs/tutorials/README.md` (index; tutorials 02–05 marked planned), `DOMAIN.md` first-win section now points to the tutorial
- **Result:** The first-win command is no longer a bare one-liner; a student can complete the scan loop unassisted. Exercise 6 rehearses the attestation format; exercise 4 teaches the conformance/adequacy boundary by deliberate breakage.
- **Open issues:** Sample output numbers in Step 5 are illustrative (live fetch is blocked in this sandbox — could not capture a real success report). Tutorials 02–05 are titles only. Exercise 4's typo-provider behavior was not executed; the tutorial asks the student to discover it, but an instructor should verify the actual error message once on an unrestricted machine.

## 2026-06-12 — First honest run: BLS extract + ATS scan dry-run

- **Recipe:** manual (pre-recipe verification run under MYCROFT.md contract)
- **Inputs:** `scripts/bls/extract-soc-occupation-table.py` against `data/bls/db-30-2-text/` + `data/bls/oesm24nat/`; `npm run ats:scan -- --dry-run` with `REALLOCATION_ENGINE_PORTALS=data/ats/portals.example.yml`
- **Outputs:** `data/bls/compact/soc_occupation_compact.csv` (1,016 occupations; SHA-256 recorded in audit), `data/bls/bls-audit.md` (962/1,016 = 94.7% matched to OEWS 2024 detailed SOC rows)
- **Result:** BLS extractor runs clean after the `Skills.txt` fix; audit generated and read; CSV schema unchanged (`skill_*` columns intact). ATS scan loaded the example portal config, matched the Greenhouse provider, ran filter/dedup/report logic, and wrote nothing in dry-run mode — machinery verified.
- **Open issues:** ATS scan's live fetch failed in the sandboxed environment (network egress blocked) — provider fetch is **not** verified end-to-end; rerun on an unrestricted machine. BLS extractor was not executed pre-fix, so "failed before fix" is inferred from the missing `Recipes.txt` file, not observed. `playwright`/`sharp` not installed; only `js-yaml`/`glob` were needed for this run.

## 2026-06-12 — Fixed rename-shrapnel bugs; added lifecycle frontmatter

- **Recipe:** manual
- **Inputs:** `scripts/bls/extract-soc-occupation-table.py`, `scripts/ats/analyze-patterns.py`, `scripts/ats/README.md`, 8 core recipe files
- **Outputs:** three one-line fixes (`Recipes.txt`→`Skills.txt`; `modes/RUN_LOG.md`→`logs/RUN_LOG.md` in script and README; audit prose "recipe Level scores"→"skill Level scores"); MYCROFT.md lifecycle frontmatter added to `_shared` (type: contract) and `scan`, `pipeline`, `oferta`, `tracker`, `pdf`, `patterns`, `update` (status: DRAFT, todos_open: 11 each)
- **Result:** Known defects 1–2 from DOMAIN.md closed. Recipe status is now machine-readable.
- **Open issues:** `scripts/cowork-agentic-repo.py` still contains mangled prose ("Recipes and recipes…") — cosmetic, not load-bearing. The 33 non-core recipes have no frontmatter yet.

## 2026-06-11 — Established MYCROFT.md as source of truth

- **Recipe:** manual
- **Inputs:** architecture review of the full repo; Codex cross-review; Gru SDD; principles discussion (Cowork session)
- **Outputs:** `MYCROFT.md` (new — constitution v0.1.0: 8 principles, verification stack, recipe lifecycle, TODO-closure evidence, attestation format), `DOMAIN.md` (new — domain manifest: actual layout, runnable command surface, known gaps/defects), `CLAUDE.md` (rewritten as pointer to MYCROFT.md), `AGENTS.md` (rewritten as pointer; also removed "recipes, recipes" rename shrapnel)
- **Result:** One governing file; precedence rule explicit; current-vs-planned architecture separated (domain layout is current; `data/raw`/`data/verified`/snickerdoodle CLI marked roadmap). Claude Code named as v0 runtime.
- **Open issues:** Known defects listed in DOMAIN.md §Known gaps: BLS `Recipes.txt` bug, `modes/RUN_LOG.md` path bug, scorer unimplemented, no recipe has a logged run, doctor script not built, person-named recipes need privacy review, skill/recipe terminology in manuscript unreconciled. README and `docs/` not yet updated to cite MYCROFT.md.

## 2026-05-28 — Recipe folder converted to verified-data workflows

- **Recipe:** manual
- **Inputs:** `recipes/`, `scripts/`, `README.md`, `DATA_CONTRACT.md`
- **Outputs:** `recipes/_shared.md`, `recipes/README.md`, active recipes, and draft/helper recipe files
- **Result:** Recipes now point students toward repo scripts, audits, and logs instead of prompt-only recipes.
- **Open issues:** Some workflows remain intentionally marked as draft until supporting scripts exist.

## 2026-05-28 — Removed copied Job-Ops source tree

- **Recipe:** manual
- **Inputs:** `data/career-ops-main/`, `scripts/ats/`, `recipes/`, `resumes/`
- **Outputs:** `.gitignore`, `README.md`, `DATA_CONTRACT.md`, provider docs
- **Result:** Removed the copied reference directory after useful pieces had been adapted into maintained repo paths.
- **Open issues:** Provenance now lives in docs and adapted files, not in a local source copy.

## 2026-05-28 — Normalized data directory names

- **Recipe:** manual
- **Inputs:** old mixed-case 80 Days and BLS data directories, `data/sec/form-d/`
- **Outputs:** `data/80-days-to-stay/`, `data/bls/`, lower-kebab SEC extracted folders, updated docs/scripts
- **Result:** Source/reference data directories now use lower-case kebab-case names. Maintained automation now uses lowercase `scripts/` by repo convention.
- **Open issues:** Some source data filenames and JSON field values still preserve upstream naming.

## 2026-06-13 -- Context parity + privacy pass + doctor (consolidated; re-logged after a git reset dropped prior entries)

- **Parity:** brought this repo to the Madison/Mycroft context architecture — ported `conformance.mjs`/`to-markdown.mjs`/`build-instructions.mjs`, added `instructions/` (6 shared rule modules + `reallocation-engine.md` + manifest) compiling to generated root `AGENTS.md`/`CLAUDE.md`, plus `.claude/` hooks (archive-guard + conformance-check) and `.github/workflows/verify.yml` (conformance + instruction drift guard). `MYCROFT.md` confirmed identical to the other Mycroft-domain repos.
- **Privacy pass (gap #6):** 14 person-named case-study recipes anonymized -> `case-*.md` role slugs; student names + Canvas submission-IDs scrubbed. Verified zero residual PII repo-wide. Git **history** also purged via `git filter-repo` (--invert-paths on the 14 old paths + --replace-text on names/IDs); force-pushed.
- **doctor (gap #5):** `scripts/doctor.mjs` (`npm run doctor`) — environment + npm-command-target + domain-dir checks + recipe-status dashboard. Surfaced gap #8: only 7/42 recipes carry lifecycle frontmatter; declared todos_open (77) vs 518 body `[TODO` markers.
- **Note:** a later `git reset`/`filter-repo` reverted edits to pre-existing tracked files (DOMAIN.md gaps reconciliation, this log, package.json scripts, generated AGENTS/CLAUDE) while new files survived; re-applied 2026-06-13. New files were unaffected.
- **Result:** doctor + conformance green; DOMAIN.md known-gaps reconciled (#1,#2,#5,#6 resolved; #3,#4,#7,#8 open).

## 2026-06-13 -- Backfill recipe lifecycle frontmatter (gap #8)

- **Commands:** One-off migration over recipes/ (excl. README + templates): prepended a `status/todos_open/last_gate/attestation/recipe_version` block to the 34 recipes that lacked one, injected the lifecycle keys into `_shared.md` (kept `type: contract`), and reconciled `todos_open` to the true `[TODO`-marker body count everywhere. 7 already-stamped recipes unchanged (idempotent).
- **Result:** doctor now reports 42/42 recipes with frontmatter, 0 missing; declared todos_open == body markers (518 = 518, mismatch gone). Conformance clean. DOMAIN gap #8 resolved.
- **Open issues:** All 42 remain `status: DRAFT` — promotion past DRAFT still requires a real gated run (gap #4) + attestation. Frontmatter is now the substrate for that lifecycle tracking.

## 2026-06-13 -- Build the Bayesian Role Scorer (gap #3)

- **Skill:** Implement the book's Chapter-11 decision core — the composite role scorer / combiner.
- **Inputs:** spec from `chapters/11-the-bayesian-role-scorer.md` (composite form, weights sponsorship 0.35 / fit 0.30, multiplicative liveness+timeline gates, threshold ~0.3, Apply/Consider/Skip, override discipline, auditability thesis) + `docs/search-profile-design.md` (weights are a function of the profile, not constants). Confirmed exact structure by reproducing the chapter's worked example backward.
- **Commands:** Wrote `scripts/score/role-scorer.mjs` — combiner only (reads per-role evidence records; does not compute components). Multiplicative gates × weighted votes; profile-conditional sponsorship weight (→0 when authorization doesn't need sponsorship); per-term audit trace with source labels (record / model-judgment / your-input); documented-override support (override without a reason is ignored, per Ch.11); JSON + Markdown report + skip-rate summary. Config block annotates every weight/threshold with provenance; role_quality weight + Consider floor left as documented `[VERIFY]` defaults (not pinned by the chapter). Built fixture `data/examples/ch11-roles.json` reproducing the chapter's two roles + gate/Consider/override cases. Wired `npm run score`.
- **Result:** Verified against the book — Cambridge biotech composite 0.446 → Apply; identical-fit non-sponsor 0.178 → Skip; ghost posting gated to 0 → Skip; Likely-tier → Consider; documented override flips Skip→Apply and records the reason. Conformance clean; doctor sees the new command. DOMAIN gap #3 resolved.
- **Open issues:** `[VERIFY]` weights (role_quality, Consider floor) need confirmation vs the system design document before real decisions. The scorer is a pure combiner — wiring the upstream feeds (Ch.7/8/9/10) to emit the per-role evidence envelope is separate (the run-envelope schema is still `[TODO: DEFINE]` in recipes/pipeline.md) and tied to the honest run (gap #4).

## 2026-06-14 -- The honest run (gap #4): first gated, logged recipe run

- **Recipe:** `oferta` (Ch.11 Bayesian role scorer), **sample mode**, run id `oferta-2026-06-14-001`.
- **Command:** `npm run score data/examples/ch11-roles.json` (stored script; no ad-hoc code).
- **Inputs:** verified fixture `data/examples/ch11-roles.json` + run-envelope (`mode: sample`).
- **Gates:** 1 Source ✓ · 2 Scope ✓ (sample) · 3 Data-shape ✓ · 4 Script-readiness ✓ · 5 Approval n/a (no live network/writes/model) · 6 Report ✓. Human adequacy gate: **PENDING attestation.**
- **Result:** 5 roles → Apply 2 · Consider 1 · Skip 2 (skip 40%). Cambridge 0.446 / non-sponsor 0.178 reproduce Ch.11. Output fully sourced (record / model-judgment / your-input).
- **Artifacts:** `logs/oferta-2026-06-14.json`, `reports/generated/oferta-2026-06-14.md`, `data/examples/role-scores.{json,md}`.
- **Flags:** skip-rate 40% < 50% (curated fixture, expected); `role_quality` weight 0 [VERIFY] drops the Ch.9 signal (gap #3); 1 documented override.
- **Open:** machine half of P4 done; **human adequacy (P4 second half) outstanding** — attest to promote `oferta` past DRAFT.

## 2026-06-14 -- Rename MYCROFT.md → SNICKERDOODLE.md (constitution rebrand)

- **Why:** disambiguate this repo's constitution from the shared **Mycroft** agent-OS frame it was forked from. Renamed to a cookie-recipe name fitting the book's "recipe" vocabulary.
- **Did:** `git mv MYCROFT.md SNICKERDOODLE.md`; rebranded the file's own identity (`# SNICKERDOODLE`, "Snickerdoodle is an agent-operating system…", lineage line preserved). Swapped every `MYCROFT.md` path/governance reference (instructions/ source, `conformance.mjs` required-files list, `manifest.yml` `@import`, CI comment, `DOMAIN.md`, `status.md`, `archive/README.md`, docs/). Rebranded this-repo "Mycroft" prose (P4, "a Snickerdoodle domain", audit doc); **kept** the cross-repo "Madison and Mycroft" shared-library mention.
- **Rebuilt:** `node scripts/build-instructions.mjs --promote` → `AGENTS.md` + `CLAUDE.md` regenerated; `CLAUDE.md` now imports `@SNICKERDOODLE.md`.
- **Untouched:** `data/` CSVs (real company names containing "mycroft") and prior RUN_LOG history (append-only).
- **Result:** conformance + doctor green; no stale `MYCROFT.md` outside data/history.

## 2026-08-11 -- Workday production scraper (scripts/ats/scrapers/workday/)

- **Recipe:** manual (human spec: model on `scrapers/lever/scraper.py` exactly; Workday CXS API; hard EMPTY-vs-ERROR separation).
- **Inputs:** `scripts/ats/scrapers/lever/scraper.py` (structure), `scrapers/common/{config,normalize,rate_limiter,retry,schema_validator,logger}.py`, unified job-record schema in `schema_validator.py`.
- **Outputs:** `scripts/ats/scrapers/workday/{__init__.py,scraper.py}`. POST `wd3.myworkdayjobs.com/wday/cxs/<tenant>/jobs`, paginated by 20 until `offset >= total` (MAX_PAGES=100 guard); per-company `jobs.json` / `normalized_jobs.json` / `metadata.json`, `summary.json` at output root with four disjoint buckets: `found` / `empty` / `not_found` / `errors`.
- **Result:** 70/70 offline checks passed against a monkeypatched transport (pagination, field mapping, EMPTY, 404, 500, transport failure, JSON-parse failure, all-records-invalid, mixed-validity partial, mid-run page failure, missing `total`, file layout). Every emitted record passes `validate_batch(strict=True)`. `node scripts/conformance.mjs` clean on both files. No network calls made — no live tenant has been scraped yet.
- **Open issues:** (1) `postedOn` is usually a relative display string ("Posted 30+ Days Ago") — deliberately NOT converted to a date, so `date_posted` is "" for most Workday postings; only absolute dates convert. (2) The `en-US/<tenant>/job/<externalPath>` URL template needed leading-slash + duplicate `job/` normalization or every posting URL would be malformed — verify against a live tenant before trusting it in the liveness gate. (3) Tenants on hosts other than `wd3` (wd1/wd5/myworkdaysite.com) are out of scope. (4) `employment_type` map covers the common descriptors; unmapped ones fall back to "" rather than guessing. (5) Untested against a live Workday board — no attestation, and the empty/error split has only been exercised against mocks.

## 2026-08-12 -- Workday scraper, first live run: BLOCKED (wrong API host/path in spec)

- **Command:** `python -m scrapers.workday.scraper --company "Wayfair" --tenant "wayfair" -o ../../data/ats/workday/` (from `scripts/ats/`).
- **Result:** 0 found · 0 empty · 0 not found · **1 error** (`request_failed`). Exit 0. Only `data/ats/workday/summary.json` written; no company directory, so no `jobs.json` / `normalized_jobs.json` / `metadata.json`.
- **Blocker:** the specified host `wd3.myworkdayjobs.com` resolves to **127.0.0.1** — Workday publishes loopback for the bare `wdN.myworkdayjobs.com` and apex names. Confirmed via local resolver (dns1.neu.edu) and 8.8.8.8; `wd1`/`wd5`/apex behave identically. General connectivity was fine (example.com 200, greenhouse DNS resolves), and the failure reproduced with the sandbox disabled — so this is the spec's URL, not a sandbox block and not a scraper defect.
- **Probe (diagnosis only, no code changed):** real tenant hosts are `<tenant>.wdN.myworkdayjobs.com` — `wayfair.wd1` → 209.177.165.20, `wayfair.wd5` → 209.177.169.65 (bare GET returns 406). The CXS path also needs a **career-site segment**: `/wday/cxs/<tenant>/<careerSite>/jobs`. `wayfair.wd1/.../wayfair/{WayfairCareers,Wayfair,External}/jobs` → 401; `wayfair.wd5/.../wayfair/WayfairCareers/jobs` → **422** (endpoint exists, payload/headers rejected). Correct host + career site for this tenant not yet established.
- **Assessment of the scraper itself:** the ERROR path behaved as designed — retry/backoff fired 3× then gave up, the company was classified `errors` (not `empty`, not `not_found`), no job count was claimed, and no per-company files were written. The empty-vs-error separation held under a real failure.
- **Open:** connector needs a per-tenant `host` + `career_site` (both un-derivable from a company name, like `tenant`); `--file` CSV would grow to `company,tenant,host,career_site`. Awaiting the human's call before changing the interface.

## 2026-08-12 -- Workday scraper: careers-URL auto-discovery + first successful live run

- **Change:** replaced `--tenant` with `--careers-url`. `parse_careers_url()` recovers host/tenant/career-site from the public URL (`<tenant>.wd<N>.myworkdayjobs.com/<career-site>`), tolerating a `/en-US/` locale segment, a trailing slash, a query string, and a scheme-less paste. Unparseable URLs get their own summary bucket `invalid_careers_url` — **no request is attempted**, extraction_status `error`, no files written (a config fault is not a network fault). CSV columns are now `company,careers_url`. POST body and pagination unchanged, per spec.
- **Command:** `python -m scrapers.workday.scraper --company "City of Aurora" --careers-url "https://auroragov.wd1.myworkdayjobs.com/Careers" -o ../../data/ats/workday/`
- **Result:** **1 found · 0 empty · 0 not found · 0 invalid URL · 0 errors.** 39 postings, `total_reported` 39, one page (`total < 20`… in fact 39 → 2 pages), 0 validation errors, extraction_status `success`. Artifacts: `data/ats/workday/summary.json` + `data/ats/workday/cityofaurora/{jobs,normalized_jobs,metadata}.json`.
- **URL construction verified against the live site:** two generated `source_url`s fetched → HTTP 200, ~31–33 KB each. The `/en-US/<career-site>/job/<externalPath>` form resolves; the previous `wd3` form never did.
- **Finding — this tenant's list endpoint is sparse.** The CXS response carries only `title`, `externalPath`, `postedOn`, `bulletFields` (39/39 postings). `jobPostingId`, `locationsText`, `jobFamilyGroup`, `jobScheduleType` are **absent**, so `job_id` falls back to `externalPath` (as specified) and `location` / `department` / `employment_type` are "" for all 39 records. `postedOn` is relative ("Posted Today"), so `date_posted` is "" too. The scraper degraded correctly — every record still validates — but the records are thin. `bulletFields` here is `[req_id, location]`; its contents are a per-tenant display config, so positional decoding is **not** attempted (would violate P3). Enriching location/date requires either a per-tenant bulletFields map or a per-posting detail fetch (`/wday/cxs/<tenant>/<site><externalPath>`), i.e. one extra request per job.
- **Regression:** 83/83 offline checks pass against a monkeypatched transport (all prior cases plus 5 invalid-URL forms confirming zero HTTP attempts and no directory written). Conformance clean.
- **Open:** (1) sparse-field enrichment decision above, un-taken; (2) only one tenant exercised — pagination beyond 2 pages, EMPTY, and 404 are still mock-only against live Workday; (3) no attestation.

## 2026-08-12 -- Workday scraper: live failure-bucket tests (1 pass, 1 surprise)

- **Test 2 (invalid URL) — as expected.** `--careers-url https://example.com/not-workday` → `invalid_careers_url` bucket, `extraction_status=error`, no company directory written, and **no HTTP request attempted** (no `ats_scraper.retry` line; the offline suite asserts zero transport calls with a spy). Exit 0.
- **Test 1 (nonexistent tenant) — landed in `errors`, not `not_found`.** `fakecorpxyz123.wd1.myworkdayjobs.com` → HTTP **422** → `unexpected_status_422`. Not a defect in the branch logic; the expectation about Workday's signalling was wrong.
- **Probe — Workday's failure codes on the CXS endpoint (verified live, fixed known-good body):**
  | Condition | Status |
  |---|---|
  | unknown **tenant** | **422** (`errorCode: HTTP_422`) |
  | known tenant, unknown **career site** | **404** (`errorCode: S21`, `"not found: J..."`) |
  | known tenant + site | 200 |
  | bogus trailing path segment | 405 |
- **Also confirmed:** `*.wd1.myworkdayjobs.com` is **wildcard DNS** — `fakecorpxyz123` and `zzznope999` both resolve to 209.177.165.20, the same edge IP as `auroragov`. A nonexistent tenant therefore never fails DNS; it always reaches Workday and gets a status code back. DNS resolution is not a usable existence check.
- **Consequence:** the `not_found` bucket fires only for the wrong-career-site case (404). An unknown tenant currently lands in `errors` beside timeouts and 5xx — i.e. a permanent configuration mistake is presented as a retryable transient failure, which is the conflation this design exists to prevent.
- **Open (human decision, not taken):** map first-page **422 → `not_found`** so both "this board does not exist" cases share a bucket, keeping the distinguishing status code in the record. Deferred to the human — reclassification is a judgment about what 422 means, and P1 puts that call on the person, not the agent.

## 2026-08-12 -- Workday scraper: 422 reclassified to not_found (human decision cleared)

- **Decision:** human approved mapping first-page **422 → `not_found`**, joining 404. Rationale: both are permanent "no such board" answers, so neither belongs beside timeouts and 5xx in `errors`, where they'd read as retryable.
- **Change:** added `NOT_FOUND_STATUS_CODES = (404, 422)`; the first-page branch now tests membership. `status_code` added to the `not_found` summary record so the two stay separable in the data (404 = unknown career site, 422 = unknown tenant), and the console line names which half was wrong. Module docstring updated with both codes and the wildcard-DNS reason the status code is the only existence check. **First page only** — mid-run, a 422 still degrades the run to `partial` and keeps the pages already fetched.
- **Re-ran break test 1:** `fakecorpxyz123` → `[1/1] FakeCorp -> fakecorpxyz123: not found (HTTP 422 — unknown tenant)`; `0 found · 0 empty · 1 not found · 0 invalid URL · 0 errors`; summary row carries `"status_code": 422`; no company directory written.
- **Re-ran break test 2:** unchanged — `https://example.com/not-workday` → `invalid_careers_url`, no HTTP request, no directory.
- **Happy-path re-check (live, scratch output dir so the test artifacts stayed intact):** City of Aurora → 39 found, extraction_status `success`. No regression from the reclassification.
- **Offline suite: 94/94** (was 83) — added 422→not_found, status-code preservation on both 404 and 422, an end-to-end run with both a 404 and a 422 company asserting two `not_found` rows with distinct codes and no directories, and a **regression guard that mid-run 422 stays `partial`, not `not_found`**. Conformance clean.
- **Open:** unchanged from the prior entry — sparse-field enrichment (bulletFields / detail fetch) still un-taken; EMPTY and multi-page pagination still mock-only against live Workday; no attestation.

## 2026-08-13 -- Workday connector: moved to a real clone, smoke suite ported to pytest

- **Why:** development happened in a GitHub **zip export**, which carries no `.git`, so `git add` failed with "not a git repository" and nothing could be staged. The five entries above were written in that zip copy and are ported here verbatim.
- **Clone:** `git clone https://github.com/nikbearbrown/the-reallocation-engine.git` → `main` at `3124767`. It had no `scripts/ats/scrapers/workday/` and no workday recipes, so nothing was overwritten. Verified the shared layer the connector depends on (`scrapers/common/*.py`, `lever/scraper.py`) is **content-identical** to the zip — every file differed only in line endings (`core.autocrlf=true` checks out CRLF).
- **Test port:** the 94-check development smoke script (a temp file with a hand-rolled `check()` helper, outside the repo) became `scripts/ats/scrapers/workday/test_scraper.py` — real pytest functions, **one per original check, 1:1, none dropped or merged**. Two `@pytest.mark.parametrize` sites (3 filenames × 2 companies; 5 bad URLs × 2 assertions) expand to 16 items, so the count still lands on 94. Scenario setup moved into module-scoped fixtures; transport patching is now a `try/finally` context manager, so no test can leak a stubbed transport into another. This also makes the recipe's section-7 verification command true — it previously named a file that did not exist.
- **Result:** `python -m pytest scripts/ats/scrapers/workday/test_scraper.py -v` → **94 passed in 2.02s**. `node scripts/conformance.mjs` → 136 files, all conform. `npm run doctor` → exit 0, environment runnable, **privacy check clean**.
- **Caveat on the privacy check:** the clone's `data/ats/` holds only `portals.example.yml` — the live Aurora scrape output was **not** copied over, so doctor's "no private/PII paths are tracked" never saw it. Checked the real question directly instead: `git check-ignore -v` resolves both `data/ats/workday/summary.json` and `.../cityofaurora/normalized_jobs.json` to `data/ats/.gitignore:11:*`, a bare wildcard. Scrape artifacts cannot be committed whether or not they exist on disk.
- **Frontmatter:** doctor reports **0 of 44 recipes carry lifecycle frontmatter** in upstream `main` — a pre-existing repo-wide gap, not introduced here. (Note this contradicts the 2026-06-13 entry above claiming 42/42; that work is not in upstream `main`. Flagged per the Precedence rule rather than silently reconciled.) The two workday recipes were then stamped `status: RUNNABLE-LIVE` on human instruction.
- **Open:** `ATTESTATION.md` requested but **not written** — an attestation is the human's record of having judged the running system (Verification Stack, layer 3); an agent authoring it would void its meaning. `attestation: null` in both recipes until a person writes and signs one.

## 2026-08-13 -- Defect: doctor.mjs frontmatter parser does not strip YAML comments

- **Conflict (logged per the Precedence rule, not worked around).** `SNICKERDOODLE.md`'s Recipe Lifecycle template ships each field with an inline `#` comment (`status: DRAFT          # DRAFT | SPECIFIED | ...`). Stamping the two workday recipes with that exact format produces valid YAML — a real parser returns `{'status': 'RUNNABLE-LIVE', 'todos_open': 0, 'last_gate': 'live-run, ...', 'attestation': None, 'recipe_version': '0.1.0'}`.
- **But `scripts/doctor.mjs:86` reads frontmatter with `line.match(/^([a-z_]+):\s*(.*)$/i)`**, which captures the trailing comment as part of the value. Doctor therefore reports `by status: RUNNABLE-LIVE  # DRAFT | SPECIFIED | RUNNABLE-SAMPLE | RUNNABLE-LIVE | VERIFIED 2` instead of `RUNNABLE-LIVE 2`, and would group two recipes with the same status into different buckets if their comments differed.
- **Not fixed here.** SNICKERDOODLE governs, so the recipes keep the spec's format; the defect is in the checker, and patching `scripts/doctor.mjs` is outside this contribution's scope. Fix is one line — strip ` #…` before trimming, honoring quoted values — and belongs in its own change.
- **Counts are unaffected:** `with lifecycle frontmatter: 2   missing: 42`. Only the status grouping string is wrong.

## 2026-08-13 -- Attestation skeleton created (unsigned, awaiting human judgment)

- **Artifact:** `scripts/ats/scrapers/workday/ATTESTATION.md` — Ran/Saw/Expected rows filled from this session's actual runs and the four "Did not test" items recorded; all 10 `JUDGMENT:` lines and the `Human signer` / `Date` fields left deliberately blank. Written by the agent at the human's direction; per the Verification Stack (layer 3) the attestation is the *human's* record, so nothing is signed and no verdict is claimed. Both recipes keep `attestation: null` — the field is repointed here only once a person fills and signs it.
