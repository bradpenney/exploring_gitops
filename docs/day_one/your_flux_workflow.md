---
title: "Your Flux Workflow: Commit, Build, Deploy"
description: "How enterprise GitOps works: your CI pipeline builds a versioned OCI artifact, Flux reads it from your artifact registry, and your cluster reconciles."
---

# Your Flux Workflow

!!! tip "Part of Day One: Understanding GitOps"
    Before reading this article, make sure you've read [What Is GitOps?](what_is_gitops.md). This article builds directly on the reconciliation model explained there.

You merged your PR, a release was cut, and CI ran. Now you're wondering: *did it actually deploy?*

In enterprise GitOps, the answer to that question is not "Flux checked GitHub and applied your YAML." It's something more robust than that.

!!! info "What You'll Learn"
    - Why enterprise GitOps uses OCI artifacts, not direct Git polling
    - The full pipeline from your code commit to a running deployment
    - What your role as a developer actually is (smaller than you think)
    - How to verify your version is live

---

## Why Not Just Poll GitHub?

A common misconception about GitOps is that Flux watches your GitHub repository and applies changes when you merge. Some tutorials teach this pattern. **It is not enterprise-grade.**

Here's why:

<div class="grid cards" markdown>

-   :material-server-off: **Runtime Dependency on a Git Forge**

    ---

    A Git forge is developer-grade infrastructure — built for code review and collaboration, not to sit in the critical path of production reconciliation. Point Flux at GitHub or GitLab and your cluster's ability to deploy now depends on that forge staying up. Forges have outages, and a self-hosted GitHub Enterprise or GitLab instance is often *less* resilient than the managed service — exactly the runtime dependency you don't want under production.

    An artifact registry (Harbor, ECR, Artifactory) is the opposite: enterprise-grade, highly available, and purpose-built to be pulled from continuously. When a forge does go down, already-running workloads keep serving traffic — but nothing new reconciles until it's back. Depend on infrastructure designed for the job.

    **Enterprise standard:** Flux reads from an artifact registry, not a Git forge.

-   :material-tag-off: **No Semantic Versioning**

    ---

    A Git branch is mutable. `main` today is not the same as `main` yesterday. There is no stable, versioned reference to "the manifests that produced version 1.2.3 of this application."

    **Enterprise standard:** OCI artifacts are tagged with semantic versions. `registry/my-app-config:v1.2.3` is immutable. You can audit, roll back to, or promote any specific version by tag.

-   :material-shield-off: **No Enterprise Artifact Management**

    ---

    GitHub is a code forge. Enterprise artifact registries (Harbor, AWS ECR, JFrog Artifactory) provide vulnerability scanning, access control policies, retention rules, artifact signing, and promotion workflows.

    **Enterprise standard:** Kubernetes manifests are artifacts. Treat them like artifacts.

</div>

---

## The Enterprise GitOps Pipeline

In a properly architected GitOps environment, the flow has two halves. First, two repositories — your application code and your GitOps config — each release an artifact to the registry:

```mermaid
flowchart TD
    AppRepo["Application Repo<br/>(your code)"]
    AppCI["CI: build image"]
    Image["Container Image<br/>registry/my-app:v4.7.0"]

    ConfigRepo["GitOps Config Repo<br/>(manifests; may bundle<br/>several microservices)"]
    ConfigCI["CI: package manifests"]
    Artifact["OCI Manifest Artifact<br/>registry/my-app-config:v1.2.3<br/>(declares image v4.7.0)"]

    Registry["Enterprise Registry<br/>(Harbor / ECR / Artifactory)"]

    AppRepo -->|"release"| AppCI
    AppCI --> Image
    Image --> Registry

    ConfigRepo -->|"release"| ConfigCI
    ConfigCI --> Artifact
    Artifact --> Registry

    style AppRepo fill:#2d3748,stroke:#cbd5e0,stroke-width:2px,color:#fff
    style AppCI fill:#2d3748,stroke:#cbd5e0,stroke-width:2px,color:#fff
    style Image fill:#4a5568,stroke:#cbd5e0,stroke-width:2px,color:#fff
    style ConfigRepo fill:#2d3748,stroke:#cbd5e0,stroke-width:2px,color:#fff
    style ConfigCI fill:#2d3748,stroke:#cbd5e0,stroke-width:2px,color:#fff
    style Artifact fill:#4a5568,stroke:#cbd5e0,stroke-width:2px,color:#fff
    style Registry fill:#2f855a,stroke:#cbd5e0,stroke-width:2px,color:#fff
```

Two repos, two pipelines, two artifacts — both landing in the same registry:

