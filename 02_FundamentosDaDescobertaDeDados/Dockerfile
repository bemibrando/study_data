FROM python:3.11.8-slim

# Avoid Python buffering
ENV PYTHONDONTWRITEBYTECODE=1 \
	PYTHONUNBUFFERED=1 \
	PIP_NO_CACHE_DIR=1 \
	PIP_DISABLE_PIP_VERSION_CHECK=1
	
# Create non-root user  
RUN useradd -m appuser

WORKDIR /app

# Install system dependencies (only needed)
RUN apt-get update && apt-get install -y --no-install-recommends\
    build-essential \
    git \
    curl \
    && rm -rf /var/lib/apt/lists/*
  
# Copy pyproject  
COPY pyproject.toml .  
  
# Install dependencies  
RUN pip install --upgrade pip && pip install .

# Copy application code  
COPY src/ ./src/  
#COPY notebooks/ ./notebooks/  
  
# Fix permissions  
RUN chown -R appuser:appuser /app  
  
# Switch to non-root user  
USER appuser

# Expose Jupyter port
EXPOSE 8888

# Healthcheck
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
	CMD curl --fail http://localhost:8888/health || exit 1

# Start Jupyter  
CMD ["python", "-m", "notebook", "--ip=0.0.0.0", "--port=8888", "--no-browser"]