# Documentation-as-Code \& Static Site Generation

## 1\. Objective

Transition from manual note-taking to a professional **Docs-as-Code** pipeline. This module establishes an automated, version-controlled documentation site using **MkDocs** and the **Material for MkDocs** theme, hosted via a clean, structured repository layout.

\---

## 2\. Engineering Principles \& Decisions

* **Docs-as-Code Paradigm:** Documentation is treated with the same rigor as application code—stored in Git, peer-reviewed via pull requests, and automatically compiled.
* **Environment Isolation (PEP 668):** Python packages are strictly scoped using virtual environments (`venv`) to prevent system-wide package manager conflicts on Linux distributions.
* **Zero-Padding Curriculum Structure:** Files are prefixed numerically (`00-index.md`, `01-environment-setup.md`, `02-documentation-as-code.md`) to guarantee a deterministic, clean sidebar navigation flow.

\---

## 3\. Implementation Workflow

### Step 1: Establishing Python Isolation (`venv`)

To comply with modern Linux environment constraints and prevent dependency collisions, we initialized an isolated Python virtual environment inside the workspace root:

```bash
# Create the virtual environment
python3 -m venv venv

# Activate the session scope
source venv/bin/activate
```

> \*\*Why `venv`?\*\* Modern Linux distributions enforce PEP 668 to protect core OS libraries from breaking changes caused by global package managers (`pip`). Virtual environments provide an isolated container for project-specific binaries.

### Step 2: Installing Documentation Engines

With the virtual environment active, MkDocs and its professional Material theme were installed:

```bash
pip install --upgrade pip
pip install mkdocs mkdocs-material
```

### Step 3: Configuring the Engine (`mkdocs.yml`)

The root configuration file dictates the visual schema, feature enhancements, and navigation mapping:

```yaml
site\_name: Kervin's DevOps Journey
site\_url: https://your-github-username.github.io/DevOps-Journey/
site\_author: Kervin

theme:
  name: material
  palette:
    - scheme: slate
      primary: indigo
      accent: deep purple
  features:
    - navigation.instant
    - navigation.tracking
    - search.suggest
    - search.highlight
    - content.code.copy

nav:
  - Home: 00-index.md
  - 01. Environment Setup: 01-environment-setup.md
  - 02. Documentation as Code: 02-documentation-as-code.md
```

### Step 4: Local Preview Loop

To test modifications live before pushing to remote repositories:

```bash
mkdocs serve
```

This spins up a local development web server with hot-reloading active on `http://127.0.0.1:8000/DevOps-Journey/`.

\---

## 4\. Repository Hygiene \& Git Configuration

To maintain a clean state, build artifacts and local runtimes (`venv/`, `site/`) are excluded via `.gitignore`:

```text
venv/
site/
Thumbs.db
Desktop.ini

