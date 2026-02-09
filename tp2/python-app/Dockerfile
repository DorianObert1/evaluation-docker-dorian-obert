FROM python:3.11-slim

WORKDIR /app

# Copier requirements.txt d'abord pour optimiser le cache pip
COPY requirements.txt .

# Installer les dépendances avec cache pip optimisé
RUN pip install --no-cache-dir -r requirements.txt

# Copier le code de l'application
COPY app.py .

# Variable d'environnement
ENV FLASK_ENV=production

EXPOSE 5000

# Healthcheck
HEALTHCHECK --interval=30s --timeout=10s --retries=3 \
    CMD curl -f http://localhost:5000/health || exit 1

# Utiliser gunicorn en production
CMD ["gunicorn", "--bind", "0.0.0.0:5000", "app:app"]

