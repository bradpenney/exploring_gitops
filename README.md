# Exploring GitOps

Source for [gitops.bradpenney.io](https://gitops.bradpenney.io) — GitOps and FluxCD:
from understanding the paradigm to running production-grade continuous delivery in Kubernetes.

Built with [MkDocs](https://www.mkdocs.org/) + [Material](https://squidfunk.github.io/mkdocs-material/).
Pushes to `main` are published to the `gh-pages` branch via `mkdocs gh-deploy` (see `.github/workflows/ci.yaml`).

## Local development

```bash
poetry install
poetry run mkdocs serve   # live preview at http://127.0.0.1:8000
poetry run mkdocs build --strict   # what CI runs before deploying
```
