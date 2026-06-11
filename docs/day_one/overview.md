---
title: "Day One: Understanding GitOps - Overview"
description: "GitOps explained for developers and new team members. Learn what changed, why it changed, and how to work confidently with a FluxCD-enabled cluster."
---

# Day One: Understanding GitOps

Your platform team set up [Flux](https://fluxcd.io/flux/) and now deployments work differently. You merge your changes to `main`, CI builds a semantically versioned OCI artifact and pushes it to your company's artifact registry, and a controller inside the cluster detects the new version and reconciles. You never run [`kubectl apply`](https://k8s.bradpenney.io/day_one/kubectl/first_deploy/) directly anymore.

Day One explains the paradigm and gives you the vocabulary to work confidently in a GitOps-managed environment. You're not the person who installed Flux — you're the developer inheriting it.

---

## Why You're Here

=== ":material-git: I Just Merged a PR — Did It Deploy?"

    You merged your changes. CI passed and pushed a new OCI artifact to the registry. But you're not sure if Flux picked it up.

    - Flux polls your artifact registry on an interval (usually 1-5 minutes)
    - After detecting a new semver version, it fetches the artifact and reconciles the cluster
    - Check status with [`kubectl get ocirepository`](https://k8s.bradpenney.io/day_one/kubectl/commands/), `kubectl get kustomization`, or `kubectl get helmrelease`

    **[Reading Flux Status](reading_flux_status.md) covers this.**

=== ":material-file-tree: I'm Seeing Resources I Don't Recognize"

    `kubectl get` is showing resource types — `OCIRepository`, `Kustomization`, `HelmRelease` — that don't exist in a standard cluster.

    - These are Flux's own custom resources, each managing part of the delivery pipeline
    - A *source* fetches content from a registry; a *reconciler* applies it to the cluster
    - Knowing what each one represents is the first step to working in a Flux-managed cluster

    **[Flux Resources Explained](flux_resources.md) covers this.**

=== ":material-help: My Team Said 'Just Commit Your Change'"

    You need to ship a feature, fix a bug, or adjust a config value.

    - Your commit triggers CI, which builds a *semantically versioned* OCI artifact (e.g. `v2.3.1`) — never an untagged "latest"
    - Flux deploys the specific versions it's told to; production is pinned to tested versions and doesn't chase the newest build
    - You don't touch `kubectl` or [Helm](https://k8s.bradpenney.io/day_one/helm/commands/) directly — but "merged" isn't "live in prod" until that version is promoted

    **[Your Flux Workflow](your_flux_workflow.md) covers this.**

---

## The Articles

<div class="grid cards" markdown>

-   :material-lightbulb: **[What Is GitOps?](what_is_gitops.md)**

    ---

    The paradigm behind GitOps — why it exists, what problem it solves, and how FluxCD fits in. Start here.

-   :material-source-branch: **[Your Flux Workflow](your_flux_workflow.md)**

    ---

    The full pipeline from code commit to running deployment — and why enterprise GitOps uses OCI artifacts, not direct Git polling.

-   :material-magnify: **[Reading Flux Status](reading_flux_status.md)**

    ---

    The `kubectl` commands that tell you whether Flux reconciled successfully — and how to read the errors when it didn't.

-   :material-file-tree: **[Flux Resources Explained](flux_resources.md)**

    ---

    `OCIRepository`, `Kustomization`, `HelmRelease` — what each Flux custom resource is and what its output tells you.

</div>
