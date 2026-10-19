---
name: find
description: Find College of Liberal Arts (COLA, UT Austin) faculty experts on a topic, for reporter/media requests or research to feature. Use when the user asks who in Liberal Arts works on, studies, or could comment on a subject, or pastes a reporter's request.
argument-hint: <topic, question, or pasted request>
---

# Find COLA experts

Request: $ARGUMENTS

Use the `cola-experts` connector's tools. If they aren't available, tell the user the COLA experts
connector isn't connected (check `/mcp` and sign in with their UT EID) and stop. Don't answer from
general knowledge instead.

## 1. Understand the request
Work out, from the wording (don't ask unless it's genuinely ambiguous):
- **Topic**, and any angle (e.g. "Texas", "historical", "policy").
- **Purpose**: *media* (default; a reporter needs someone to talk to) or *feature* (words like "feature", "highlight", "story ideas", "spotlight").
- **How many**: default 3–5 for media, 5–8 for feature.
- **Constraints**: department, deadline, "include grad students", etc.

## 2. Search
Call `search_experts` with 4–8 `terms`: the exact phrase, synonyms, narrower subfields, broader
fields, and the vocabulary scholars would use (a reporter's "AI and jobs" → "artificial intelligence",
"automation", "labor market", "future of work", "technology and employment").

- `department`: a slug from `list_departments`, when the request names one.
- `roles: ["faculty"]` unless the user wants researchers or graduate students too.
- `list_research_areas` shows the tags departments actually use; helpful for picking terms.
- If results are thin, search again with broader or different terms before concluding.

## 3. Vet the top candidates
Call `get_profile` with the EID of each of the 5–8 strongest and judge fit yourself; the search
score is only a starting point.

Prefer, in order:
1. Expertise stated directly: research-area tags, interests, publication titles, UT Experts blurb.
2. Bio mentions.
3. Course descriptions only (weakest; say so).

Then weigh: faculty over researchers over graduate students; recent publications;
**UT Experts listed** = has agreed to be a media contact. **Not listed** = flag "confirm willingness first".
Flag anyone **on leave**, any record not updated in 2+ years, and grad students.

## 4. Answer
Media format, one block per person, best first:

**Name**, Title, Department
Why they fit: one sentence grounded in the record (quote a phrase where useful).
Evidence: [Profile](profile_url) · [Paper or book title](url) · [UT Experts](url)
Contact: email · Flags: e.g. "Not in UT Experts", "On leave"

For *feature* requests, lead each person with one or two possible story angles from their recent work.

End with: *COLA data as of {data_as_of}. Check availability before referring.*

Rules:
- Use only facts in the records. Never invent publications, titles, or contact details.
- Cite with the URLs in the record; if a claim has no URL, say which field it came from.
- If nobody is a strong match, say so and list the closest people with what's missing.
