# github-action-docker-buildx-build

## Synopsis

A GitHub Action for invoking `docker buildx build`.

## Overview

The GitHub Action performs:

1. [docker/setup-qemu-action@v3](https://github.com/docker/setup-qemu-action)
1. [docker/setup-buildx-action@v3](https://github.com/docker/setup-buildx-action)
1. Logs into a Docker registry (`docker.io` being the default)
1. Performs `docker buildx build`...

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
              uses: Senzing/github-action-docker-buildx-build@latest
              with:
                  image-repository: senzing/test-ground
                  image-tag: ${{ github.ref_name }}
                  password: ${{ secrets.DOCKERHUB_PASSWORD }}
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
              uses: Senzing/github-action-docker-buildx-build@latest
              with:
                  build-options: "--push"
                  image-repository: senzing/test-ground
                  image-tag: ${{ github.ref_name }}
                  password: ${{ secrets.DOCKERHUB_PASSWORD }}
                  username: ${{ secrets.DOCKERHUB_USERNAME }}
    ```

   Notice the addition of the `--push` build option.
   Build options are inserted into the `docker buildx build` command.

## Inputs

See also `inputs:` section of [action.yaml](action.yaml)

### build-options

Command line arguments passed directly to `docker buildx build`

- Optional parameter
- Default: "" (no build options)

### context

**Optional:**
The `docker` command's build context.
See [Description](https://docs.docker.com/engine/reference/commandline/build/#description).

Default: "."

### image-repository

**Required:**
The identifier of the Docker image.
Example: `senzing/senzingapi-runtime`

### image-tag

**Optional:**
The tag appended to the Docker image identifier.
Example:  the `1.2.3` in `senzing/senzingapi-runtime:1.2.3`

Default: "latest"

### password

**Required:**
The password for the user on the Docker registry server.

### platforms

**Optional:**
The comma-separated list of platforms to build on.
To find candidates, run `docker buildx ls`

Default: "linux/amd64"

### registry-server

**Optional:**
The Docker registry server.

Default: `docker.io`

### username

**Required:**
The username on the Docker registry server.
