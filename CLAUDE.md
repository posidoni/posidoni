# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

This is `posidoni/posidoni` — a GitHub **profile repository**. Because the repo name matches the owner's username, `README.md` renders as the profile page shown on the owner's GitHub profile. There is no application code, build system, or test suite; the repository is entirely Markdown content plus the images it references.

## Structure

- `README.md` — the GitHub profile page (Mikhail Kuznetsov, Senior Go backend engineer). Edits here are public-facing on the profile.
- `blog/` — long-form Markdown posts. Posts are linked from the "Writing" section of `README.md`; when adding a post, add a matching link there.
- `assets/` — images referenced from blog posts and the README via relative paths (e.g. `../assets/...` from within `blog/`).
- `.github/dependabot.yml` — weekly Dependabot for `github-actions` and `gomod` ecosystems (present for hygiene though there is currently no Go module).
- `lefthook.yml` — Git pre-commit hooks (see below).

## Commands

There is nothing to build, lint, or test. The only tooling is the Git hook layer.

```bash
lefthook install        # one-time: wire up the pre-commit hook
lefthook run pre-commit # manually run hooks against staged files
```

## Key convention: image metadata stripping

The `pre-commit` hook (`lefthook.yml`) runs `exiftool -All=` on every staged image (`png/jpeg/jpg/gif/webp/bmp/tiff/pdf`) to strip **all** EXIF/metadata, then re-stages the cleaned file. This is a deliberate privacy step — any image added to `assets/` must go through this hook (requires `exiftool` and `lefthook` >= 1.9.0 installed locally). `exiftool` writes `*.original` backups, which are git-ignored.

When adding images, prefer letting the hook clean them rather than committing raw camera/screenshot files directly.
