# kratos-kind

A basic non-persistent setup for [Ory Kratos](https://www.ory.sh/kratos) ([GitHub](https://github.com/ory/kratos))

From their GitHub, Ory Kratos is described as

> Next-gen identity server (think Auth0, Okta, Firebase) with Ory-hardened authentication, MFA, FIDO2, profile management, identity schemas, social sign in, registration, account recovery, service-to-service and IoT auth. Can work as an OAuth2 / OpenID Connect Provider. Golang, headless, API-only - without templating or theming headaches.

## Prerequisites

These instructions are for macOS but can easily be adapted to windows or linux using the package manager of your choice.

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

Once the services have the `Running` status then go ahead and open [http://localhost/ui](http://localhost/ui)

## Delete kind cluster

When you're done just run `kind delete cluster` to scrap it all.
