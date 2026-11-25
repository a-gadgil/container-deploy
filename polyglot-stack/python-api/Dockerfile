# Stage 1: Build virtual environment with pinned dependencies
FROM python:3.12-slim-bookworm AS builder

WORKDIR /build
RUN python -m venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Stage 2: Minimal non-root runtime
FROM python:3.12-slim-bookworm

RUN groupadd --gid 10001 appgroup && \
    useradd --uid 10001 --gid appgroup --no-create-home --shell /usr/sbin/nologin appuser

WORKDIR /app
COPY --from=builder /opt/venv /opt/venv
COPY app/ ./app/

ENV PATH="/opt/venv/bin:$PATH" \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    APP_HOST=0.0.0.0 \
    APP_PORT=8091 \
    APP_ENV=container

USER appuser:appgroup
EXPOSE 8091

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8091", "--workers", "2"]
