# Build the manager binary
FROM golang:1.26.4-alpine3.24 AS builder

# Install minimal build deps
RUN apk add --no-cache git

WORKDIR /workspace

# Clone & build MinIO Object Browser
# RUN git clone --depth=1 --branch v1.7.6 https://github.com/minio/object-browser.git
RUN git clone --depth=1 --branch v1.7.6 https://github.com/OpenMaxIO/openmaxio-object-browser.git

# COPY go.mod object-browser/

RUN cd openmaxio-object-browser && \
    sed -i 's/{name:"OpenID",id:"openID",path:E.zZ.IDP_OPENID_CONFIGURATIONS,icon:(0,d.jsx)(m.fiF,{})},//g' ./web-app/build/static/js/main.45669c2e.js && \
    go mod edit -go=1.26 -toolchain=go1.26.4 -require=github.com/prometheus/prometheus@v0.312.0 -require=golang.org/x/net@v0.56.0 -require=golang.org/x/crypto@v0.53.0 -require=github.com/golang-jwt/jwt/v4@v4.5.2 -require=golang.org/x/oauth2@v0.33.0 -require=google.golang.org/grpc@v1.80.0 && \
    go mod tidy && \
    go mod download && \
    CGO_ENABLED=0 GOOS=linux GO111MODULE=on go build -a -o console ./cmd/console

FROM alpine:3.24.1

WORKDIR /usr/local/bin

COPY --from=builder --chown=1000 /workspace/openmaxio-object-browser/console .

# Avoiding vulnerabilities
RUN set -x \
    && apk add --upgrade --no-cache apk-tools

# Upgrade all tools to avoid vulnerabilities
RUN set -x && apk upgrade --no-cache --available

ENTRYPOINT ["/usr/local/bin/console"]
