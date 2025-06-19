# github-action-docker-buildx-build

## Synopsis

A GitHub Action for invoking `docker buildx build`.

## Overview

The GitHub Action performs:

1. [actions/checkout@v4]
1. [docker/setup-qemu-action@v3]
1. [docker/setup-buildx-action@v3]
1. [docker/login-action@v3] \
   Logs into a Docker registry (`docker.io` being the default)
   Can be skipped with `login-to-dockerhub` input
1. [aws-actions/configure-aws-credentials@v4]
   Configure credentials for ECR registry login
   Skipped by default with `login-to-ecr`
1. [aws-actions/amazon-ecr-login@v2]
   Logs into a public ECR registry
   Skipped by default with `login-to-ecr`
1. [docker/build-push-action@v6]
1. [anchore/sbom-action@v0.18.0]
   Skipped by default with `sign-image`
1. [actions/attest-build-provenance@v2]
   Skipped by default with `sign-image`
1. [actions/attest-sbom@v1]
   Skipped by default with `sign-image`
1. [sigstore/cosign-installer@v3]

## Usage

1. An example `.github/workflows/docker-build-container.yaml` file
   which verifies that the Docker image builds successfully:

   ```yaml
   name: docker-build-container.yaml

   on: [push]

   jobs:
     docker-build-container:
       runs-on: ubuntu-latest
       steps:
         - name: Build docker image
           uses: Senzing/github-action-docker-buildx-build@v2
           with:
             image-repository: senzing/test-ground
             image-tag: ${{ github.ref_name }}
             password: ${{ secrets.DOCKERHUB_ACCESS_TOKEN }}
             username: ${{ secrets.DOCKERHUB_USERNAME }}
   ```

1. An example `.github/workflows/docker-push-container-to-dockerhub.yaml` file
   which builds Docker images and pushes them to DockerHub:

   ```yaml
   name: docker-push-container-to-dockerhub.yaml

   on:
     push:
       tags:
         - "[0-9]+.[0-9]+.[0-9]+"

   jobs:
     docker-push-containers-to-dockerhub:
       runs-on: ubuntu-latest
       steps:
         - name: Build docker image and push to DockerHub
           uses: Senzing/github-action-docker-buildx-build@v2
           with:
             image-repository: senzing/test-ground
             image-tag: ${{ github.ref_name }}
             password: ${{ secrets.DOCKERHUB_ACCESS_TOKEN }}
             push: true
             username: ${{ secrets.DOCKERHUB_USERNAME }}
   ```

   Notice the addition of the `push` input.
   Used by the `docker/build-push-action` action.

1. An example `.github/workflows/docker-push-container-to-dockerhub.yaml` file
   which builds Docker images and pushes them to DockerHub with build-args:

   ```yaml
   name: docker-push-container-to-dockerhub.yaml

   on:
     push:
       tags:
         - "[0-9]+.[0-9]+.[0-9]+"

   jobs:
     docker-push-containers-to-dockerhub:
       runs-on: ubuntu-latest
       steps:
         - name: Build docker image and push to DockerHub
           uses: Senzing/github-action-docker-buildx-build@v2
           with:
             build-options: |
               SENZING_ACCEPT_EULA=I_ACCEPT_THE_SENZING_EULA
               ACCEPT_EULA=Y
             image-repository: senzing/test-ground
             image-tag: ${{ github.ref_name }}
             password: ${{ secrets.DOCKERHUB_ACCESS_TOKEN }}
             push: true
             username: ${{ secrets.DOCKERHUB_USERNAME }}
   ```

   Notice the addition of the `build-options` input.
   Used by the `docker/build-push-action` action `build-args` input.

1. An example `.github/workflows/docker-push-container-to-dockerhub.yaml` file
   which builds Docker images, pushes them to DockerHub, and adds signing and attestations:

   ```yaml
   name: docker-push-container-to-dockerhub.yaml

   on:
     push:
       tags:
         - "[0-9]+.[0-9]+.[0-9]+"

   jobs:
     docker-push-containers-to-dockerhub:
       runs-on: ubuntu-latest
       steps:
         - name: Build docker image and push to DockerHub
           uses: Senzing/github-action-docker-buildx-build@v2
           with:
             image-repository: senzing/test-ground
             image-tag: ${{ github.ref_name }}
             password: ${{ secrets.DOCKERHUB_ACCESS_TOKEN }}
             push: true
             sign-image: true
             username: ${{ secrets.DOCKERHUB_USERNAME }}
   ```

## Inputs

See also `inputs:` section of [action.yaml]

### aws-region

- Optional parameter
- Default: "us-east-1"
- Only needed if using ECR login.

### build-options

Command-line arguments passed directly to `docker buildx build`

- Optional parameter
- Default: "" (no build options)
- Note: Parameters should be provided as a List.
  List type is a newline-delimited string

### context

The `docker` command's build context.
See [Description].

- Optional parameter
- Default: "."

### dockerfile-path

Path to the Dockerfile.
Hint: If you have overridden the default context you likely need to set this.

- Optional parameter
- Default: "./Dockerfile"

### image-repository

The identifier of the Docker image.

- Required parameter
- Example: `senzing/senzingapi-runtime`

### image-tag

The tag appended to the Docker image identifier.
Example: the `1.2.3` in `senzing/senzingapi-runtime:1.2.3`

- Optional parameter
- Default: "latest"

### login-to-dockerhub

- Optional parameter
- Default: true

### login-to-ecr

- Optional parameter
- Default: false
- Only needed if using ECR login.

### password

The access token or password for the user on the Docker registry server. \
It is recommended to use an [access token for login]. \
Refer to the respective registry provider documentation for additional login details.

### platforms

The comma-separated list of platforms to build on.
To find candidates, run `docker buildx ls`

- Optional parameter
- Default: "linux/amd64,linux/arm64"

### push

- Optional parameter
- Default: false

### registry-server

The Docker registry server.

- Optional parameter
- Default: `docker.io`

### role-session-name

- Optional parameter
- Only needed if using ECR login.
- [See Configure AWS Credential action documentation for additional details]

### role-to-assume

- Optional parameter
- Only needed if using ECR login.
- [See Configure AWS Credential action documentation for additional details]

### sign-image

- Optional parameter
- Only needed for signing and adding attestations. Should be limited to tag builds.

### username

The username on the Docker registry server.

[action.yaml]: action.yaml
[actions/attest-build-provenance@v2]: https://github.com/actions/attest-build-provenance
[actions/attest-sbom@v1]: https://github.com/actions/attest-sbom
[actions/checkout@v4]: https://github.com/actions/checkout
[access token for login]: https://github.com/docker/login-action#docker-hub
[anchore/sbom-action@v0.18.0]: https://github.com/anchore/sbom-action
[aws-actions/amazon-ecr-login@v2]: https://github.com/aws-actions/amazon-ecr-login
[aws-actions/configure-aws-credentials@v4]: https://github.com/aws-actions/configure-aws-credentials
[Description]: https://docs.docker.com/engine/reference/commandline/build/#description
[docker/build-push-action@v6]: https://github.com/docker/build-push-action
[docker/login-action@v3]: https://github.com/docker/login-action
[docker/setup-buildx-action@v3]: https://github.com/docker/setup-buildx-action
[docker/setup-qemu-action@v3]: https://github.com/docker/setup-qemu-action
[See Configure AWS Credential action documentation for additional details]: https://github.com/aws-actions/configure-aws-credentials?tab=readme-ov-file#using-this-action
[sigstore/cosign-installer@v3]: https://github.com/sigstore/cosign-installer
