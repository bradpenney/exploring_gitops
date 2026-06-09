---
title: "Exploring GitOps - FluxCD Guide"
description: "A progressive guide to GitOps and FluxCD — from understanding the paradigm to running production-grade continuous delivery in Kubernetes."
---

<img src="images/exploring_gitops.png" alt="Exploring GitOps" class="img-responsive-right" width="300">

# Welcome to Exploring GitOps

GitOps is how modern platform teams ship software reliably, at scale, with a full audit trail baked in. Git becomes your deployment mechanism. Kubernetes reconciles toward whatever your repository declares. Nothing runs in your cluster that wasn't committed first.

This site covers GitOps with **[FluxCD](https://fluxcd.io/flux/)** — a [CNCF Graduated project](https://www.cncf.io/projects/flux/) and the GitOps engine of choice for platform teams who want production-grade continuous delivery without the operational overhead.

## Who Is This For?

<div class="grid cards" markdown>

-   :material-account: **Day One — Developers and New Team Members**

    ---

    Your platform team set up Flux and now deployments happen differently. You're not running `kubectl apply` anymore — you commit to Git and something else takes over.

    **Day One explains the paradigm** so you can work confidently with a GitOps-enabled cluster.

    [:octicons-arrow-right-16: Start with Day One](day_one/overview.md)

-   :material-server: **Essentials — Platform Engineers Getting Started**

    ---

    You're responsible for setting up or maintaining a FluxCD deployment. You know Kubernetes, understand Helm, and now you need to wire it all together with GitOps.

    **Essentials covers installing, bootstrapping, and the core Flux building blocks.**

    *Coming soon*

-   :material-lightning-bolt: **Efficiency — Platform Engineers Going Deeper**

    ---

    Flux is running. Now you need multi-tenancy, automated image updates, Slack notifications, and production monitoring.

    **Efficiency covers the workflows that separate a working Flux install from a well-run one.**

    *Coming soon*

-   :material-trophy: **Mastery — Production GitOps**

    ---

    You're running Flux in production and need to handle secrets management, disaster recovery, security hardening, and scale.

    **Mastery is for the platform engineers who own this in production.**

    *Coming soon*

</div>

## Why FluxCD?

FluxCD is a [CNCF Graduated project](https://www.cncf.io/projects/flux/) — the same tier as Kubernetes, Prometheus, and Helm. It's GitOps-native by design, runs entirely as Kubernetes controllers, and has no external control plane to manage.

This site covers FluxCD exclusively. [ArgoCD](https://argo-cd.readthedocs.io/en/stable/) is a direct competitor — both are CNCF projects implementing the same GitOps principles, and the mental model transfers between them. The specific resources, commands, and workflows differ significantly. Flux earns its place for platform teams who want tight Kubernetes-native integration and minimal operational overhead; ArgoCD appeals to teams that want a built-in UI and a more opinionated workflow. This site won't cover ArgoCD.

## The Site Structure

Every section follows a progressive tier:

| Tier | Audience | Tone |
|------|----------|------|
| **Day One** | Developers and new team members | Mentor-to-learner, builds understanding |
| **Essentials** | Platform engineers, getting started | Peer-to-peer, no hand-holding |
| **Efficiency** | Platform engineers, going deeper | Colleague-to-colleague, full context |
| **Mastery** | Production owners | Colleague-to-colleague, unfiltered depth |

Start where you are. The tiers are designed to be read in order within your level, but you can jump to what you need.