- **The container image** — built from your **application repo**, versioned on its own track (say `v4.7.0`)
- **The OCI manifest artifact** — built from your **GitOps config repo**, versioned separately (say `v1.2.3`). The manifests inside declare which image tag to run — `v4.7.0` here. **This artifact tag, not the image tag, is what Flux watches.**

Once both are in the registry, the second half takes over — Flux reconciles the cluster from the OCI manifest artifact:

```mermaid
flowchart TD
    Registry["Enterprise Registry<br/>(Harbor / ECR / Artifactory)"]
    OCIRepo["OCIRepository<br/>(Flux source)"]
    Kustomization["Kustomization<br/>(Flux reconciler)"]
    Cluster["Kubernetes Cluster"]

    Registry -->|"Flux polls on interval<br/>detects new semver tag"| OCIRepo
    OCIRepo --> Kustomization
    Kustomization -->|"applies manifests<br/>from OCI artifact"| Cluster

    style Registry fill:#2f855a,stroke:#cbd5e0,stroke-width:2px,color:#fff
    style OCIRepo fill:#4a5568,stroke:#cbd5e0,stroke-width:2px,color:#fff
    style Kustomization fill:#4a5568,stroke:#cbd5e0,stroke-width:2px,color:#fff
    style Cluster fill:#1a202c,stroke:#cbd5e0,stroke-width:2px,color:#fff
```

