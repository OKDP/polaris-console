[![ci](https://github.com/okdp/polaris-console/actions/workflows/ci.yml/badge.svg)](https://github.com/okdp/polaris-console/actions/workflows/ci.yml)
[![Release](https://img.shields.io/github/v/release/okdp/polaris-console)](https://github.com/okdp/polaris-console/releases/latest)&ensp;&ensp;
[![License Apache2](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](http://www.apache.org/licenses/LICENSE-2.0)
<a href="https://okdp.io">
<img src="https://okdp.io/logos/okdp-notext.svg" height="20px" style="margin: 0 2px;" />
</a>

## Overview

This repository provides a Docker and Helm packaging for Apache Polaris Console, mainly for showcase and deployment scenarios.

It is not a fork of [apache/polaris-tools](https://github.com/apache/polaris-tools), it derives selected packaging assets from the upstream Apache Polaris Console source.

If suitable upstream Docker images or Helm charts become available, this repository may switch to consuming them directly or refactor the local packaging.


## Upstream

Apache Polaris Console source:

```text
https://github.com/apache/polaris-tools/tree/main/console
```

The Helm chart was originally derived from:

```text
https://github.com/apache/polaris-tools/tree/main/console/helm
```

## Docker

Run the console:

```bash
docker run --rm -p 8080:80 quay.io/okdp/polaris-console:v0.1.0
```

Configure the Polaris API endpoint:

```bash
docker run --rm -p 8080:80 \
  -e VITE_POLARIS_API_URL="http://polaris:8181" \
  -e VITE_POLARIS_REALM="POLARIS" \
  -e VITE_POLARIS_PRINCIPAL_SCOPE="PRINCIPAL_ROLE:ALL" \
  -e VITE_OAUTH_TOKEN_URL="http://polaris:8181/api/catalog/v1/oauth/tokens" \
  quay.io/okdp/polaris-console:v0.1.0
```

Open:

```text
http://localhost:8080
```

## Helm

Install from this repository:

```bash
helm install polaris-console ./helm/polaris-console
```

Install with an image override:

```bash
helm install polaris-console ./helm/polaris-console \
  --set image.repository=quay.io/okdp/polaris-console \
  --set image.tag=v0.1.0
```

Use a values file:

```bash
helm upgrade --install polaris-console ./helm/polaris-console \
  -f values.local.yaml
```

## Configuration

Chart values are documented in
[`helm/polaris-console/README.md`](helm/polaris-console/README.md).

Example values:

```yaml
image:
  repository: quay.io/okdp/polaris-console
  tag: v0.1.0

env:
  polarisApiUrl: "http://polaris:8181"
  polarisRealm: "POLARIS"
  polarisPrincipalScope: "PRINCIPAL_ROLE:ALL"
  oauthTokenUrl: "http://polaris:8181/api/catalog/v1/oauth/tokens"
```

Additional environment variables can be passed with `extraEnv`:

```yaml
extraEnv:
  VITE_POLARIS_API_URL: "http://polaris:8181"
  VITE_POLARIS_REALM: "POLARIS"
  VITE_POLARIS_PRINCIPAL_SCOPE: "PRINCIPAL_ROLE:ALL"
```

OIDC example:

```yaml
image:
  repository: quay.io/okdp/polaris-console
  tag: v0.1.0

extraEnv:
  VITE_POLARIS_API_URL: "http://polaris:8181"
  VITE_POLARIS_REALM: "POLARIS"
  VITE_POLARIS_PRINCIPAL_SCOPE: "PRINCIPAL_ROLE:ALL"
  VITE_OIDC_ISSUER_URL: "http://localhost:8080/realms/EXTERNAL"
  VITE_OIDC_CLIENT_ID: "polaris-console"
  VITE_OIDC_REDIRECT_URI: "http://localhost:5173/auth/callback"
  VITE_OIDC_SCOPE: "openid profile email"
```

## Development

Create a branch for your changes:

```bash
git checkout -b feature/my-change
```

Useful local checks:

```bash
helm lint helm/polaris-console
helm template polaris-console helm/polaris-console
helm-docs -c helm
```

Run the CI workflow locally with [act](https://github.com/nektos/act):

```bash
act --container-architecture linux/amd64 \
  -W .github/workflows/ci.yml \
  --env GITHUB_REPOSITORY_OWNER=OKDP \
  --secret GITHUB_TOKEN=<token>
```

CI builds the Docker image, runs Helm chart checks, and regenerates Helm docs.
Release workflows publish tagged Docker and Helm artifacts when releases are
created.

## Licensing And Notices

The root `LICENSE` and `NOTICE` files apply to this OKDP packaging repository.

Upstream Apache Polaris Console legal files are preserved under:

```text
licenses/apache-polaris-console/LICENSE
licenses/apache-polaris-console/LICENSE-BUNDLE
licenses/apache-polaris-console/NOTICE
```

## Trademark And Non-Affiliation

Apache Polaris and Apache are trademarks of The Apache Software Foundation.

This project is not affiliated with, endorsed by, or sponsored by The Apache
Software Foundation.
