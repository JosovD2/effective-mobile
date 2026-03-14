FROM python:3.12-slim

EXPOSE 8080

WORKDIR /app

COPY app.py .

RUN addgroup --system appgroup && \
    adduser --system --ingroup appgroup appuser

USER appuser

HEALTHCHECK --interval=10s --timeout=3s --start-period=5s --retries=3 \
    CMD python -c "import urllib.request; urllib.request.urlopen('http://localhost:8080/')" || exit 1

CMD ["python", "app.py"]
