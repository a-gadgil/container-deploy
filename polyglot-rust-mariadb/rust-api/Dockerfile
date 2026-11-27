FROM rust:1-bookworm AS builder
WORKDIR /src
COPY Cargo.toml Cargo.lock* ./
RUN mkdir -p src && echo 'fn main() {}' > src/main.rs \
    && cargo build --release \
    && rm -rf src
COPY src ./src
RUN touch src/main.rs && cargo build --release

FROM gcr.io/distroless/cc-debian12:nonroot
COPY --from=builder /src/target/release/polyglot-rust-api /polyglot-rust-api
USER nonroot:nonroot
EXPOSE 8091
ENTRYPOINT ["/polyglot-rust-api"]
