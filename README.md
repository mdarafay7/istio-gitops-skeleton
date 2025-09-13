# Istio Labs via Argo CD — GitOps Skeleton (Minikube)

This repo lets you run the Istio hands-on labs A–F purely via GitOps.

## Quick start
1) Create a GitHub repo (private or public) named `istio-gitops`.
2) Upload these files, then edit **environments/minikube/01-root-app.yaml**:
   - Set `spec.source.repoURL` to your repo URL.
3) Install Argo CD and apply the root app:
   ```bash
   kubectl create ns argocd
   kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
   kubectl -n argocd port-forward svc/argocd-server 8080:443
   kubectl apply -f environments/minikube/01-root-app.yaml
   sudo -E minikube tunnel   # needed for istio-ingressgateway LoadBalancer
   ```

## Fill in the sample content
- **platform/addons/** files are placeholders. Copy the real manifests from your local istio release:
  `istio-1.23.0/samples/addons/*.yaml` into `platform/addons/` (replace the placeholder files).
- **apps/bookinfo/** files are placeholders. Copy:
  - `istio-1.23.0/samples/bookinfo/platform/kube/bookinfo.yaml` → `apps/bookinfo/bookinfo.yaml`
  - `istio-1.23.0/samples/bookinfo/networking/bookinfo-gateway.yaml` → `apps/bookinfo/gateway.yaml`

After you paste those, Argo will deploy addons and Bookinfo correctly. The lab folders already contain working Istio CRDs for each exercise.

## Lab order
- Istio base → istiod → ingress (sync waves)
- Addons, then Bookinfo
- Labs A–F (enable by syncing each App in Argo CD)

## Troubleshooting
- No LoadBalancer IP? Run `sudo -E minikube tunnel`.
- Sidecars not injected? `kubectl label ns default istio-injection=enabled --overwrite` and restart pods.
- No traces? Open Kiali & Jaeger via `istioctl dashboard kiali/jaeger` and generate traffic for ~30–60s.
