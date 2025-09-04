# --- Stage 1: Builder ---
FROM python:3.9-slim-bookworm as builder

# Install build tools for Python packages
RUN apt-get update && apt-get install -y --no-install-recommends gcc \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy only requirements first for better caching
COPY requirements.txt .

# Build wheels for dependencies
RUN --mount=type=cache,target=/root/.cache/pip \
    pip wheel --no-cache-dir --no-deps -r requirements.txt -w /wheels


# --- Stage 2: Final Production Image ---
FROM python:3.9-slim-bookworm

# Install only runtime system libraries
RUN apt-get update && apt-get install -y --no-install-recommends \
    libgl1 libglib2.0-0 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy built wheels & install them
COPY --from=builder /wheels /wheels
RUN pip install --no-cache-dir /wheels/*

# Copy application source code
COPY run.py . 
COPY app/ app/

# Security: create non-root user
RUN useradd -m appuser \
    && mkdir -p /app/app/uploads \
    && chown -R appuser:appuser /app

USER appuser

# Expose port
EXPOSE 5000

# Default command (Gunicorn for Flask)
CMD ["gunicorn", "-b", "0.0.0.0:5000", "run:app"]
