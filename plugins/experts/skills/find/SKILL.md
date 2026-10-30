---
name: find
description: Find College of Liberal Arts (COLA, UT Austin) faculty experts on a topic, for reporter/media requests or research to feature. Use when the user asks who in Liberal Arts works on, studies, or could comment on a subject, or pastes a reporter's request.
argument-hint: <topic, question, or pasted request>
---

# Find COLA experts

Request: $ARGUMENTS

Default count: **5** people (N below, unless the request asks for a different number).

Use the `cola-experts` connector's tools. If they aren't available, tell the user the COLA experts
connector isn't connected and stop; don't answer from general knowledge instead. Suggest typing
`/mcp` to check `cola-experts`: if it's missing, update the Experts plugin and start a new session;
if it shows as failed, the server may be down.

## 1. Understand the request
Work out, from the wording (don't ask unless it's genuinely ambiguous):
- **Topic**, and any angle (e.g. "Texas", "historical", "policy").
- **Purpose**: *media* (default; a reporter needs someone to talk to) or *feature* (words like "feature", "highlight", "story ideas", "spotlight").
- **How many** (N): the number the request asks for, otherwise the default count. Same for media and feature.
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

## 4. Answer
Up to N people, best first; fewer rather than padding with weak matches. Media format, one block
per person, with the name alone as its heading so names read at a glance:

```markdown
### 1. Name
Title · Department

- **Why they fit:** one sentence grounded in the record (quote a phrase where useful).
- **Evidence:** [Profile](profile_url) · [Paper or book title](url) · [UT Experts](url)
- **Contact:** email
- **Flags:** e.g. "Not in UT Experts", "On leave" (leave this line out if there are none)
```

For *feature* requests, add one or two possible story angles from their recent work just below the title line.

End with: *COLA data as of {data_as_of}. Check availability before referring.*

Rules:
- Use only facts in the records. Never invent publications, titles, or contact details.
- Cite with the URLs in the record; if a claim has no URL, say which field it came from.
- If nobody is a strong match, say so and list the closest people with what's missing.
