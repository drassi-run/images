# syntax=docker/dockerfile:1
ARG UV_VERSION=0.12

FROM astral/uv:$UV_VERSION-debian-slim AS builder
WORKDIR /build
SHELL ["/bin/bash", "-c"]

# https://github.com/astral-sh/python-build-standalone
ARG PYTHON_VERSION=3.12
# > uv python dir
ENV UV_PYTHON_INSTALL_DIR=/build
RUN uv python install "$PYTHON_VERSION"
RUN set -eux; \
    # remove all symlinks
    find . -maxdepth 1 -type l -delete; \
    mv ./cpython-$PYTHON_VERSION* ./python; \
    # remove marker
    rm -rf "./python/lib/python${PYTHON_VERSION}/EXTERNALLY-MANAGED";

FROM scratch
COPY --from=builder /build/python /

### Smoke test ###
# docker run -it --rm --mount type=image,source=drassi/python:3.12,target=/opt/python,readonly -w /opt/python fedora:44 bash
