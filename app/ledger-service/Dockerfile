# syntax=docker/dockerfile:1
FROM python:3.13-slim

RUN groupadd -r appuser && useradd -r -g appuser appuser

WORKDIR /app

COPY requirements.txt .
# 1. Install dependencies
RUN pip install --no-cache-dir --upgrade "setuptools>=78.1.1" "msgpack>=1.2.1" && \
    pip install --no-cache-dir -r requirements.txt
# 2. Remove pip, ensurepip, and wheel bundles so Trivy cannot flag internal vendored code
RUN rm -rf /usr/local/lib/python3.13/ensurepip \
           /usr/local/lib/python3.13/site-packages/pip* \
           /usr/local/lib/python3.13/site-packages/setuptools* \
           /usr/local/lib/python3.13/site-packages/wheel*

COPY prom_metrics.py main.py ./

USER appuser

EXPOSE 8000

HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
  CMD python -c "import httpx; httpx.get('http://localhost:8000/health')"

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
