FROM dhi.io/python:3.14-dev@sha256:490a2607efe2d8234b16da40c41fbca6d12799f046bf0def488ce868fc5b43da AS builder

WORKDIR /app
RUN python -m venv --without-pip /venv
COPY requirements.txt .
RUN python -m pip --python /venv/bin/python install --no-cache-dir --no-compile \
    --require-hashes --only-binary=:all: -r requirements.txt

FROM dhi.io/python:3.14@sha256:238eb9b564c3eec6de43cdaddfee6d92f946ae38a367d763c5e2fec4472a4814

WORKDIR /app
ENV PATH="/venv/bin:$PATH" \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1
COPY --from=builder --chown=65532:65532 /venv /venv
COPY --chown=65532:65532 app.py .
COPY --chown=65532:65532 templates ./templates

USER 65532:65532
EXPOSE 8080
CMD ["opentelemetry-instrument", "gunicorn", "--bind=0.0.0.0:8080", "--access-logfile=-", "app:app"]
