# Teach PR skill

Based on the [Teach skill from Matt Pocock](https://github.com/mattpocock/skills), adapted to be grounded in a specific pull request or branch instead of a freeform topic.

To avoid getting lazy when using code agents and AI when developing. We still need to learn how things work and what the code we use actually does. This skill deep-dives into a specific PR or branch: it reads the diff, commit messages, and (when available) the PR's title, description, and review comments to work out what new technology or techniques it introduces, drafts a mission from that, and builds short interactive lessons that send you back to the real diff to read yourself — not a paraphrase of it.

State lives in `.teach-pr/<pr-number-or-branch>/` inside the repo. Add that path to your `.gitignore` — the skill won't do it for you. If the PR gets new commits after you start, the next session will notice and offer to extend the mission and lessons for the delta.
