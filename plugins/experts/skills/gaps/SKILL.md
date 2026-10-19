---
name: gaps
description: Report COLA faculty missing from UT Experts and UT Experts entries that disagree with COLA data.
disable-model-invocation: true
---

# UT Experts gap report

Call the `cola-experts` connector's `ut_experts_gaps`. If the tool isn't available, tell the user
the COLA experts connector isn't connected (check `/mcp` and sign in with their UT EID) and stop.

Report:
1. **Coverage**: faculty listed in UT Experts out of total faculty.
2. **Faculty not in UT Experts**, grouped by department. These are candidates to invite.
3. **Title conflicts** (COLA title vs UT Experts title, with link). COLA is authoritative;
   these UT Experts entries likely need updating.
4. **UT Experts entries with no COLA match** (link each). Likely departed, emeritus, or in another unit.

Close by noting that a college media rep can fix entries through the UT Experts Media Rep Login.
Keep lists compact; offer the full list if there are more than ~20 in a section.
