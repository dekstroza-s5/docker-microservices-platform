FROM python:3.13-slim
RUN useradd --uid 10001 --create-home app
WORKDIR /app
RUN pip install --no-cache-dir flask==3.1.0 gunicorn==23.0.0
COPY --chown=app:app app.py .
USER app
EXPOSE 8000
CMD ["gunicorn", "--bind", "0.0.0.0:8000", "app:app"]
