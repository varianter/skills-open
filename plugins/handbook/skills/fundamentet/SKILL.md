---
name: fundamentet
description: Answer questions about Variant's foundation (Fundamentet) from the employee handbook — company purpose and values (formål og verdier), work-life balance and diversity (selve livet), flexible hours and how we work (arbeidet), social life and events (sosialt), and compensation philosophy (penger — lønn, bonus, overskuddsdeling). Use for "why does Variant exist", "what are Variant's values", "flexitime", "parental/family policy", "profit sharing", or similar foundational questions.
---

# Fundamentet (The Foundation)

## When to use

Use this skill for questions about Variant's core purpose, values, culture, and how
people work day to day at a philosophical/policy level — as opposed to step-by-step
practical procedures (see the `information` skill) or internal business processes
(see the `prosesser` skill).

## Steps

1. Check `references/pages.md` below for the page whose title/description best
   matches the question.
2. Fetch that exact URL with `WebFetch`, passing a prompt that describes the user's
   actual question so the fetch extracts the relevant part of the page.
3. If no page is a clear match, fetch `https://handbook.variant.no/soek` to search,
   or fall back to the `handbook` router skill.
4. Answer from the fetched content only — this page can change over time, so don't
   rely on prior knowledge of what it used to say. Translate from Norwegian as
   needed, but keep exact figures/terms precise for compensation-related content.
5. Cite the URL used.
