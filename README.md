# infra-secrets

## Dev creation

```
# create cluster
kind create cluster --name staging-vault

# bootstrap flux
flux bootstrap github --context=kind-staging-vault --owner=${GITHUB_ORG} --repository=infra-secrets --branch=main --path=clusters/staging --personal=false --private=false 

```
