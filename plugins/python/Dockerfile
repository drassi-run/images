# syntax=docker/dockerfile:1
ARG UV_VERSION=0.9

FROM astral/uv:$UV_VERSION-debian-slim AS builder
WORKDIR /build
SHELL ["/bin/bash", "-c"]
ENV UV_PYTHON_INSTALL_DIR=/build

# https://github.com/astral-sh/python-build-standalone
ARG PYTHON_VERSION=3.12
RUN uv python install "$PYTHON_VERSION"
RUN mv ./cpython-$PYTHON_VERSION* ./python

FROM scratch
COPY --from=builder /build/python /

### Smoke test ###
# docker run -it --rm --mount type=image,source=drassi/python:3.12,target=/opt/python,readonly -w /opt/python fedora:44 bash
