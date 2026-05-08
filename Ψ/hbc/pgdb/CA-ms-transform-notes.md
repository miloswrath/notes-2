[[CA-Microservice-Notes]]

## General Overview
---
**Files to Work With (Ignore other files)**: `cc_master`, `mem_master`, `ps_master`, `ts_master`
*Important*: In CC Master, the same tasks as ts_master exist, ignore them in cc_master, only take the information from ts_master

### What to do with the files
---
- There is a task column in each file that denotes what task the data is associated with.
- Add a new task (on first run) table for each task that exists with the same column data that exists in the original file

**General Structure of Table in Rough CSV Format (Not SQL)**:
```csv
task_id, task_name, task_version, study_id, study_subject_id, session, [task data here]
```

task_id is unique, study_id must match an existing study in the study table, study_subject_id must match one that exists in study_subjects table, session and other information is grabbed from the files

**ID Matching to Study**
Subjects <= 7699 Are BOOST Observational
Subjects 7700 > x > 8000 Are Boost Observational Older Adults
Subjects >= 8000 Are Boost Intervention
- [ ] *Query the study and study_subjects table to make sure these align*


