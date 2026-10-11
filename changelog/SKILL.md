---
name: changelog
description: Create or update CHANGELOG.md in the current git repo's root, with one heading per date and one bullet per change. If there is no changelog, build it from the full git history; otherwise add the changes on the current branch that aren't in the changelog yet. Run manually before merging a branch.
disable-model-invocation: true
---

# Changelog

Keep `CHANGELOG.md` in the repository root as a short, readable history of what changed and when. It is written for someone skimming the project, not a copy of `git log`.

## Format

```markdown
# Changelog

All notable changes to this project, newest first.

## 2026-10-11
- Require responsive design in the mission, tech stack, and Phase 0 specs
- Add 44px nav tap targets and a viewport meta tag test

## 2026-10-07
- Add Hono server with a welcome page
```

- One `## YYYY-MM-DD` heading per date, newest first. Within a date, list the most significant changes first.
- One bullet per meaningful change, in the imperative mood ("Add …", "Fix …"), like a good commit subject.
- Leave out merge commits and pure noise (typo fixes, "wip", formatting-only commits). Fold a follow-up fix into the bullet for the change it fixes, so the log reads as a list of outcomes rather than attempts.
- Rewrite unclear commit subjects so they make sense without the diff, but don't invent changes the commits don't show.

## Steps

1. Find the project root: the current working directory (where the session started). The changelog lives there. Compare it with `git rev-parse --show-toplevel`. If the project is a subfolder of a bigger repo, run every `git log` below from the project root with a trailing `-- .`, so the changelog covers only commits that touch this project.
2. Find the default branch: `git symbolic-ref --short refs/remotes/origin/HEAD` (drop the `origin/` prefix). If there is no remote, use `main`, falling back to `master`.
3. **No `CHANGELOG.md` yet:** read the whole history with
   `git log --no-merges --date=short --format='%ad%x09%h%x09%s%n%b'`
   and group it by date (the author date). When a subject is too vague to describe the change (for example "Bug fix"), look at `git show --stat <hash>` to see what it touched. Write the file in the format above.
4. **`CHANGELOG.md` exists:** read it first. Collect the branch's commits with
   `git log <default>..HEAD --no-merges --date=short --format='%ad%x09%h%x09%s%n%b'`.
   If you are on the default branch itself, use the commits dated after the newest heading instead. Skip any change the changelog already describes. Add the rest under the right date headings, creating a heading if it doesn't exist yet and keeping the headings in date order. Don't edit existing entries unless they're wrong.
5. If there are uncommitted changes, don't log them. Say they were left out, because the log should only describe committed work.
6. Write the file, then show the user the new or changed entries.

Don't commit, push, or stage the changelog. The user reviews it and commits it as part of their own merge flow.
