FROM golang:1.23-bookworm AS builder
WORKDIR /src
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -trimpath -ldflags="-s -w" -o /out/go-api ./cmd/server

FROM gcr.io/distroless/static-debian12:nonroot
COPY --from=builder /out/go-api /go-api
USER nonroot:nonroot
EXPOSE 8090
ENTRYPOINT ["/go-api"]
