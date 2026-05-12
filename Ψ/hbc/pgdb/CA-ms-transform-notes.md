[[CA-Microservice-Notes]]

## Cognitive Assessment Transform Notes
---

These notes reflect the current `boost_pgdb` cognitive assessment representation after the cognitive task schema + variant/counterbalance changes.

## What Needs Transforming Right Now
---

For the current cognitive imports, treat `counterbalance` as `NULL` for all rows.

The source files and their actual columns are:

- `cc_master`
  - id/task columns: `task`, `subject_id`, `session`
  - measure columns to transform: `condition`, `accuracy`, `mean_rt`
- `mem_master`
  - id/task columns: `task`, `subject_id`, `session`
  - measure columns to transform: `condition`, `count_correct`, `mean_rt`, `accuracy`
- `ps_master`
  - id/task columns: `task`, `subject_id`, `session`
  - measure columns to transform: `condition`, `count_correct`
- `ts_master`
  - id/task columns: `task`, `subject_id`, `session`
  - measure columns to transform: `switch_cost`, `mixing_cost`

So the immediate transform inputs are:

- `task` -> cognitive task code / `task_id` resolution
- `subject_id` -> `study_subject_id` resolution
- `session` -> `session_number`
- per-file task data columns listed above

Do not wait on counterbalance discovery for these files; write `counterbalance = NULL`.

## Source Files
---

**Files to work with:**

- `cc_master`
- `mem_master`
- `ps_master`
- `ts_master`

**Important:** `cc_master` contains task rows that also exist in `ts_master`. For overlapping tasks, ignore the duplicate task data in `cc_master` and treat `ts_master` as authoritative.

## Current Storage Model
---

Cognitive assessment data is no longer stored in one unified scores table and should not be split into study-specific task tables by default.

Current rule:

- Use **one physical table per compatible cognitive task**, shared across studies.
- Table names follow `task_<task_code>` style, for example `task_AF`.
- Study membership is derived from `study_subject_id -> study_subjects -> studies`.
- Do **not** write `study_id` directly into task tables.
- Do **not** create separate per-study task tables.
- Only create a separate task table when task schema, scoring semantics, or analysis meaning are incompatible, and record that decision via `cognitive_task_storage_exceptions`.

## Task Identity and Variants
---

Each task row should identify:

- `study_subject_id` — FK to `study_subjects`
- `session_number` — positive session number from source data
- `task_id` — FK to `cognitive_tasks`
- `variant_id` — nullable FK to `cognitive_task_variants`
- `counterbalance` — nullable text label
- task-specific measurement columns from the source file

`task_id` is **not** the row's unique ID. The task table has its own surrogate primary key, for example `flanker_id` on `task_flanker`.

Task versions/forms should be represented through `cognitive_task_variants`, not by creating duplicate task tables. If the administered variant is unknown for historical rows, leave `variant_id` null.

Counterbalance should be preserved when available. If a task does not use counterbalancing or the value is unknown, leave `counterbalance` null.

For the current `boost-beh/meta` files listed above, assume the counterbalance value is unknown/unavailable and keep it null for every transformed row.

## Rough Table Shape
---

Use this conceptual shape for each task-specific table:

```csv
<task>_id, study_subject_id, session_number, task_id, variant_id, counterbalance, created_at, [task-specific data columns]
```

Example conceptual shape:

```csv
flanker_id, study_subject_id, session_number, task_id, variant_id, counterbalance, condition_1_accuracy, condition_1_rt, condition_2_accuracy, condition_2_rt, created_at
```

Do **not** use this old shape:

```csv
task_id, task_name, task_version, study_id, study_subject_id, session, [task data]
```

Reasons:

- `task_name` is represented by `cognitive_tasks.task_code` and the table name.
- `task_version` is represented by `cognitive_task_variants` / `variant_id`.
- `study_id` is derived through `study_subject_id`, not duplicated on task rows.
- `task_id` is the cognitive task lookup FK, not the row primary key.

## Natural Key / Idempotency
---

Task rows must be idempotent on this natural key:

```text
(study_subject_id, session_number, task_id, variant_id, counterbalance)
```

The DB uniqueness must treat null `variant_id` and null `counterbalance` as equal so repeated historical/non-counterbalanced writes do not create duplicates.

Implications:

- Same participant + same session + same task + same variant + same counterbalance = update/upsert same row.
- Same participant + same session + same task + different variant = distinct row.
- Same participant + same session + same task + same variant + different counterbalance = distinct row.

## Transform Flow
---

For each source row:

1. Read the source file and task column.
2. Normalize the task name/code to the shared `cognitive_tasks.task_code` value.
3. Resolve `study_subject_id` from the DB using the source subject identifier.
4. Resolve study membership through `study_subjects`; do not calculate or store `study_id` on the task row.
5. Resolve `task_id` from `cognitive_tasks`.
6. If a task version/form is known, resolve or register the corresponding `cognitive_task_variants` row and use its `variant_id`.
7. Write `counterbalance = NULL` for current `cc_master`, `mem_master`, `ps_master`, and `ts_master` transforms.
8. Map the actual source columns into the matching `task_<task_code>` model/table columns:
   - `cc_master`: `condition`, `accuracy`, `mean_rt`
   - `mem_master`: `condition`, `count_correct`, `mean_rt`, `accuracy`
   - `ps_master`: `condition`, `count_correct`
   - `ts_master`: `switch_cost`, `mixing_cost`
9. Write through the shared `boost_pgdb.upsert()` contract, not custom insert SQL.

## Creating / Registering Task Tables
---

When a compatible new cognitive task appears:

1. Ensure `cognitive_tasks` has a row for the task code.
2. Create one shared task table via `boost_pgdb.schema.create_task_table(task_name, columns)`.
3. Add a matching `boost_pgdb.models.tasks.<task_name>` model before production use.
4. Register known variants in `cognitive_task_variants`.
5. Add/update tests and release the shared DB library before the microservice depends on it.

Separate task tables are only allowed when one of these incompatibilities is documented:

- `schema` — fields cannot be represented in the existing task table
- `scoring` — scores have incompatible meaning or scale
- `analysis` — combining rows would be scientifically misleading

Approved exceptions belong in `cognitive_task_storage_exceptions`.

## Subject / Study Resolution
---

The microservice should treat `study_subjects` as authoritative.

Do not hard-code study assignment from subject-number ranges in the transform service except as a migration/backfill aid when no DB mapping exists.

Historical migration range notes:

- `7000-7699` -> `BOOST_OBS`
- `7700-7999` -> `BOOST_OO`
- `8000+` -> `BOOST_INT`
- documented carryover subjects `sub-6011` and `sub-6013` map to `BOOST_OBS`

Before writing task rows, query/validate that the resolved `study_subject_id` exists and maps to the expected study.

## Open Implementation Checks
---

- [ ] Confirm exact task code normalization rules for values in `cc_master`, `mem_master`, `ps_master`, and `ts_master`.
- [ ] Confirm which source columns indicate task variant/version/form.
- [x] For current work, use `counterbalance = NULL` for all rows from `cc_master`, `mem_master`, `ps_master`, and `ts_master`.
- [ ] Confirm source session field maps cleanly to `session_number`.
- [ ] Confirm all emitted task rows can resolve `study_subject_id` from existing DB data.
- [ ] Confirm every emitted `variant_id` belongs to the same `task_id` as the task row.
