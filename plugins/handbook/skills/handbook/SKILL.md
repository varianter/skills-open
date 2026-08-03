---
name: handbook
description: Answer questions about Variant as an employer using the official employee handbook (håndbok) at handbook.variant.no — company purpose and values, benefits, compensation, leave, processes, HMS, security incidents, offices/locations, and other internal policy questions. Use this when a question is about "how things work at Variant" but doesn't obviously belong to one specific handbook section; otherwise prefer the more specific fundamentet, information, prosesser, or avdelinger skill directly.
---

# Variant Handbook

The handbook is a public, statically generated site — no login required — and is the
single source of truth for how Variant works as a company. It is content-managed on
GitHub and can change at any time, so **never answer policy questions from memory or
training data**. Always fetch the live page first.

## Sections

| Skill | URL segment | Covers |
|---|---|---|
| `fundamentet` | `/fundamentet/*` | Purpose & values, life/work balance, how we work (flex hours), social life, compensation philosophy |
| `information` | `/information/*` | Practical day-to-day info: pay, expense reports (reiseregning), time tracking, vacation, insurance/pension, equipment |
| `prosesser` | `/prosesser/*` | Internal processes: hiring, HMS (health/safety), sustainability, diversity, leadership, security incidents, contract templates |
| `avdelinger` | `/avdelinger/*` | Office locations (Oslo, Bergen, Trondheim, Stavanger): address, wifi, practicalities |

## Steps

1. Identify which section the question best fits using the table above, then use that
   section's skill (it has a `references/pages.md` index of exact pages and URLs).
2. If the question spans sections or doesn't clearly fit one, fetch
   `https://handbook.variant.no/soek` (the search page) or the site root
   `https://handbook.variant.no` to locate the right page.
3. Always fetch the actual page content with `WebFetch` before answering — do not
   guess or rely on cached knowledge of what the handbook says.
4. Content is in Norwegian (Bokmål). Translate for the user as needed, but preserve
   exact figures, names, and legal/HR specifics rather than paraphrasing loosely.
5. Cite the URL you used so the employee can open the full page themselves.
