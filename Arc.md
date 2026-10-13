helm upgrade --install arc `
  --namespace arc-systems `
  --create-namespace `
  oci://ghcr.io/actions/actions-runner-controller-charts/gha-runner-scale-set-controller `
  --wait `
  --timeout 5m

export GITHUB_CONFIG_URL="https://github.com/OWNER/REPOSITORY"

kubectl create namespace arc-runners

kubectl create secret generic arc-github-auth \
  -n arc-runners \
  --from-literal=github_token="$(read -rsp 'GitHub PAT: ' PAT; echo; echo -n "$PAT")"

helm upgrade --install arc-runner-set \
  --namespace arc-runners \
  --set-string githubConfigUrl="$GITHUB_CONFIG_URL" \
  --set-string githubConfigSecret=arc-github-auth \
  --set minRunners=0 \
  --set maxRunners=2 \
  oci://ghcr.io/actions/actions-runner-controller-charts/gha-runner-scale-set \
  --wait --timeout 5m