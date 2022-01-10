# infra-secrets

## Dev creation

```
# create cluster
kind create cluster --name staging-vault

# bootstrap flux
flux bootstrap github --context=kind-staging-vault --owner=${GITHUB_ORG} --repository=infra-secrets --branch=main --path=clusters/staging --personal=false --private=false 

flux bootstrap github --context=kind-development-vault --owner=${GITHUB_ORG} --repository=infra-secrets --branch=main --path=clusters/development --personal=false --private=false 

```

## Vault commmands

https://learn.hashicorp.com/tutorials/vault/versioned-kv?in=vault/secrets-management

```
# initiate vault
kubectl exec -ti --namespace=vault vault-0 -- vault operator init

# Unseal the first vault server until it reaches the key threshold, 3 of the 5 generated keys
$ kubectl exec -ti --namespace=vault vault-0 -- vault operator unseal MBFSDepD9E6whREc6Dj+k3pMaKJ6cCnCUWcySJQymOb

# login
kubectl exec -ti --namespace=vault vault-0  -- vault login

# list enabled secret engines
kubectl exec -ti --namespace=vault vault-0 -- vault secrets list -detailed

# enable secret engine
kubectl exec -ti --namespace=vault vault-0 -- vault secrets enable -path=secret kv-v2

# store secret
kubectl exec -ti --namespace=vault vault-0 -- vault kv put secret/test Greeting="Hello" Greetee="World"

# add meta data
kubectl exec -ti --namespace=vault vault-0 -- vault kv metadata put -custom-metadata=Language="English" -custom-metadata=Accent="Glaswegian" secret/test 

# get secret
kubectl exec -ti --namespace=vault vault-0 -- vault kv get secret/test

# list secret under secret
kubectl exec -ti --namespace=vault vault-0 -- vault kv list secret/

# delete secret
kubectl exec -ti --namespace=vault vault-0 -- vault kv delete secret/test

# Delete all versions of the secret at the path secret/test
kubectl exec -ti --namespace=vault vault-0 -- vault kv metadata delete secret/test

# Delete specific version (v 4 in this example)
kubectl exec -ti --namespace=vault vault-0 -- vault kv destroy -versions=4 secret/test

# vault status
kubectl exec --namespace=vault vault-0 -- vault status
# iteratate showing status of i instances
for i in {0..2} ; do kubectl exec --namespace=vault vault-$i -- vault status ; done

# helm status
helm status vault

# describe pod
kubectl describe pod --namespace=vault vault-0
```