Flux watches the manifest artifact via an [`OCIRepository`](https://fluxcd.io/flux/components/source/ocirepositories/) resource, which references a tag or a semver policy. When a matching version is available, Flux fetches the artifact and applies the manifests to the cluster. **Flux never connects to GitHub at runtime.**

!!! warning "Production Pins, It Doesn't Float"
    A loose semver range like `>=1.2.0` will pull the newest matching artifact — fine for a dev or staging environment, but **not** how production should work. Production environments pin to a specific, tested version (e.g. `v1.2.3`) and are moved forward deliberately through promotion. Production never chases "latest."

---

## Your Role as a Developer

In an enterprise GitOps environment, your workflow is deliberately minimal:

```mermaid
flowchart LR
    Write["Write code<br/>or config change"]
    PR["Open a PR<br/>in your app repo"]
    Review["Teammate reviews<br/>and approves"]
    Merge["Merge to main"]
    Done["Release cut; CI builds<br/>and publishes the artifact"]

    Write --> PR --> Review --> Merge --> Done

    style Write fill:#2d3748,stroke:#cbd5e0,stroke-width:2px,color:#fff
    style PR fill:#2d3748,stroke:#cbd5e0,stroke-width:2px,color:#fff
    style Review fill:#4a5568,stroke:#cbd5e0,stroke-width:2px,color:#fff
    style Merge fill:#4a5568,stroke:#cbd5e0,stroke-width:2px,color:#fff
    style Done fill:#2f855a,stroke:#cbd5e0,stroke-width:2px,color:#fff
```

You do not:

- Push to the cluster directly
- Edit image tags in a config repo
- Trigger deployments manually
- Touch the OCI artifact or the registry

Your CI pipeline does all of that. **Your job ends at the merge.**

!!! note "Who Ships It to Production? (Segregation of Duties)"
    Your merge builds and publishes the artifact, and lower environments like dev and staging may reconcile to it automatically. Promoting it to **production**, though, is usually a *different person's* job — a release manager or the on-call SRE (site reliability engineer) advances the version Flux deploys to prod. The developer who wrote the change doesn't ship it there themselves. That separation is **segregation of duties**: a standard enterprise control, and often a compliance requirement.

!!! tip "Where Do the Manifests Live?"
    We recommend **two repositories**: your **application repo** (the code) and a separate **GitOps config repo** (your Kubernetes manifests and Flux definitions). Keeping them apart means one config repo can bundle the manifests for *several* microservices, and config changes follow their own PR and release flow — independent of any single app's code. Releasing from the config repo is what produces the next versioned OCI manifest artifact.

---

## What Gets Versioned

The OCI manifest artifact carries its own semantic version — and that artifact tag is the version Flux watches. A tag like `v1.2.3` on the manifest artifact means:

- The manifests declare a specific container image — `registry/my-app:v4.7.0` — which is versioned on its own track and need not match the artifact's tag
- The replica count, resource limits, ConfigMap values, and everything else in the manifests are exactly what was on `main` when `v1.2.3` was built
- This combination has been tested in CI
- This exact state can be deployed, rolled back to, or promoted to production by referencing the artifact tag `v1.2.3`

This is the version your SREs will reference in incident reports, deployment logs, and change management records.

---

## Rolling Back

In this model, a rollback is an operation your on-call SRE can perform without touching Git at all — by pinning the `OCIRepository` to an earlier semver tag. For you as a developer, a rollback might also be initiated by reverting your commit and letting CI build a new `v1.2.4` that looks like `v1.2.1`.

Either way: you don't reach for `kubectl rollout undo`. The artifact registry is the source of truth for what version should be running.

---

## Practice Exercises

??? question "Exercise 1: Why Not Git?"
    Your colleague suggests: "Instead of all this OCI artifact complexity, why don't we just have Flux watch the `main` branch of our GitHub repo directly? It's simpler."

    **What are the three strongest arguments against this in an enterprise environment?**

    ??? tip "Solution"
        1. **Runtime dependency on GitHub.** If GitHub has an outage, Flux cannot reconcile. New deployments and config changes stall until GitHub returns. An enterprise registry (Harbor, ECR, Artifactory) is purpose-built for high availability and does not carry this risk.

        2. **No semantic versioning.** A branch is mutable — `main` today is not the same as `main` last week. You cannot pin, audit, or roll back to a specific version of "what Flux was applying on Tuesday." An OCI artifact at `v1.2.3` is immutable and permanently auditable.

        3. **No artifact management.** Enterprise registries provide vulnerability scanning, signing, access control, and retention policies. GitHub provides none of these for your Kubernetes manifests.

??? question "Exercise 2: Trace the Pipeline"
    Your team merges a PR that changes the log level in a ConfigMap from `info` to `debug`.

    **Trace the full pipeline from merge to cluster update. What produces the new OCI artifact? What does it contain? How does Flux detect it?**

    ??? tip "Solution"
        1. **PR merges** to `main` in the application repo
        2. **A release is cut** — tagging the new version (e.g., `v2.4.1`); this is what triggers CI
        3. **CI packages** the updated manifests (including the ConfigMap with `LOG_LEVEL: debug`) as an OCI artifact tagged `v2.4.1` and pushes it to the enterprise registry
        4. **Flux's OCIRepository** polls the registry on its interval (e.g., every 5 minutes), detects `v2.4.1` satisfies its semver policy (`>=2.0.0`), and fetches the artifact
        5. **Flux's Kustomization** compares the fetched manifests to the cluster state, finds the ConfigMap differs, and applies the update
        6. **Cluster updated** — the ConfigMap now contains `LOG_LEVEL: debug`

??? question "Exercise 3: Who Owns the Rollback?"
    A deployment of `v3.1.0` is causing elevated error rates. The on-call SRE needs to roll back to `v3.0.8` immediately.

    **In this OCI-based GitOps setup, who performs the rollback and how?**

    ??? tip "Solution"
        The **on-call SRE** performs the rollback — not the application developer.

        They pin the `OCIRepository` to `v3.0.8` by updating the semver constraint or specifying the tag directly. Flux immediately reconciles to the `v3.0.8` manifests from the artifact registry. No Git operations required for the emergency rollback.

        The **application developer** may then open a PR to fix the issue in code, which CI builds as `v3.1.1` — superseding the broken `v3.1.0` and restoring forward momentum.

        This separation — the SRE owns the rollback, the dev team owns the fix — is a feature of the enterprise pattern, not a limitation.

---

## Quick Recap

| Question | Answer |
|----------|--------|
| What does Flux poll in production? | An enterprise OCI artifact registry — not GitHub |
| What is the deployment artifact? | An OCI image containing versioned Kubernetes manifests |
| What is your job as a developer? | Commit code, open PR, merge — CI and Flux handle the rest |
| How are deployments versioned? | Semantic version tags on the OCI artifact (`v1.2.3`) |
| How do you roll back? | The on-call SRE pins the `OCIRepository` to an earlier semver tag |

---

## What's Next

You understand how your changes flow from code to cluster. The next question is: **how do you verify it worked?**

**[Reading Flux Status](reading_flux_status.md)** covers the `kubectl` commands that tell you whether Flux reconciled your OCI artifact successfully — and how to interpret the errors when it didn't.

---

## Further Reading

### Official Documentation

- [FluxCD: OCIRepository](https://fluxcd.io/flux/components/source/ocirepositories/) — the Flux source type that watches your artifact registry; covers the full spec, authentication, and status fields

### The Alternative

- [ArgoCD Documentation](https://argo-cd.readthedocs.io/en/stable/) — the direct competitor to FluxCD; same GitOps principles, different architecture

### Related Learning

- [Essential kubectl Commands](https://k8s.bradpenney.io/day_one/kubectl/commands/) — `kubectl get` and `kubectl describe` for verifying changes applied after a reconciliation
- [Your First Helm Deployment](https://k8s.bradpenney.io/day_one/helm/first_deploy/) — New to Helm? Helm charts can also be packaged and delivered as OCI artifacts

### Related Articles

- [What Is GitOps?](what_is_gitops.md) — the paradigm behind everything in this article
- [Day One Overview](overview.md) — the full Day One learning path
