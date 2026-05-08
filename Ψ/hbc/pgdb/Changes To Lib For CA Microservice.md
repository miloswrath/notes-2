---
created: 2026-05-08
updated: 2026-05-08
status: active
due:
confidence: medium
aliases: []
tags:
  - psi/execute
  - project
related:
  - "[[CA-Microservice-Notes]]"
  - "[[CA-ms-transform-notes]]"
sources: []
links: []
---

# Changes To Lib For CA Microservice

## Outcome
What does done look like?
 Yes — a few parts of the pasted spec conflict with the current boost_pgdb library/schema contract.

 ### Main contradictions

 ┌───────────────────────────────────────┬─────────────────────────────────────────────────────┬─────────────────────────────────────────────────────┐
 │ Spec says                             │ Lib/schema says                                     │ Why it matters                                      │
 ├───────────────────────────────────────┼─────────────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
 │ Per-task table shape: task_id,        │ create_task_table() creates: <task>_id,             │ study_id is not stored in task tables; it is        │
 │ study_id, study_subject_id, session,  │ study_subject_id, session_number, task_id,          │ derived through study_subjects. session should be   │
 │ [task data]                           │ created_at, plus custom columns                     │ session_number.                                     │
 ├───────────────────────────────────────┼─────────────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
 │ task_id is unique                     │ task_id is a FK to cognitive_tasks(task_id) and     │ The unique row id is <task>_id, e.g. flanker_id,    │
 │                                       │ repeats for every row of the same task              │ not task_id.                                        │
 ├───────────────────────────────────────┼─────────────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
 │ study_id must match existing study on │ Task rows only require study_subject_id; study_id   │ The microservice should resolve/validate            │
 │ every task row                        │ lives on study_subjects                             │ study_subject_id against study_subjects, not insert │
 │                                       │                                                     │ study_id into task tables.                          │
 ├───────────────────────────────────────┼─────────────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
 │ session column                        │ Lib uses session_number                             │ Using session would fail model/schema alignment     │
 │                                       │                                                     │ unless transformed.                                 │
 ├───────────────────────────────────────┼─────────────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
 │ Subjects <= 7699 are BOOST            │ Migration rules are 7000–7699 -> BOOST_OBS, plus    │ If the service treats all <7000 as observational,   │
 │ Observational                         │ explicit legacy exceptions sub-6011, sub-6013       │ it contradicts the migration validation.            │
 ├───────────────────────────────────────┼─────────────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
 │ 7700 > x > 8000                       │ Should be 7700 <= x <= 7999 / 7700–7999 -> BOOST_OO │ The spec’s inequality is impossible as written.     │
 ├───────────────────────────────────────┼─────────────────────────────────────────────────────┼─────────────────────────────────────────────────────┤
 │ “Add a new task table for each task”  │ Lib can create tables dynamically, but upsert       │ The lib currently has Flanker; arbitrary new tasks  │
 │                                       │ requires a matching BoostModel subclass per task    │ need either new model modules in the lib or an      │
 │                                       │                                                     │ added generic task-row mechanism.                   │
 └───────────────────────────────────────┴─────────────────────────────────────────────────────┴─────────────────────────────────────────────────────┘

 ### Compatible parts

 - Using only cc_master, mem_master, ps_master, ts_master is fine.
 - Ignoring ts_master-duplicate tasks in cc_master is a microservice ingestion rule and does not conflict with the lib.
 - Creating task tables on first run is compatible with create_task_table() since it uses CREATE TABLE IF NOT EXISTS.

 ### Suggested corrected table contract

 Use this instead:

 ```csv
   <task>_id, study_subject_id, session_number, task_id, [task data], created_at
 ```

## Next actions
- [ ] 

## Milestones
- [ ] 

## Related
- Knowledge:
- Ideas:

## Notes / log
- 2026-05-08 — Created.
