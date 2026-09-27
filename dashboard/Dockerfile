FROM python:3.11-slim

RUN groupadd -r -g 10001 appuser && \
    useradd -r -u 10001 -g appuser -s /sbin/nologin -c "Dashboard application user" appuser

WORKDIR /dashboard
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY --chown=root:root app.py report_generator.py ./
COPY --chown=root:root templates/ ./templates/

RUN mkdir -p /dashboard/reports && \
    chown appuser:appuser /dashboard/reports && \
    chmod 755 /dashboard/reports

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
    CMD python3 -c "import urllib.request; urllib.request.urlopen('http://localhost:5000/api/status', timeout=2)" || exit 1

USER appuser

CMD ["gunicorn", "--bind", "0.0.0.0:5000", "--workers", "1", "--threads", "4", "app:app"]
