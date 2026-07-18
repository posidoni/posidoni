# Repository instructions

## Purpose

This repository renders Mikhail Kuznetsov's public GitHub profile. `README.md` is the primary artifact; supporting files should help humans and AI agents maintain it without adding noise to the profile.

## Editing principles

- Keep the profile concise, factual, and easy to scan.
- Prefer plain Markdown, short tables, and simple disclosure with `<details>`.
- Do not add badges, generated statistics, decorative images, or ornamental HTML unless Mikhail explicitly requests them.
- Do not add an "Open to work" banner or restore the former hobbies/coffee section unless explicitly requested.
- Keep email, LinkedIn, and Telegram together in the final contact section. Do not repeat them elsewhere.
- Preserve concrete engineering outcomes and metrics, but never invent, extrapolate, or "improve" a claim.
- Describe companies briefly and neutrally. Avoid company-worship or marketing language.
- Match the language of the user's request. Keep English profile copy natural and direct.

## Source of truth

- Treat facts supplied directly by Mikhail as authoritative.
- Use the current `README.md` and the articles under `blog/` as repository-local context.
- If a fact looks stale or conflicts with a newer owner instruction, flag it for human review instead of guessing.

## Workflow

1. Read `README.md` and inspect the current diff before editing.
2. Make the smallest coherent change that satisfies the request.
3. Run `git diff --check` and verify every added local link.
4. Show the diff to Mikhail before committing or pushing unless he explicitly says otherwise.

Human review is the final gate for public changes.
