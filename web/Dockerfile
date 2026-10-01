# Multi-stage: build the React app, then serve it with nginx.
#
# --platform=$BUILDPLATFORM pins the build stage to the host's native arch even when
# cross-building (e.g. amd64 host building an arm64 image). The output is
# arch-independent, and QEMU-emulated npm installs are known to corrupt esbuild/rollup's
# platform-specific native binaries, which breaks `vite build` with unrelated-looking
# module-resolution errors.
FROM --platform=$BUILDPLATFORM node:22-alpine AS build
WORKDIR /app

COPY frontend/package.json frontend/package-lock.json* ./
RUN npm ci 2>/dev/null || npm install

COPY frontend/ ./
RUN npm run build

FROM nginx:alpine

COPY web/nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=build /app/dist /usr/share/nginx/html

# Exercise media is provided through a Docker volume at runtime.
