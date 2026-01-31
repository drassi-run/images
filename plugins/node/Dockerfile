# syntax=docker/dockerfile:1
FROM --platform=linux/amd64 ubuntu:24.04 AS builder
LABEL maintainer="dev@drassi.run"
SHELL ["/bin/bash", "-c"]
WORKDIR /build

RUN --mount=type=cache,target=/var/cache/apt,sharing=locked \
    --mount=type=cache,target=/var/lib/apt/lists,sharing=locked \
<<EOF
  apt-get update
  apt-get install -y --no-install-recommends \
    curl ca-certificates \
    tar xz-utils
EOF

ARG NODE_VERSION=24.13.0
ARG TARGETARCH
RUN <<EOF
  set -eux
  case "$TARGETARCH" in
    amd64)  NODE_ARCH="x64" ;;
    arm64)  NODE_ARCH="arm64" ;;
    *)
      echo "unsupported arch: $TARGETARCH"
      exit 1 ;;
  esac
  curl -fsSL "https://nodejs.org/dist/v${NODE_VERSION}/node-v${NODE_VERSION}-linux-${NODE_ARCH}.tar.xz" -o "node.tar.xz"
  tar -xvf "node.tar.xz" --no-same-owner
  mv "./node-v${NODE_VERSION}-linux-${NODE_ARCH}" ./node
EOF

FROM scratch
COPY --from=builder /build/node /

### Smoke test ###
# docker run -it --rm --mount type=image,source=drassi/node:24,target=/opt/node,readonly -w /opt/node fedora:44 bash
