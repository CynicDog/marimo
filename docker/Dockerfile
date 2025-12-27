# syntax=docker/dockerfile:1.12

FROM python:3.11-slim

# System deps
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        openjdk-21-jre-headless \
        curl \
        ca-certificates && \
    rm -rf /var/lib/apt/lists/*

ENV JAVA_HOME=/usr/lib/jvm/java-21-openjdk-arm64
ENV PATH="$JAVA_HOME/bin:$PATH"

# uv
COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/

# App dir + user
RUN useradd -m appuser
WORKDIR /app

# Python config
ARG marimo_version=0.12.8
ENV MARIMO_SKIP_UPDATE_CHECK=1
ENV UV_SYSTEM_PYTHON=1

# Install Python deps + prepare volumes
RUN uv pip install --no-cache-dir \
        marimo==${marimo_version} \
        marimo[recommended,lsp,sql] \
        pyspark==3.5.1 \
        delta-spark==3.1.0 \
        altair pandas numpy && \
    mkdir -p \
        /delta \
        /data/raw \
        /app/data && \
    chown -R appuser:appuser \
        /delta \
        /data \
        /app

# Runtime user
USER appuser

ENV PORT=8080
ENV HOST=0.0.0.0
EXPOSE 8080

CMD ["marimo", "edit", "--no-token", "-p", "8080", "--host", "0.0.0.0"]
