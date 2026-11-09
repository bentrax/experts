---
name: find
description: Find College of Liberal Arts (COLA, UT Austin) faculty experts on a topic, for reporter/media requests or research to feature. Use when the user asks who in Liberal Arts works on, studies, or could comment on a subject, or pastes a reporter's request.
argument-hint: <topic, question, or pasted request>
allowed-tools: WebSearch
---

# Find COLA experts

Request: $ARGUMENTS

Default count: **5** people (N below, unless the request asks for a different number).

Use the `cola-experts` connector's tools. If they aren't available, tell the user the COLA experts
connector isn't connected and stop; don't answer from general knowledge instead. Suggest typing
`/mcp` to check `cola-experts`: if it's missing, remove the Experts plugin, install it again, and start a new session;
if it shows as failed, the server may be down.

## 1. Understand the request
Work out, from the wording (don't ask unless it's genuinely ambiguous):
- **Topic**, and any angle (e.g. "Texas", "historical", "policy").
- **Purpose**: *media* (default; a reporter needs someone to talk to) or *feature* (words like "feature", "highlight", "story ideas", "spotlight").
- **How many** (N): the number the request asks for, otherwise the default count, at most 10 (if they asked for more, show 10 and say that's the maximum). Same for media and feature.
- **Constraints**: department, deadline, "include grad students", etc.

## 2. Search
Call `search_experts` with 4–8 `terms`: the exact phrase, synonyms, narrower subfields, broader
fields, and the vocabulary scholars would use (a reporter's "AI and jobs" → "artificial intelligence",
"automation", "labor market", "future of work", "technology and employment").

- `limit`: 3 × N, at least 15 and at most 50.
- `department`: a slug from `list_departments`, when the request names one.
- `roles: ["faculty"]` unless the user wants researchers or graduate students too.
- `list_research_areas` shows the tags departments actually use; helpful for picking terms.
- If results are thin, search again with broader or different terms before concluding.

## 3. Vet the top candidates
Call `get_profile` with the EID of each of the 2 × N strongest (at most 15) and judge fit yourself;
the search score is only a starting point.

Prefer, in order:
1. Expertise stated directly: research-area tags, interests, publication titles, UT Experts blurb.
2. Bio mentions.
3. Course descriptions only (weakest; say so).

Then weigh: faculty over researchers over graduate students; recent publications;
**UT Experts listed** = has agreed to be a media contact. **Not listed** = flag "confirm willingness first".
Flag anyone **on leave**, any record not updated in 2+ years, and grad students.

## 4. Search the web and OpenAlex
For each person you'll list, run these `WebSearch` calls, all in parallel:
1. `"Full Name" "University of Texas"` plus 2–3 topic words: news coverage, quotes, op-eds, interviews, podcasts.
2. `"Full Name"` plus their department or field: personal or lab website, other coverage.
3. `"Full Name"` with `allowed_domains` bsky.app, x.com, twitter.com, youtube.com, substack.com: social accounts and newsletters.

- Keep a result only if it clearly identifies this person: it names UT Austin, their department or field, or links to a site in their record. If unsure, leave it out.
- Don't fetch the pages; the result's title, outlet, and date are enough.
- Web results never change titles, contact details, or who is on the list.
- Keep UT's own coverage (UT News, liberalarts.utexas.edu). For *feature* requests, flag a past feature there: "Already featured by COLA (Mon YYYY)" or "by UT News".
- If `WebSearch` isn't available, skip this step, leave out the External mentions line, and say so once above the list.

Also call `get_publications` once per person you'll list (never for the others you vetted), with
`query` = their EID and `limit` 3, in parallel with the web searches:
- `status: matched` → use `author` and `publications`.
- `status: candidates` → compare each candidate's `display_name`, `display_name_alternatives`, `last_known_institutions`, `works_count` and `topics` with the person's COLA department, title and interests. If exactly one clearly fits, call again with its `author_id` (same `limit`) and flag "OpenAlex match chosen by name; verify". If none fits, or two fit equally, treat it as `none`.
- `status: none` → write "no OpenAlex record identified" on the line. This says nothing about their output; OpenAlex covers books and the humanities thinly.
- Flag "OpenAlex author not at UT Austin" when `author.last_known_institutions` doesn't include UT Austin.
- A tool error "OpenAlex: not found." (unknown `author_id`) is `status: none` for that person only; later calls continue.
- On a tool error (`OpenAlex unavailable …`): make no further `get_publications` calls this run, leave out the Publications (OpenAlex) line for everyone, and say "OpenAlex lookup unavailable" once above the list.
- OpenAlex results never change titles, contact details, or who is on the list. Mention `openalex_as_of` only if asked how fresh they are.

## 5. Answer
Up to N people, best first; fewer rather than padding with weak matches. Media format, one block
per person, with the name alone as its heading so names read at a glance:

```markdown
### 1. Name
Title · Department

- **Why they fit:** one sentence grounded in the record (quote a phrase where useful).
- **Evidence:** [Profile](profile_url) · [Paper or book title](url) · [UT Experts](url)
- **Publications (OpenAlex):** {author.works_count} works · [Title (YYYY)](doi_url, else openalex_url) · … (up to 3, newest first, kept apart from the COLA titles in Evidence; "no OpenAlex record identified" if none)
- **Contact:** email
- **External mentions:** [Personal site](url) · [Outlet: "Headline" (Mon YYYY)](url) · [Bluesky](url) · [Substack](url) (up to 3 news items, on-topic first, then newest; "none found" if nothing)
- **Flags:** e.g. "Not in UT Experts", "On leave", "OpenAlex match chosen by name; verify" (leave this line out if there are none)
```

For *feature* requests, add one or two possible story angles from their recent work just below the title line.

End with: *COLA data as of {data_as_of}. External mentions from a web search on {today's date}; may be incomplete. Check availability before referring.*

Rules:
- Use only facts in the records, except External mentions, which come only from web results, each linked to its source, and Publications (OpenAlex), which come only from `get_publications`. Never invent publications, titles, or contact details.
- Cite with the URLs in the record; if a claim has no URL, say which field it came from.
- If nobody is a strong match, say so and list the closest people with what's missing.
