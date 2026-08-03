---
name: information
description: Answer practical day-to-day questions from the employee handbook's "Praktisk info" section — salary payment (lønnsutbetaling), expense reports (reiseregning, utgiftsføring), the sales process (om salg), time tracking, vacation and days off (ferie, timeføring), insurance/pension/sick leave/parental leave (forsikringer, pensjon, sykefravær, permisjon), and equipment/gadget budget/software licenses (gadgetbudsjett, programvarelisenser, utstyr). Use for concrete "how do I..." questions about pay, time, expenses, health benefits, or gear.
---

# Praktisk info (Practical Info)

## When to use

Use this skill for concrete, procedural employee questions — how to submit an
expense report, how vacation/time tracking works, what insurance covers, how to get
new equipment — as opposed to company values/culture (see `fundamentet`) or internal
business processes like hiring or security incidents (see `prosesser`).

## Steps

1. Check `references/pages.md` below for the page whose title/description best
   matches the question.
2. Fetch that exact URL with `WebFetch`, passing a prompt that describes the user's
   actual question so the fetch extracts the relevant part of the page.
3. If no page is a clear match, fetch `https://handbook.variant.no/soek` to search,
   or fall back to the `handbook` router skill.
4. Answer from the fetched content only — this page can change over time, so don't
   rely on prior knowledge of what it used to say. Translate from Norwegian as
   needed, but keep exact figures/terms precise (amounts, deadlines, entitlements).
5. Cite the URL used.
