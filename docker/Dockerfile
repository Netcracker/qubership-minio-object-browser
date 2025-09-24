# Build the manager binary
FROM golang:1.25.1-alpine3.22 AS builder

WORKDIR /workspace

# Copy the Go Modules manifests
COPY go.mod go.mod
COPY go.sum go.sum

# cache deps before building and copying source so that we don't need to re-download as much
# and so that source changes don't invalidate our downloaded layer
RUN go mod download

# Copy the go source
COPY ./ .

# Build
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 GO111MODULE=on go build -a -o console ./cmd/console

FROM alpine:3.22.1

WORKDIR /usr/local/bin

COPY --from=builder --chown=1000 /workspace/console .

# Avoiding vulnerabilities
RUN set -x \
    && apk add --upgrade --no-cache apk-tools

# Upgrade all tools to avoid vulnerabilities
RUN set -x && apk upgrade --no-cache --available

ENTRYPOINT ["/usr/local/bin/console"]
