---
date: 2026-10-16
categories:
  - ci-cd
tags:
  - github-actions
  - mkdocs
---

# Wiring MkDocs to GitHub Pages with strict builds

The docs site was already deploying. The problem was *how* it was deploying.

## The old pipeline

The original workflow ran `mkdocs gh-deploy --force` with `contents: write` on
the workflow token. That works. It also does three things I didn't want:

1. Pushes the built site as commits to a `gh-pages` branch, polluting the
   repo's commit history with bot commits.
2. Grants the workflow write access to the repo, which it doesn't need.
3. Depends on an unpinned `mkdocs-material`, so a bad upstream release breaks
   the build with no local change to explain it.

## The new pipeline

Two jobs, `build` and `deploy`. Build produces a Pages artifact; deploy
publishes it. Build uses `mkdocs build --strict`, which fails on any warning —
broken links, missing nav entries, unknown anchors. Nothing reaches the live
site unless it builds clean.

Permissions are scoped to what each job needs: `contents: read` for checkout,
`pages: write` and `id-token: write` for the deploy. No write access to the
repo. No `gh-pages` branch.

Dependencies are pinned in `requirements.txt`. `pip install -r
requirements.txt` installs the same versions on every machine, every run.

## What broke along the way

Two things worth remembering:

The home page was named `00-index.md`. MkDocs maps filenames to URLs almost
literally, so that became `/infra-lab/00-index/` instead of `/infra-lab/`.
Renaming it to `index.md` fixed the root URL. The `00-` prefix was a leftover
from an ordering trick that MkDocs doesn't actually need — nav order comes
from the `nav:` list, not the filename.

Switching from branch deploys to artifact deploys requires flipping the Pages
source in repo settings from "Deploy from a branch" to "GitHub Actions". The
workflow can run green and the site can still serve the old branch if that
setting is missed. Green CI does not mean the change is live.

## Why this matters beyond one repo

The pipeline is small, but the shape is the one that scales: build once,
publish the artifact, keep permissions minimal, pin what you depend on, fail
loudly on warnings. The same pattern shows up in every serious CI/CD setup,
whether it's a docs site or a fleet of services.