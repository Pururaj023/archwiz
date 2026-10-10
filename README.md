# Game Picks

A small game recommendation site made of five independent pages. Each page is its own container, has its own GitHub Actions pipeline and its own Kubernetes deployment, running on a local minikube cluster.

```
shared/style.css            shared styles, copied into every image
pages/<page>/index.html     the page
pages/<page>/Dockerfile     nginx image for that page
pages/<page>/k8s/deployment.yaml   Deployment + Service
.github/workflows/<page>.yml       build + push to Docker Hub, deploy to minikube
k8s/ingress.yaml            one-time routing for all pages
```

Pages: `home`, `action`, `rpg`, `strategy`, `indie`.

Images: `pururajchoudhary23/gamepicks-<page>` on Docker Hub.

## How the pipeline works

1. **build** (GitHub cloud runner): builds the image and pushes it to Docker Hub using the `DOCKER_TOKEN` secret.
2. **deploy** (self-hosted runner on your machine): runs `kubectl apply` against your local minikube and waits for the rollout.

The deploy job must run on your own machine because GitHub's cloud runners cannot reach a minikube cluster on localhost.

## One-time setup

1. Repo secret: `DOCKER_TOKEN` (Docker Hub access token). Secret names are case-insensitive, so `docker_token` also works.
2. Make the `gamepicks-*` repositories **public** on Docker Hub so minikube can pull them without a pull secret. They are created on the first push.
3. Self-hosted runner: GitHub repo > Settings > Actions > Runners > New self-hosted runner, and follow the steps on the machine that runs minikube. `kubectl` must be installed there and minikube must be running (`minikube start`).
4. Ingress:
   ```bash
   minikube addons enable ingress
   kubectl apply -f k8s/ingress.yaml
   ```
5. Hosts file entry so `gamepicks.local` works:
   - Linux / macOS (Docker-less drivers): `echo "$(minikube ip) gamepicks.local" | sudo tee -a /etc/hosts`
   - Docker driver on macOS / Windows: use `127.0.0.1 gamepicks.local` in the hosts file and keep `minikube tunnel` running in a terminal.

Then open http://gamepicks.local/ .

## Quick check without ingress

```bash
minikube service gamepicks-home --url
```

## Try one page locally

```bash
docker build -f pages/home/Dockerfile -t gamepicks-home .
docker run --rm -p 8080:8080 gamepicks-home
```

A change under `pages/<page>/` only runs that page's pipeline. A change under `shared/` runs all five.
