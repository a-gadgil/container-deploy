# Stage 1: Compile C++20 Release binary against Bookworm's exact glibc/libstdc++
FROM debian:bookworm-slim AS builder

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
      g++=4:12.2.0-3 \
      cmake=3.25.1-1 \
      make=4.3-4.1 && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /build
COPY CMakeLists.txt .
COPY third_party/ ./third_party/
COPY src/ ./src/

RUN cmake -S . -B build -DCMAKE_BUILD_TYPE=Release && \
    cmake --build build -j"$(nproc)" && \
    strip build/cpp-api

# Stage 2: Distroless non-root runtime (no shell, no package manager, minimal CVE surface)
FROM gcr.io/distroless/cc-debian12:nonroot

WORKDIR /app
COPY --from=builder /build/build/cpp-api /app/cpp-api

ENV APP_HOST=0.0.0.0 \
    APP_PORT=8083 \
    APP_ENV=container

USER nonroot:nonroot
EXPOSE 8083

CMD ["/app/cpp-api"]
