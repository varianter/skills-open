# Variant Code skills

## Skill overview

- [codebase-onboarding](skills/codebase-onboarding): Generate a fresh, guided onboarding course for a new developer joining the codebase, with short interactive lessons and quizzes.
- [teach-pr](skills/teach-pr): Teach the user about the techniques and technology used in a pull request or branch.
- [varde](skills/varde): Build a static HTML page or small site styled with Variant's Varde design system.
- [varde-docs](skills/varde-docs): Turn markdown or documentation content into a static HTML documentation site styled with Varde.

## Use skills from this template

Use skills from plugins in this repo by connecting marketplace to Claude Code:

```
# if not added before
claude plugins marketplace add varianter/skills-open

# Installing Code plugin
claude plugin install code@variant-skills

# Use in claude
claude
/codebase-onboarding
/teach-pr
```


Or adding the skills directly:

```
npx skills add varianter/skills-open

# list all
npx skills add varianter/skills-open --list

# install one skill
npx skills add varianter/skills-open --skill teach-pr
npx skills add varianter/skills-open --skill codebase-onboarding
# ... etc
```
