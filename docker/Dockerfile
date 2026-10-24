FROM node:22-bookworm-slim AS base
RUN apt update && apt install --yes --no-install-recommends \
    curl ca-certificates gnupg unzip dumb-init \
    && install -m 0755 -d /etc/apt/keyrings \
    && curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg \
    && chmod a+r /etc/apt/keyrings/docker.gpg \
    && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian $(. /etc/os-release && echo "$VERSION_CODENAME") stable" > /etc/apt/sources.list.d/docker.list \
    && apt update \
    && apt install --yes --no-install-recommends docker-ce-cli docker-compose-plugin \
    && rm -rf /var/lib/apt/lists/* \
    && npm install -g tsx@4.19.3

FROM golang:1.21.4-bookworm AS build_healthcheck
WORKDIR /app
COPY ./extra/healthcheck.go ./extra/healthcheck.go
RUN go build -o ./extra/healthcheck ./extra/healthcheck.go

FROM base AS build
WORKDIR /app
COPY --chown=node:node ./package.json ./package-lock.json ./
RUN npm ci --omit=dev

FROM base AS release
WORKDIR /app
COPY --chown=node:node --from=build_healthcheck /app/extra/healthcheck /app/extra/healthcheck
COPY --chown=node:node --from=build /app/node_modules /app/node_modules
COPY --chown=node:node . .
RUN test -d frontend-dist || (echo "frontend-dist is missing; run npm run build:frontend first" && exit 1)
RUN mkdir ./data
ENV UV_USE_IO_URING=0
VOLUME /app/data
EXPOSE 5001
HEALTHCHECK --interval=60s --timeout=30s --start-period=60s --retries=5 CMD extra/healthcheck
ENTRYPOINT ["/usr/bin/dumb-init", "--"]
CMD ["tsx", "./backend/index.ts"]
