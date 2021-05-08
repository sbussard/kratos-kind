# kratos-kind

A basic non-persistent setup for [Ory Kratos](https://www.ory.sh/kratos) ([GitHub](https://github.com/ory/kratos))

From their GitHub, Ory Kratos is described as

> Next-gen identity server (think Auth0, Okta, Firebase) with Ory-hardened authentication, MFA, FIDO2, profile management, identity schemas, social sign in, registration, account recovery, service-to-service and IoT auth. Can work as an OAuth2 / OpenID Connect Provider. Golang, headless, API-only - without templating or theming headaches.

## Prerequisites

`brew install kind k9s`

## Create kind cluster

```bash
# Create cluster
kind create cluster --config=kind-cluster-config.yaml
# Install NGINX controller for kind
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/static/provider/kind/deploy.yaml
# Wait for nginx ingress controller to start
kubectl wait --namespace ingress-nginx --for=condition=ready pod --selector=app.kubernetes.io/component=controller --timeout=90s
# Apply app manifests (this is idempotent, so you can make changes and run this command again and the cluster will update itself accordingly)
kubectl apply -R -f manifests
# View k9s dashboard (ctrl + c to exit)
# press 0 to show all resources in k9s
k9s
```

### Testing it out

*Note: you may need to run the patch in the next section to fix a ui bug*

Once the services have the `Running` status then go ahead and open [http://localhost/ui](http://localhost/ui)

## Patch UI bug
There is a bug in the ui portion and until [the PR](https://github.com/ory/kratos-selfservice-ui-node/pull/120/files) is merged, you may need to follow these steps to create a modified version of the ui docker container to get it to work right

1. Inside `kratos-selfservice-ui-node.yaml`, replace `image: oryd/kratos-selfservice-ui-node:v0.6.0-alpha.2` with `image: kratos-custom-ui:latest`
2. Create and load a custom container with the bugfix by running the following commands

```bash
git clone git@github.com:sbussard/kratos-selfservice-ui-node.git
cd kratos-selfservice-ui-node
git checkout patch-1
docker build -t kratos-custom-ui:latest .
kind load docker-image kratos-custom-ui:latest
```

## Delete kind cluster

When you're done just run `kind delete cluster` to scrap it all.
