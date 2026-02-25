---
name: best-practices
description: Mejores prácticas para crear aplicaciones Python robustas, mantenibles y profesionales. Estructura, patrones, configuración, manejo de errores y más.
---

# 🏆 Best Practices Skill

## Propósito

Guía de referencia con las mejores prácticas para construir aplicaciones Python de calidad profesional. Cubre desde la estructura del proyecto hasta el deployment.

---

## Cuándo Invocar

Invoca esta skill cuando el usuario:

- Inicie un proyecto nuevo y quiera hacerlo bien desde el principio
- Pregunte "¿cuál es la mejor forma de...?"
- Necesite revisar si su código sigue buenas prácticas
- Quiera mejorar la calidad general de su proyecto
- `/best-practices`

---

## Instructions

### 1. Estructura de Proyecto Profesional

```
proyecto/
├── src/
│   └── nombre_paquete/
│       ├── __init__.py
│       ├── main.py
│       ├── config.py          # Configuración centralizada
│       ├── exceptions.py      # Excepciones personalizadas
│       ├── models/
│       │   └── __init__.py
│       ├── services/
│       │   └── __init__.py
│       ├── repositories/
│       │   └── __init__.py
│       └── utils/
│           └── __init__.py
├── tests/
│   ├── conftest.py
│   ├── unit/
│   └── integration/
├── docs/
├── scripts/
├── .env.example              # Template sin secretos reales
├── .gitignore
├── pyproject.toml
├── requirements.txt
├── Makefile                   # Targets comunes (test, lint, run)
└── README.md
```

**Reglas clave:**

- El código fuente va en `src/` para evitar conflictos de imports
- Tests separados en `unit/` e `integration/`
- NUNCA `.env` en el repo — solo `.env.example` con placeholders

### 2. Configuración Robusta

```python
# ✅ Usar pydantic-settings para configuración tipada
from pydantic_settings import BaseSettings
from pydantic import Field

class Settings(BaseSettings):
    database_url: str = Field(..., description="URL de conexión a la base de datos")
    debug: bool = Field(default=False)
    api_key: str = Field(..., min_length=10)
    max_retries: int = Field(default=3, ge=1, le=10)

    model_config = {"env_file": ".env", "env_file_encoding": "utf-8"}

settings = Settings()
```

```python
# ❌ NUNCA hardcodear configuración
DATABASE_URL = "postgres://user:pass@localhost/db"

# ✅ Siempre desde variables de entorno
import os
DATABASE_URL = os.environ["DATABASE_URL"]
```

### 3. Manejo de Errores Profesional

```python
# ✅ Excepciones personalizadas con jerarquía clara
class AppError(Exception):
    """Base para errores de la aplicación."""
    def __init__(self, message: str, code: str | None = None):
        self.message = message
        self.code = code
        super().__init__(message)

class NotFoundError(AppError):
    """Recurso no encontrado."""
    pass

class ValidationError(AppError):
    """Error de validación de datos."""
    pass

class ExternalServiceError(AppError):
    """Error en servicio externo."""
    pass
```

```python
# ❌ Mal: captura genérica silenciosa
try:
    result = do_something()
except Exception:
    pass

# ✅ Bien: captura específica con logging
import logging

logger = logging.getLogger(__name__)

try:
    result = do_something()
except ConnectionError as e:
    logger.error("Fallo de conexión: %s", e)
    raise ExternalServiceError("Servicio no disponible") from e
except ValueError as e:
    logger.warning("Dato inválido: %s", e)
    raise ValidationError(str(e)) from e
```

### 4. Funciones y Métodos

```python
# ✅ Funciones pequeñas, con un solo propósito, tipadas
def calculate_discount(price: float, tier: str) -> float:
    """Calcula el descuento basado en el tier del cliente."""
    discounts = {"bronze": 0.05, "silver": 0.10, "gold": 0.20}
    rate = discounts.get(tier, 0.0)
    return round(price * rate, 2)
```

**Reglas:**

- Máximo ~20 líneas por función
- Un solo nivel de abstracción por función
- Nombres descriptivos: `calculate_discount` > `calc` > `cd`
- Parámetros: máximo 3-4. Si necesitas más → usar dataclass o dict tipado
- Return early para reducir anidamiento

### 5. Gestión de Dependencias

```toml
# ✅ pyproject.toml moderno (PEP 621)
[project]
name = "mi-proyecto"
version = "0.1.0"
requires-python = ">=3.11"
dependencies = [
    "fastapi>=0.100",
    "pydantic>=2.0",
    "httpx>=0.24",
]

[project.optional-dependencies]
dev = [
    "pytest>=7.0",
    "ruff>=0.1",
    "mypy>=1.0",
    "pre-commit>=3.0",
]
```

**Reglas:**

- Fijar versiones mínimas, no exactas (a menos que haya razón)
- Separar dependencias de producción y desarrollo
- Usar `pip-compile` o `poetry.lock` para lockfile reproducible

### 6. Logging Estructurado

