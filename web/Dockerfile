# Multi-stage: build the React app, then serve it with nginx.
#
# Deliberately plain FROM: the classic builder (Docker/Compose without buildx, which is
# what a Synology NAS runs) cannot expand $BUILDPLATFORM and fails with
# "failed to parse platform". $BUILDPLATFORM only matters when cross-building (e.g. amd64
# host producing an arm64 image) to avoid QEMU-emulated npm installs corrupting
# esbuild/rollup's native binaries. Self-hosters build on the machine that runs the
# image, so the flag is unnecessary here — buildx users can opt in via
# `--build-arg`/compose `platforms` instead.
FROM node:26-alpine AS build
WORKDIR /app

COPY frontend/package.json frontend/package-lock.json* ./
RUN npm ci 2>/dev/null || npm install

COPY frontend/ ./
RUN npm run build

FROM nginx:alpine

COPY web/nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=build /app/dist /usr/share/nginx/html

# Exercise media is provided through a Docker volume at runtime.
