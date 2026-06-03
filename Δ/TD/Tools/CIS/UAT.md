---
created: 2026-06-02
updated: 2026-06-02
type: read
status: seed
aliases:
  - CIS UAT
  - User Acceptance Testing
  - UAT environment
topics:
  - CIS
  - UAT
  - user acceptance testing
  - testing environments
source_type: internal notes
source: brain
author:
related:
  - "[[CIS Training 1]]"
ideas: []
projects:
  - "[[AI Inbox Triage for Cisco]]"
tags:
  - delta/read
  - td/tools
  - cis
  - uat
---

# UAT

## Summary
UAT is the CIS testing environment used to learn workflows, test new functionality before production, and validate access-dependent behavior from different user perspectives.

## Key Points
- UAT provides a safer place to experiment with CIS features and workflows before they affect production.
- It can be used to impersonate or act as other users to see what they see and test access-specific functionality.
- Most UAT data appears to lag production by about 10 days, though some areas, such as Ops Center logs, may be more current.
- Changes made while using another person's account can affect that person's visible preferences in UAT.

## Why This Matters
UAT is important to understand for where initial deployment is, where to test things, and things to keep in mind while working on it.

## Connections
- Related knowledge: [[CIS Training 1]]
- Possible ideas:
- Relevant projects: [[AI Inbox Triage for Cisco]]

# Notes
---

## UAT Fundamentals
---

**Definition:** UAT is the testing environment for **CIS**.

UAT is useful for:
- Learning new programs and functionality in a lower-risk environment.
- Testing newly added functionality before it reaches production.
- Viewing CIS from another user's perspective to understand what they can see and what actions they can take.
- Trying functionality that may not be available from your own account.

## UAT Quirks
---

### Data delay
- Most UAT data is delayed by about 10 days.
- Some areas may be more recent, such as Ops Center logs.

### User preferences
- If you are using another person's account, you can change visible preferences such as appearance and font profiles.
- Be careful when changing settings while testing from another user's perspective.
