---
name: profile
description: Show everything known about one College of Liberal Arts (UT Austin) faculty member, with sources, to vet them before a referral. Use when the user asks about a specific COLA faculty member by name or EID.
argument-hint: <name or EID>
allowed-tools: WebSearch
---

# COLA faculty profile

Person: $ARGUMENTS

Call the `cola-experts` connector's `get_profile` with the name or EID. If the tool isn't available,
tell the user the COLA experts connector isn't connected and stop. Suggest typing `/mcp` to check
`cola-experts`: if it's missing, update the Experts plugin and start a new session; if it shows as
failed, the server may be down.

- **Several matches**: list them (name, title, department) and ask which one; then call again with the EID.
- **No match**: say so; suggest a different spelling, or that they may not be in the COLA directory.

Then run these `WebSearch` calls, all in parallel:
1. `"Full Name" "University of Texas"` plus 2–3 words from their research areas: news coverage, quotes, op-eds, interviews, podcasts.
2. `"Full Name"` plus their department or field: personal or lab website, other coverage.
3. `"Full Name"` with `allowed_domains` bsky.app, x.com, twitter.com, linkedin.com, threads.net, youtube.com: social accounts.

Keep a result only if it clearly identifies this person: it names UT Austin, their department or field, or links to a site in their record. If unsure, leave it out. Don't fetch the pages. Web results never change titles or contact details. If `WebSearch` isn't available, skip it, leave out the External mentions line, and say so.

Present it in the same layout as `/experts:find`, with the name alone as the heading:

```markdown
### Name
Title · Department(s) and role · [Profile](profile_url)

- **Expertise:** research-area tags, interests, two-line summary of the bio.
- **Recent publications:** up to 5, newest first, linked, one sub-bullet each.
- **Recent courses:** titles; link syllabi.
- **Media:** UT Experts listed or not ([UT Experts](url)), and its expertise blurb if listed.
- **Contact:** email · [CV](cv_url) · personal website links
- **External mentions:** [Personal site](url) · up to 5 news items, newest first, as [Outlet: "Headline" (Mon YYYY)](url) · social accounts ([Bluesky](url), [LinkedIn](url), …); "none found" if nothing.
- **Flags:** on leave; record not updated in 2+ years (`updated`); UT Experts title differs from COLA's (COLA is authoritative); graduate student. Leave this line out if there are none.
```

End with: *COLA data as of {data_as_of}. External mentions from a web search on {today's date}; may be incomplete.*
Use only what's in the record, except External mentions, which come only from web results, each linked to its source.
