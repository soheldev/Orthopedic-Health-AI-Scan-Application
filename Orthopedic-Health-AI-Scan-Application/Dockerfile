# --- Stage 1: Build Environment ---
FROM python:3.9-slim as builder

WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt


# --- Stage 2: Final Production Image ---
FROM python:3.9-slim

RUN apt-get update && apt-get install -y libgl1 libglib2.0-0

WORKDIR /app
COPY --from=builder /usr/local/lib/python3.9/site-packages /usr/local/lib/python3.9/site-packages
COPY . .
EXPOSE 5000
CMD ["python", "run.py"]
