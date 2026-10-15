# 🏗️ Infra Lab

A systems engineering lab: Linux, Go, containers, observability, and the CI/CD
that ties them together.

This repo holds the tooling, configuration, and documentation behind my work in
systems and infrastructure engineering.

## 🧰 Stack

| Layer             | Tool                         |
| ----------------- | ---------------------------- |
| Docs framework    | MkDocs Material 9.7.0        |
| Language (docs)   | Python 3.12                  |
| CI/CD             | GitHub Actions               |
| Hosting           | GitHub Pages                 |
| Local development | WSL 2 (Ubuntu) on Windows 11 |

## 📁 Repository layout

```text
.
├── .github/
│   └── workflows/
│       └── deploy.yml              # CI/CD — build and deploy the docs site
├── docs/                           # Markdown source for the published site
│   ├── index.md                    # Home page
│   ├── 01-environment-setup.md
│   └── 02-documentation-as-code.md
├── mkdocs.yml                      # MkDocs configuration
├── requirements.txt                # Pinned Python dependencies
└── README.md
```

## 💻 Local development

Requires Python 3.10+ and a Unix-like shell. Tested on WSL 2 (Ubuntu).

Create a virtual environment outside the repo — keeps the project tree clean and
avoids running Linux binaries from the Windows filesystem.

```bash
python3 -m venv ~/venvs/mkdocs-infra
source ~/venvs/mkdocs-infra/bin/activate
```

Install pinned dependencies:

```bash
pip install -r requirements.txt
```

Serve locally with live reload:

```bash
mkdocs serve
```

The site is available at `http://127.0.0.1:8000/infra-lab/`.

Build the static site into `./site/`:

```bash
mkdocs build --strict
```

`--strict` fails the build on any warning — broken internal links, missing nav
entries, unknown anchors. CI runs the same command, so a clean local build is a
clean pipeline build.

## 🚀 Deployment

Deployment is handled by GitHub Actions on every push to `main`.

Pipeline stages:

1. Checkout the repo, set up Python 3.12 with pip caching.
2. Install dependencies from `requirements.txt`.
3. Run `mkdocs build --strict`.
4. Upload `site/` as a Pages artifact.
5. Publish the artifact to GitHub Pages.

The build and deploy steps run as separate jobs — deploy only proceeds if build
succeeds. A failed build does not touch the live site.

Live site: <https://kevzkip.github.io/infra-lab/>

## 📌 Versioning policy

Top-level dependencies are pinned exactly (e.g. `mkdocs-material==9.7.0`).
Transitive dependencies are not pinned. If a transitive package ever breaks a
build, it gets pinned with an inline comment explaining why.