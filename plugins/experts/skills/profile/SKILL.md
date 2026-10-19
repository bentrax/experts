---
name: profile
description: Show everything known about one College of Liberal Arts (UT Austin) faculty member, with sources, to vet them before a referral. Use when the user asks about a specific COLA faculty member by name or EID.
argument-hint: <name or EID>
---

# COLA faculty profile

Person: $ARGUMENTS

Call the `cola-experts` connector's `get_profile` with the name or EID. If the tool isn't available,
tell the user the COLA experts connector isn't connected (check `/mcp` and sign in with their UT EID) and stop.

- **Several matches**: list them (name, title, department) and ask which one; then call again with the EID.
- **No match**: say so; suggest a different spelling, or that they may not be in the COLA directory.

Present:

1. **Name**, title, department(s) and role, with profile link. Note if on leave.
2. **Expertise**: research-area tags, interests, two-line summary of the bio.
3. **Recent publications** (up to 5, newest first, linked).
4. **Recent courses** (titles; link syllabi).
5. **Media**: UT Experts listed or not (link), and its expertise blurb if listed.
6. **Contact**: email; CV and personal website links.
7. **Flags**: on leave; record not updated in 2+ years (`updated`); UT Experts title differs from COLA's (COLA is authoritative); graduate student.

End with: *COLA data as of {data_as_of}.* Use only what's in the record.