```python
import logging
import sys

def setup_logging(level: str = "INFO") -> None:
    """Configura logging con formato consistente."""
    logging.basicConfig(
        level=getattr(logging, level.upper()),
        format="%(asctime)s | %(levelname)-8s | %(name)s | %(message)s",
        datefmt="%Y-%m-%d %H:%M:%S",
        handlers=[logging.StreamHandler(sys.stdout)],
    )

# Uso por módulo
logger = logging.getLogger(__name__)
logger.info("Procesando pedido %s", order_id)
```

**Reglas:**

- Un logger por módulo con `__name__`
- NUNCA `print()` para diagnóstico — usar `logger.debug()`
- NUNCA loggear datos sensibles (passwords, tokens, PII)
- Usar `%s` o lazy formatting, nunca f-strings en log

### 7. Testing como Ciudadano de Primera Clase

```python
# tests/conftest.py
import pytest

@pytest.fixture
def sample_user() -> dict:
    return {"name": "Test User", "email": "test@example.com", "tier": "gold"}

# tests/unit/test_discount.py
def test_gold_discount():
    result = calculate_discount(100.0, "gold")
    assert result == 20.0

def test_unknown_tier_no_discount():
    result = calculate_discount(100.0, "unknown")
    assert result == 0.0

def test_negative_price_raises():
    with pytest.raises(ValueError):
        calculate_discount(-10.0, "gold")
```

**Reglas:**

- Nombre de test descriptivo: `test_<qué>_<condición>_<resultado_esperado>`
- Un `assert` por test (preferible)
- Fixtures para datos repetidos
- Mocking solo cuando sea necesario (APIs externas, DB)

### 8. Gestión de Recursos

```python
# ✅ Context managers para recursos
from contextlib import contextmanager

@contextmanager
def database_connection(url: str):
    conn = create_connection(url)
    try:
        yield conn
    finally:
        conn.close()

# Uso
with database_connection(settings.database_url) as conn:
    conn.execute("SELECT 1")
```

```python
# ✅ Archivos siempre con context manager
with open("data.json", "r", encoding="utf-8") as f:
    data = json.load(f)

# ❌ NUNCA
f = open("data.json")
data = f.read()
# f.close() se puede olvidar
```

### 9. Patrones de Código Limpio

```python
# ✅ Usar Enum para estados
from enum import Enum, auto

class Status(Enum):
    PENDING = auto()
    PROCESSING = auto()
    COMPLETED = auto()
    FAILED = auto()

# ✅ Usar dataclasses para datos estructurados
from dataclasses import dataclass, field

@dataclass(frozen=True)
class OrderItem:
    product_id: str
    quantity: int
    unit_price: float

    @property
    def total(self) -> float:
        return self.quantity * self.unit_price

# ✅ Comprehensions sobre bucles cuando sea legible
names = [user.name for user in users if user.is_active]

# ❌ Comprehensions ilegibles
result = [x.val for x in [obj for obj in items if obj.type == "A"] if x.val > 0]
# ✅ Mejor separar
filtered_items = [obj for obj in items if obj.type == "A"]
result = [x.val for x in filtered_items if x.val > 0]
```

### 10. Rendimiento y Escalabilidad

| Situación                         | Solución                                    |
| --------------------------------- | ------------------------------------------- |
| Procesamiento de archivos grandes | Generadores (`yield`)                       |
| Múltiples llamadas HTTP           | `asyncio` + `httpx`                         |
| Caché de resultados               | `@functools.lru_cache` o `@functools.cache` |
| Datos tabulares grandes           | `pandas` con chunks o `polars`              |
| Operaciones CPU-bound             | `multiprocessing` o `concurrent.futures`    |

### 11. Makefile para Operaciones Comunes

```makefile
.PHONY: install test lint format run clean

install:
	python -m venv .venv
	.venv/bin/pip install -r requirements.txt

test:
	pytest tests/ -v --tb=short

lint:
	ruff check src/ tests/

format:
	ruff format src/ tests/

run:
	python -m src.nombre_paquete.main

clean:
	find . -type d -name __pycache__ -exec rm -rf {} +
	find . -type f -name "*.pyc" -delete
```

---

## Checklist Pre-Commit

- [ ] ¿Type hints en todas las funciones públicas?
- [ ] ¿Docstrings en clases y funciones públicas?
- [ ] ¿Sin secretos hardcodeados?
- [ ] ¿Tests para la funcionalidad nueva?
- [ ] ¿Linter pasa sin errores?
- [ ] ¿Dependencias actualizadas en requirements?
- [ ] ¿Logging en vez de print?
- [ ] ¿Excepciones específicas (no bare `except`)?
- [ ] ¿Recursos abiertos con context managers?
- [ ] ¿README actualizado si hay cambios en la interfaz?

---

## Constraints

- **SIEMPRE** aplicar estas prácticas en código nuevo
- **SUGERIR** mejoras cuando se detecte código que no las sigue
- **NO EXIGIR** refactoring masivo — mejoras incrementales
- **PRIORIZAR** legibilidad sobre cleverness
- **DOCUMENTAR** desviaciones justificadas de estas prácticas
