# Build the manager binary
FROM golang:1.25.4-alpine3.22 AS builder

# Install minimal build deps
RUN apk add --no-cache git

WORKDIR /workspace

# Clone & build MinIO Object Browser
RUN git clone --depth=1 --branch v1.7.6 https://github.com/minio/object-browser.git

COPY go.mod object-browser/

RUN cd object-browser && \
    go mod edit -go=1.25 -toolchain=go1.25.4 && \
    go mod download && \
    CGO_ENABLED=0 GOOS=linux GOARCH=amd64 GO111MODULE=on go build -a -o console ./cmd/console

FROM alpine:3.22.2

WORKDIR /usr/local/bin

COPY --from=builder --chown=1000 /workspace/object-browser/console .

# Avoiding vulnerabilities
RUN set -x \
    && apk add --upgrade --no-cache apk-tools

# Upgrade all tools to avoid vulnerabilities
RUN set -x && apk upgrade --no-cache --available

ENTRYPOINT ["/usr/local/bin/console"]
