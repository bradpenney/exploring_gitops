# Exploring GitOps

Source for [gitops.bradpenney.io](https://gitops.bradpenney.io) — GitOps and FluxCD:
from understanding the paradigm to running production-grade continuous delivery in Kubernetes.

Built with [MkDocs](https://www.mkdocs.org/) + [Material](https://squidfunk.github.io/mkdocs-material/).
Pushes to `main` are published to the `gh-pages` branch via `mkdocs gh-deploy` (see `.github/workflows/ci.yaml`).

## Where This Fits in the bradpenney.io Network

GitOps is the last mile — it's how everything the other sites teach actually ends up running in
production, without anyone typing a deploy command by hand:

- **[containers.bradpenney.io](https://containers.bradpenney.io)** — the `docker build` /
  `docker push` this site's CI pipeline automates is taught there by hand, step by step, so you
  understand what the pipeline is actually doing before you trust it.
- **[k8s.bradpenney.io](https://k8s.bradpenney.io)** — what Flux is actually reconciling *to*:
  Pods, Deployments, Services. This site assumes that ground is already solid.
- **[bradpenney.io](https://bradpenney.io)** — the hub all of these link out from.

The short version: containers explains what gets built, Kubernetes explains what runs it, and
this site explains how the two get connected without a human in the loop.

## Local development

```bash
poetry install
poetry run mkdocs serve   # live preview at http://127.0.0.1:8000
poetry run mkdocs build --strict   # what CI runs before deploying
```
