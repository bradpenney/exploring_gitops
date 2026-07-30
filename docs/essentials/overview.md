---
date: "2026-07-30 14:00"
title: "Essentials: FluxCD for Platform Engineers"
description: "Running FluxCD as the platform engineer responsible for it, not just the developer downstream of it — starting with deploying real platform services as OCI artifacts."
---

# Essentials

Day One explained GitOps from the developer's side of the fence — you merge, Flux reconciles, you never touch `kubectl apply`. Essentials is written for whoever owns that Flux install: setting it up, deciding what ships as an OCI artifact, and keeping the cluster a *consequence* of the artifact rather than a pile of hand-applied state.

<div class="grid cards two-col" markdown>

-   :material-source-branch: **[Deploy Traefik & cert-manager with Flux OCI Artifacts](deploying_the_edge_stack.md)**

    ---

    Shipping real platform services — Traefik, cert-manager, external-dns — as a versioned OCI artifact and a Flux Kustomization, with zero manual `kubectl` commands.

</div>

More Essentials topics — installing Flux, bootstrapping a cluster, and GitRepositories and Kustomizations from the ground up — are still on the way.

---

Start with **[Deploy Traefik & cert-manager with Flux OCI Artifacts](deploying_the_edge_stack.md)**.
