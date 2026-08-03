---
name: avdelinger
description: Answer questions about Variant's office locations (avdelinger/lokasjoner) from the employee handbook — addresses, wifi network/password, bike parking, and other office practicalities in Oslo, Bergen, Trondheim, and Stavanger. Use for "what's the wifi at the office", "where is the Oslo office", or similar location-specific questions.
---

# Avdelinger (Locations)

## When to use

Use this skill for questions specific to one of Variant's physical offices —
address, wifi, and other on-site practicalities — as opposed to company-wide policy
(see `fundamentet`, `information`, or `prosesser`).

## Steps

1. Check `references/pages.md` below for the office in question.
2. Fetch that exact URL with `WebFetch`, passing a prompt that describes the user's
   actual question so the fetch extracts the relevant part of the page.
3. If the office isn't listed or unclear which one applies, fetch
   `https://handbook.variant.no/soek` to search, or fall back to the `handbook`
   router skill.
4. Answer from the fetched content only — wifi passwords and office details can
   change, so don't rely on prior knowledge. Note that some pages point to Slack
   (e.g. `/info wifi`) for secrets that aren't published on the public site.
5. Cite the URL used.
