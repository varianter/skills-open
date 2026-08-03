---
name: prosesser
description: Answer questions about Variant's internal processes from the employee handbook — hiring and interviews (ansatt), health/safety/environment (HMS), sustainability (bærekraft, klimaregnskap), diversity (mangfold), leadership (ledelse), security incidents (sikkerhetshendelser), and contract/document templates (avtaler). Use for "how do we hire", "who do I report a safety issue to", "what's our climate accounting", "security incident procedure", or "where are the contract templates".
---

# Prosesser (Processes)

## When to use

Use this skill for questions about how Variant runs itself operationally — hiring,
health and safety, sustainability tracking, diversity work, leadership structure,
security incident response, and legal/contract templates — as opposed to company
values (see `fundamentet`) or personal practical matters like pay and vacation (see
`information`).

## Steps

1. Check `references/pages.md` below for the page whose title/description best
   matches the question.
2. Fetch that exact URL with `WebFetch`, passing a prompt that describes the user's
   actual question so the fetch extracts the relevant part of the page.
3. If no page is a clear match, fetch `https://handbook.variant.no/soek` to search,
   or fall back to the `handbook` router skill.
4. Answer from the fetched content only — this page can change over time, so don't
   rely on prior knowledge of what it used to say. Translate from Norwegian as
   needed, but keep exact names/contacts/procedures precise, especially for HMS and
   security-incident content where accuracy matters.
5. Cite the URL used. For security incidents or urgent HMS matters, surface any
   named contacts/phone numbers from the page directly rather than paraphrasing them.
