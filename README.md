# hello-argonaut-gitops

GitOps **config repo** for `hello-argonaut` — the deploy-state (which image runs in which env) lives here, separate from the app code.

## Layout
```
.
├─ Chart.yaml, values.yaml, templates/   # vendored Helm chart
├─ envs/
│  ├─ dev.yaml        # image tag for DEV     (bumped by app CI, auto-merged)
│  └─ staging.yaml    # image tag for STAGING (bumped by an APPROVED promotion PR)
└─ argocd/
   ├─ app-dev.yaml       # Argo Application → hello-argonaut-dev ns (auto, selfHeal off)
   └─ app-staging.yaml   # Argo Application → hello-argonaut-staging ns (promoted)
```

## Flow
- **dev:** app CI opens a PR here bumping `envs/dev.yaml` → auto-merged → Argo deploys dev.
- **staging:** promotion = a PR copying dev's tag into `envs/staging.yaml` → **human-approved** → Argo deploys staging.

Argo CD watches this repo; the app repo (`hello-argonaut`) only builds + pushes images.
