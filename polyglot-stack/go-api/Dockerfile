# Stage 1: Build static Go binary
FROM golang:1.27-bookworm AS builder

WORKDIR /src
COPY go.mod go.sum ./
RUN go mod download

COPY cmd/ ./cmd/
COPY internal/ ./internal/

RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 \
    go build -trimpath -ldflags="-s -w" -o /out/go-api ./cmd/server

# Stage 2: Distroless non-root runtime
FROM gcr.io/distroless/static-debian12:nonroot

WORKDIR /app
COPY --from=builder /out/go-api /app/go-api

ENV APP_HOST=0.0.0.0 \
    APP_PORT=8090 \
    APP_ENV=container

USER nonroot:nonroot
EXPOSE 8090

ENTRYPOINT ["/app/go-api"]
