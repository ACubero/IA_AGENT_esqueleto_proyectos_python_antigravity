---
name: docker-expert
description: Experto en containerización con Docker para proyectos Python. Dockerfiles optimizados, multi-stage builds y compose.
---

# 🐳 Docker Expert

Skill para containerizar aplicaciones Python de forma profesional.

## Cuándo Usar Esta Skill

- Crear Dockerfiles optimizados para Python
- Configurar Docker Compose para desarrollo
- Implementar multi-stage builds
- Optimizar tamaño de imágenes
- Configurar entornos de desarrollo con containers

---

## 🏗️ Dockerfile Optimizado para Python

```dockerfile
# Dockerfile
# Multi-stage build para aplicación Python

# === Stage 1: Builder ===
FROM python:3.12-slim as builder

WORKDIR /app

# Instalar dependencias de compilación
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copiar requirements primero (cache de layers)
COPY requirements.txt .

# Crear virtualenv e instalar dependencias
RUN python -m venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -r requirements.txt

# === Stage 2: Runtime ===
FROM python:3.12-slim as runtime

WORKDIR /app

# Crear usuario no-root
RUN groupadd -r appuser && useradd -r -g appuser appuser

# Copiar virtualenv del builder
COPY --from=builder /opt/venv /opt/venv
ENV PATH="/opt/venv/bin:$PATH"

# Copiar código fuente
COPY --chown=appuser:appuser src/ ./src/
COPY --chown=appuser:appuser main.py .

# Cambiar a usuario no-root
USER appuser

# Variables de entorno
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONPATH=/app

# Puerto expuesto
EXPOSE 8000

# Healthcheck
HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
    CMD python -c "import urllib.request; urllib.request.urlopen('http://localhost:8000/health')"

# Comando de inicio
CMD ["python", "main.py"]
```

---

## 🔧 Docker Compose para Desarrollo

```yaml
# docker-compose.yml
version: "3.9"

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
      target: runtime
    container_name: python_app
    ports:
      - "8000:8000"
    volumes:
      - ./src:/app/src:ro # Código en modo lectura
      - ./logs:/app/logs # Logs persistentes
    environment:
      - DEBUG=true
      - DATABASE_URL=postgresql://user:pass@db:5432/mydb
    depends_on:
      db:
        condition: service_healthy
    networks:
      - app_network
    restart: unless-stopped

  db:
    image: postgres:16-alpine
    container_name: postgres_db
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: mydb
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user -d mydb"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - app_network

  redis:
    image: redis:7-alpine
    container_name: redis_cache
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    networks:
      - app_network

volumes:
  postgres_data:
  redis_data:

networks:
  app_network:
    driver: bridge
```

---

## 🚀 Docker Compose para Desarrollo con Hot Reload

```yaml
# docker-compose.dev.yml
version: "3.9"

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile.dev
    volumes:
      - .:/app # Mount completo para hot reload
      - /app/.venv # Excluir venv del mount
    ports:
      - "8000:8000"
    environment:
      - DEBUG=true
      - WATCHFILES_FORCE_POLLING=true
    command: python -m watchfiles main.main --host 0.0.0.0 --port 8000
```

```dockerfile
# Dockerfile.dev (desarrollo)
FROM python:3.12-slim

WORKDIR /app

RUN pip install --no-cache-dir --upgrade pip

COPY requirements.txt requirements-dev.txt ./
RUN pip install --no-cache-dir -r requirements.txt -r requirements-dev.txt

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

CMD ["python", "main.py"]
```

---

## 📝 Procedimientos

### 1. Construir Imagen

```bash
# Build básico
docker build -t myapp:latest .

# Build con target específico
docker build --target runtime -t myapp:prod .

# Build sin cache
docker build --no-cache -t myapp:latest .

# Build con build args
docker build --build-arg VERSION=1.0.0 -t myapp:1.0.0 .
```

### 2. Ejecutar Container

```bash
# Ejecutar interactivo
docker run -it --rm myapp:latest

# Ejecutar con puerto
docker run -d -p 8000:8000 --name myapp myapp:latest

# Ejecutar con variables de entorno
docker run -d --env-file .env myapp:latest

# Ejecutar con volumen
docker run -d -v $(pwd)/data:/app/data myapp:latest
```

### 3. Docker Compose

```bash
# Iniciar servicios
docker compose up -d

# Iniciar con rebuild
docker compose up -d --build

# Ver logs
docker compose logs -f app

# Ejecutar comando en container
docker compose exec app python manage.py migrate

# Parar servicios
docker compose down

# Parar y eliminar volúmenes
docker compose down -v
```

### 4. Optimización de Imágenes

```dockerfile
# ❌ Malo - Imagen grande
FROM python:3.12
RUN pip install -r requirements.txt
COPY . .

# ✅ Bueno - Imagen optimizada
FROM python:3.12-slim as builder
# ... build stage ...

FROM python:3.12-slim
# ... runtime con solo lo necesario ...
```

**Técnicas de optimización:**

- Usar imágenes `-slim` o `-alpine`
- Multi-stage builds
- Ordenar COPY por frecuencia de cambio
- Combinar RUN commands
- Usar `.dockerignore`

### 5. .dockerignore

```gitignore
# .dockerignore
.git
.gitignore
.env
.env.*
*.pyc
__pycache__
.pytest_cache
.mypy_cache
.ruff_cache
.venv
venv
*.egg-info
dist
build
*.md
!README.md
tests
docs
.coverage
htmlcov
```

---

## 🔒 Seguridad

```dockerfile
# Usuario no-root
RUN groupadd -r appuser && useradd -r -g appuser appuser
USER appuser

# No almacenar secrets en imagen
# Usar docker secrets o variables de entorno

# Escanear vulnerabilidades
# docker scout cve myapp:latest
```

---

## ✅ Checklist Docker

- [ ] ¿Multi-stage build implementado?
- [ ] ¿Imagen base es slim/alpine?
- [ ] ¿Usuario no-root configurado?
- [ ] ¿.dockerignore configurado?
- [ ] ¿Healthcheck implementado?
- [ ] ¿Variables de entorno documentadas?
- [ ] ¿Volúmenes para datos persistentes?
- [ ] ¿Networks configuradas correctamente?
