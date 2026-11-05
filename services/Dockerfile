FROM python:3.12-slim

WORKDIR /app

# Install uv
COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/

# Copy dependency files first (Docker layer caching)
COPY services/api/pyproject.toml services/api/uv.lock ./

# Sync dependencies using uv
RUN uv sync --frozen

# Copy the rest of the application code
COPY services/api/ ./
COPY services/reporting/ /app/reporting/
COPY data/ /app/data/
COPY docs/company-knowledge-base/ /app/docs/company-knowledge-base/

# Create non-root user with home directory and set up directory permissions
RUN addgroup --system --gid 1001 brasaland && \
    adduser --system --uid 1001 --ingroup brasaland --home /home/brasaland brasaland && \
    mkdir -p /app/data /app/logs /app/.cache && \
    chown -R brasaland:brasaland /app /home/brasaland && \
    chmod 755 /app && \
    chmod 750 /app/data && \
    chmod 750 /app/logs && \
    chmod 750 /app/.cache

# Set UV cache to a writable location
ENV UV_CACHE_DIR=/app/.cache/uv

# Switch to non-root user
USER brasaland

# Expose the port the API runs on
EXPOSE 8020

# Default command: start uvicorn with reload
CMD ["uv", "run", "uvicorn", "main:app", "--reload", "--port", "8020", "--host", "0.0.0.0"]