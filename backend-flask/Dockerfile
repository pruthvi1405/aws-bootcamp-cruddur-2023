# ======================
# Builder Stage
# ======================
FROM python:3.10-slim AS builder

WORKDIR /backend-flask

COPY requirements.txt .

RUN pip install \
    --no-cache-dir \
    --prefix=/install \
    -r requirements.txt

# ======================
# Runtime Stage
# ======================
FROM python:3.10-slim

WORKDIR /backend-flask

# Install curl for health checks
RUN apt-get update && \
    apt-get install -y curl && \
    rm -rf /var/lib/apt/lists/*

# Copy installed dependencies from builder
COPY --from=builder /install /usr/local

# Copy application code
COPY . .

# Create non-root user
RUN useradd -m flaskuser && \
    chown -R flaskuser:flaskuser /backend-flask

USER flaskuser

ENV FLASK_ENV=development

EXPOSE 4567

HEALTHCHECK --interval=30s \
            --timeout=10s \
            --start-period=20s \
            --retries=3 \
    CMD curl -f https://${CODESPACE_NAME}-4567.${GITHUB_CODESPACES_PORT_FORWARDING_DOMAIN}/api/health-check || exit 1

CMD ["python3", "-m", "flask", "run", "--host=0.0.0.0", "--port=4567"]