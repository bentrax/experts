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
`cola-experts`: if it's missing, remove the Experts plugin, install it again, and start a new session; if it shows as
failed, the server may be down.

- **Several matches**: list them (name, title, department) and ask which one; then call again with the EID.
- **No match**: say so; suggest a different spelling, or that they may not be in the COLA directory.

Then call `get_publications` with the EID and `limit` 10, in parallel with the web searches below:
- `status: matched` → use `author` and `publications`.
- `status: candidates` → compare each candidate's `display_name`, `display_name_alternatives`, `last_known_institutions`, `works_count` and `topics` with the person's COLA department, title and interests. If exactly one clearly fits, call again with its `author_id` (same `limit`) and flag "OpenAlex match chosen by name; verify". If none fits, or two fit equally, treat it as `none`.
- `status: none` → write "no OpenAlex record identified" on the line. This says nothing about their output; OpenAlex covers books and the humanities thinly.
- Flag "OpenAlex author not at UT Austin" when `author.last_known_institutions` doesn't include UT Austin.
- A tool error "OpenAlex: not found." (unknown `author_id`) is `status: none` for that person only; later calls continue.
- On a tool error (`OpenAlex unavailable …`): leave out the Publications (OpenAlex) line and say "OpenAlex lookup unavailable" once.
- OpenAlex results never change titles or contact details. Mention `openalex_as_of` only if asked how fresh they are.

Also run these `WebSearch` calls, all in parallel:
1. `"Full Name" "University of Texas"` plus 2–3 words from their research areas: news coverage, quotes, op-eds, interviews, podcasts.
2. `"Full Name"` plus their department or field: personal or lab website, other coverage.
3. `"Full Name"` with `allowed_domains` bsky.app, x.com, twitter.com, youtube.com, substack.com: social accounts and newsletters.

Keep a result only if it clearly identifies this person: it names UT Austin, their department or field, or links to a site in their record. If unsure, leave it out. Don't fetch the pages. Web results never change titles or contact details. If `WebSearch` isn't available, skip it, leave out the External mentions line, and say so.

Present it in the same layout as `/experts:find`, with the name alone as the heading:

```markdown
### Name
Title · Department(s) and role · [Profile](profile_url)

- **Expertise:** research-area tags, interests, two-line summary of the bio.
- **Recent publications:** up to 5, newest first, linked, one sub-bullet each.
- **Publications (OpenAlex):** [OpenAlex author](author.url) · works_count works · last_known_institutions; then the newest 10 as sub-bullets, each Title (year, type, venue) linked to `doi_url`, else `openalex_url`. Kept apart from Recent publications, which is COLA's own list. "no OpenAlex record identified" if none.
- **Recent courses:** titles; link syllabi.
- **Media:** UT Experts listed or not ([UT Experts](url)), and its expertise blurb if listed.
- **Contact:** email · [CV](cv_url) · personal website links
- **External mentions:** [Personal site](url) · up to 5 news items, newest first, as [Outlet: "Headline" (Mon YYYY)](url) · social accounts ([Bluesky](url), [Substack](url), …); "none found" if nothing.
- **Flags:** on leave; record not updated in 2+ years (`updated`); UT Experts title differs from COLA's (COLA is authoritative); graduate student; OpenAlex match chosen by name, or OpenAlex author not at UT Austin (verify). Leave this line out if there are none.
```

End with: *COLA data as of {data_as_of}. External mentions from a web search on {today's date}; may be incomplete.*
Use only what's in the record, except External mentions, which come only from web results, each linked to its source, and Publications (OpenAlex), which come only from `get_publications`.
