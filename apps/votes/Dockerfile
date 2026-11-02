FROM dhi.io/golang:1.26-dev@sha256:7e044fc5a969032610eaadf997f2ebac4639623cc87ba80d47006600e7e39c4f AS builder

USER 1000:1000
WORKDIR /tmp/src
ENV GOCACHE=/tmp/go-build \
    GOMODCACHE=/tmp/go-mod
COPY go.mod go.sum ./
RUN go mod download && go mod verify
COPY . .
ARG TARGETOS
ARG TARGETARCH
RUN CGO_ENABLED=0 GOOS="${TARGETOS}" GOARCH="${TARGETARCH}" \
    go build -trimpath -ldflags="-s -w" -o /tmp/votes .

FROM scratch

COPY --from=builder --chown=65532:65532 /tmp/votes /usr/bin/votes
USER 65532:65532
EXPOSE 8081
ENTRYPOINT ["/usr/bin/votes"]
