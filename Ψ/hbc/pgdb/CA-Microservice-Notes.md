---
created: 2026-05-07
updated: 2026-05-07
type: read
status: seed
aliases: []
topics:
  - HBC, AI, beh
source_type: Notes
source:
author: User
related: []
ideas:
projects:
  - AI-Final
tags:
---

# CA-Microservice-Notes

## Summary
---
- Notes on what's going on with the Microservices for CA

## Current Standing
---
- [ ] Need to implement the report from RedCap and handle the duplicates



## Feature Lineup (SPECS)
---

~~***Initialize Fetching + Discovery Logic***~~
- Fetch candidate files from `HBClab/boost-beh` `/meta` testing
- Use a configurable source ref/branch, not hardcoded `tree/main/meta`
- Handle GitHub pagination, retries, transient errors, and rate limits
- Compute source metadata: path, file family, ref/commit, content hash
- Emit structured dry-run output showing discovered/supported/unsupported files

***MUST HAPPEN!!!***
- [x] Fix lib with better task logic
- [x] Add redcap report

***Build Drift + Audit State Logic***
- Persist source file state and run history in PGDB audit/state tables
- Compare current content hashes against the last successful run
- Classify files as changed, unchanged, unsupported, invalid, deleted, or renamed where possible
- Ensure reruns of the same source state are idempotent

***Define Mapping Specs Before Transform Logic***
**MAPPING NOTES**: [[CA-ms-transform-notes]]
- Explicitly define how each supported `/meta` file family maps to PGDB/db-lib models
- Document source fields, target fields, defaults, type coercions, identity rules, and validation expectations
- Do not silently infer mappings for unsupported file families
- Identify any required `boost-pgdb` / `db-lib` updates before implementation

***Build In-Memory Transform + Validation Logic***
- Parse supported source files
- Transform parsed data through a dedicated in-memory mapper layer
- Validate transformed records against `boost-pgdb` / `db-lib`
- Add unit/contract tests using source fixtures and expected PGDB model outputs

***Build Safe Upsert Logic***
- Upsert changed/new validated records only
- Do not perform destructive deletes for removed/renamed source files
- Parse, transform, and validate all changed files before starting writes
- Apply writes as one transactional batch per reconciliation run
- Roll back/fail without partial writes if any write fails

***Build Automation + Operational Output***
- Add CLI entrypoint runnable via Nix
- Add GitHub Actions workflow, ideally manual dispatch plus scheduled run
- Include dry-run mode for validation without PGDB writes
- Emit structured logs/audit records with run id, source file, stage, status, and sanitized errors
- Verify secrets are configured and never logged

