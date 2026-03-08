# README

A github shared workflow to build a set of container images and refresh them only when real changes happen

## Usage example

Add to your project .github/workflows/containers.yml
```yaml
name: Build
on:
  schedule:
    - cron: '0 8 * * *'

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

permissions:
  contents: read
  packages: write
  id-token: write
  
jobs:
  build:
    uses: kfox1111/container-build-and-refresh-action/.github/workflows/containers.yml@main
```

Add a containers directory. Make a subdirectory for each container image you want, and add a Dockerfile to each.

It will output images in `ghcr.io/<yourorg>/<yourrepo>/<containername>`

By default, it will produce a latest tag that will point at a multiarch index to the most recent builds.

It will produce immutable multiarch images with a tag of the timestamp built on. It also produces individual immutable images tagged with the architecture.

example tags:
```
latest
latest-linux-arm64
latest-linux-amd64
20260302-012712
20260302-011508
linux-arm64-20260302-012712
linux-amd64-20260302-012712
linux-arm64-20260302-011508
linux-amd64-20260302-011508
```

## Container Image Configuration

Add a yaml file called `build.yaml` in the root directory of the container.

### Dependencies

You can make make one container depend on others in the dependencies section of the `build.yaml`

Example that makes sure container `a` and `b` are built first:
```
dependencies:
- a
- b
```

### Build arguments

You can add a dictionary of build arguments in the buildArgs section. They will all be added with --build-arg to the final build.

Example `build.yaml:
```
buildArgs:
  FOO: b
```

Dockerfile
```
FROM centos:centos9
ARG FOO=a
RUN echo $FOO
```

