---
date: "2026-04-11 16:12"
title: "Exploring GitOps - FluxCD Continuous Delivery Guide"
description: "A progressive guide to GitOps and FluxCD — from understanding the paradigm to running production-grade continuous delivery in Kubernetes."
---

<img src="images/exploring_gitops.png" alt="Exploring GitOps" class="img-responsive-right" width="300">

# Welcome to Exploring GitOps

GitOps is how modern platform teams ship software reliably, at scale, with a full audit trail baked in. Your desired state is declared in version control, and Kubernetes continuously reconciles toward that declaration. Nothing runs in your cluster that wasn't committed first.

This site covers GitOps with **[FluxCD](https://fluxcd.io/flux/)** — a [CNCF Graduated project](https://www.cncf.io/projects/flux/) and the GitOps engine of choice for platform teams who want production-grade continuous delivery without the operational overhead.

## Who Is This For?

Start where you are. The tiers build in order, but jump to whatever you need.

<div class="grid cards two-col" markdown>

-   :material-account: **Day One — Developers and New Team Members**

    ---

    Your platform team set up Flux and now deployments happen differently. You're not running `kubectl apply` anymore — you commit to Git and something else takes over.

    **Day One explains the paradigm** so you can work confidently with a GitOps-enabled cluster.

    [:octicons-arrow-right-16: Start with Day One](day_one/overview.md)

-   :material-server: **Essentials — Platform Engineers Getting Started**

    ---

    You're responsible for setting up or maintaining a FluxCD deployment. You know Kubernetes, understand Helm, and now you need to wire it all together with GitOps.

    **Live now:** deploying real platform services (Traefik, cert-manager, external-dns) as a versioned OCI artifact. Installing and bootstrapping Flux itself is still coming.

    [:octicons-arrow-right-16: Deploying Platform Services with Flux](essentials/deploying_the_edge_stack.md)

-   :material-lightning-bolt: **Efficiency — Platform Engineers Going Deeper**

    ---

    Flux is running. Now you need automated image updates, progressive delivery, deployment notifications, and monitoring Flux in production.

    **Efficiency covers the workflows that separate a working Flux install from a well-run one.**

    *Coming soon*

-   :material-trophy: **Mastery — Production GitOps**

    ---

    You're running Flux in production and need to promote immutable artifacts across environments, source secrets via External Secrets, sign and verify what you ship, and run Flux at scale.

    **Mastery is for the platform engineers who own this in production.**

    *Coming soon*

</div>

## Why FluxCD?

FluxCD is a [CNCF Graduated project](https://www.cncf.io/projects/flux/) — the same tier as Kubernetes, Prometheus, and Helm. It's GitOps-native by design, runs entirely as Kubernetes controllers, and has no external control plane to manage.

!!! info "GitOps Is More Than FluxCD"
    This site covers FluxCD exclusively, but Flux isn't the only GitOps tool. [ArgoCD](https://argo-cd.readthedocs.io/en/stable/) is a direct competitor — both are CNCF projects implementing the same GitOps principles, and the mental model transfers between them. The specific resources, commands, and workflows differ significantly. Flux earns its place for platform teams who want tight Kubernetes-native integration and minimal operational overhead; ArgoCD appeals to teams that want a built-in UI and a more opinionated workflow. This site won't cover ArgoCD.

