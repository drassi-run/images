# syntax=docker/dockerfile:1
FROM ubuntu:26.04
LABEL maintainer="dev@drassi.run"
SHELL ["/bin/bash", "-c"]

RUN --mount=type=cache,target=/var/cache/apt,sharing=locked \
    --mount=type=cache,target=/var/lib/apt/lists,sharing=locked \
    set -eux; \
    apt-get update; \
    apt-get install -y --no-install-recommends \
      sudo gnupg openssl openssh-client curl wget time tini \
      tzdata locales ca-certificates apt-transport-https \
      binutils dnsutils findutils tree jq \
      iproute2 iputils-ping telnet netcat-openbsd net-tools \
      tar gzip zip unzip 7zip 7zip-rar
      # libbrotli1 libbz2-1.0 libdeflate0 liblz4-1 liblzma5 libsnappy1v5 libzstd1 zlib1g
      # brotli bzip2 lz4 xz-utils snappy-tools zstd

# Install & configure git
RUN --mount=type=cache,target=/var/cache/apt,sharing=locked \
    --mount=type=cache,target=/var/lib/apt/lists,sharing=locked \
    set -eux; \
    curl -sSL "https://packagecloud.io/install/repositories/github/git-lfs/script.deb.sh" | bash; \
    apt-get update; \
    apt-get install -y --no-install-recommends git git-lfs; \
    git --version; \
    git config --system --add safe.directory '*'

# Install make/gcc used to compile C/C++ code in many projects
RUN --mount=type=cache,target=/var/cache/apt,sharing=locked \
    --mount=type=cache,target=/var/lib/apt/lists,sharing=locked \
    set -eux; \
    apt-get update; \
    apt-get install -y --no-install-recommends build-essential; \
    gcc --version

# Install docker (client + server), which used by
# * job container & services: https://docs.github.com/en/actions/writing-workflows/workflow-syntax-for-github-actions#jobsjob_idcontainer
# * Docker action: https://docs.github.com/en/actions/sharing-automations/creating-actions/creating-a-docker-container-action
# Installation references:
# * https://docs.docker.com/engine/install/ubuntu/
# * https://get.docker.com
RUN --mount=type=cache,target=/var/cache/apt,sharing=locked \
    --mount=type=cache,target=/var/lib/apt/lists,sharing=locked \
    set -eux; \
    source /etc/os-release; \
    curl -fsSL "https://download.docker.com/linux/ubuntu/gpg" -o "/etc/apt/keyrings/docker.asc"; \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu ${UBUNTU_CODENAME} stable" \
      > "/etc/apt/sources.list.d/docker.list"; \
    apt-get update; \
    apt-get install -y --no-install-recommends \
      docker-ce containerd.io docker-ce-cli docker-compose-plugin docker-buildx-plugin; \
    docker -v

ENTRYPOINT ["/usr/bin/tini", "--"]
CMD ["/usr/bin/dockerd"]
