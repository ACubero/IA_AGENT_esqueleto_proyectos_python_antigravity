---
name: testing-expert
description: Experto en testing Python con pytest. Estrategias de testing, fixtures, mocking, coverage y TDD.
---

# 🧪 Testing Expert

Skill para implementar testing profesional en proyectos Python.

## Cuándo Usar Esta Skill

- Crear tests unitarios, de integración o e2e
- Configurar pytest y plugins
- Implementar fixtures reutilizables
- Aplicar mocking y patching
- Medir y mejorar coverage
- Aplicar TDD (Test-Driven Development)

---

## 📦 Dependencias Requeridas

```bash
pip install pytest pytest-cov pytest-mock pytest-asyncio hypothesis faker
```

---

## 🏗️ Estructura de Tests Recomendada

```
tests/
├── conftest.py          # Fixtures globales
├── unit/                # Tests unitarios (rápidos, sin I/O)
│   ├── test_models.py
│   └── test_utils.py
├── integration/         # Tests de integración (con DB, APIs)
│   └── test_api.py
├── e2e/                 # Tests end-to-end
│   └── test_workflows.py
└── fixtures/            # Datos de prueba
    └── sample_data.json
```

---

## 🔧 Configuración pytest.ini

```ini
[pytest]
testpaths = tests
python_files = test_*.py
python_functions = test_*
addopts = -v --tb=short --strict-markers
markers =
    slow: marks tests as slow (deselect with '-m "not slow"')
    integration: marks tests requiring external services
    e2e: marks end-to-end tests
```

---

## 📝 Procedimientos

### 1. Crear Test Unitario Básico

```python
# tests/unit/test_calculator.py
import pytest
from src.calculator import add, divide

class TestCalculator:
    """Tests para el módulo calculator."""

    def test_add_positive_numbers(self):
        """Suma de números positivos."""
        assert add(2, 3) == 5

    def test_add_negative_numbers(self):
        """Suma con números negativos."""
        assert add(-1, -1) == -2

    def test_divide_by_zero_raises_error(self):
        """División por cero debe lanzar ZeroDivisionError."""
        with pytest.raises(ZeroDivisionError):
            divide(10, 0)
```

### 2. Crear Fixtures Reutilizables

```python
# tests/conftest.py
import pytest
from pathlib import Path

@pytest.fixture
def sample_user():
    """Usuario de prueba básico."""
    return {"id": 1, "name": "Test User", "email": "test@example.com"}

@pytest.fixture
def temp_file(tmp_path):
    """Archivo temporal para tests."""
    file_path = tmp_path / "test_file.txt"
    file_path.write_text("contenido de prueba")
    return file_path

@pytest.fixture(scope="session")
def db_connection():
    """Conexión a DB de testing (una vez por sesión)."""
    conn = create_test_database()
    yield conn
    conn.close()
```

### 3. Implementar Mocking

```python
# tests/unit/test_api_client.py
from unittest.mock import Mock, patch, MagicMock
import pytest

class TestAPIClient:

    @patch('src.api_client.requests.get')
    def test_fetch_data_success(self, mock_get):
        """Test con mock de requests."""
        mock_get.return_value.json.return_value = {"data": "test"}
        mock_get.return_value.status_code = 200

        result = fetch_data("https://api.example.com")

        assert result == {"data": "test"}
        mock_get.assert_called_once()

    def test_with_pytest_mock(self, mocker):
        """Usando pytest-mock (más limpio)."""
        mock_response = mocker.patch('src.api_client.requests.get')
        mock_response.return_value.json.return_value = {"status": "ok"}

        result = fetch_data("https://api.example.com")
        assert result["status"] == "ok"
```

### 4. Tests Parametrizados

```python
import pytest

@pytest.mark.parametrize("input_val,expected", [
    (1, 1),
    (2, 4),
    (3, 9),
    (4, 16),
    (-2, 4),
])
def test_square(input_val, expected):
    """Test parametrizado para función square."""
    assert square(input_val) == expected

@pytest.mark.parametrize("email,is_valid", [
    ("user@example.com", True),
    ("invalid-email", False),
    ("", False),
    ("user@domain.co.uk", True),
])
def test_email_validation(email, is_valid):
    """Validación de emails con múltiples casos."""
    assert validate_email(email) == is_valid
```

### 5. Tests Asíncronos

```python
import pytest

@pytest.mark.asyncio
async def test_async_fetch():
    """Test de función asíncrona."""
    result = await async_fetch_data("https://api.example.com")
    assert result is not None

@pytest.mark.asyncio
async def test_async_with_timeout():
    """Test asíncrono con timeout."""
    import asyncio
    with pytest.raises(asyncio.TimeoutError):
        await asyncio.wait_for(slow_operation(), timeout=0.1)
```

### 6. Property-Based Testing con Hypothesis

```python
from hypothesis import given, strategies as st

@given(st.integers(), st.integers())
def test_add_commutative(a, b):
    """La suma es conmutativa para cualquier entero."""
    assert add(a, b) == add(b, a)

@given(st.text(min_size=1))
def test_string_reverse_twice(s):
    """Revertir un string dos veces da el original."""
    assert reverse(reverse(s)) == s
```

---

## 📊 Comandos de Ejecución

```bash
# Ejecutar todos los tests
pytest

# Con coverage
pytest --cov=src --cov-report=html

# Solo tests rápidos (excluir slow)
pytest -m "not slow"

# Tests específicos
pytest tests/unit/test_calculator.py -v

# Parar en primer fallo
pytest -x

# Mostrar prints
pytest -s

# Tests en paralelo (requiere pytest-xdist)
pytest -n auto
```

---

## ✅ Checklist de Testing

- [ ] ¿Cada función pública tiene al menos un test?
- [ ] ¿Los edge cases están cubiertos?
- [ ] ¿Los tests son independientes entre sí?
- [ ] ¿Coverage > 80%?
- [ ] ¿Los tests son rápidos (< 10s para unit tests)?
- [ ] ¿Los fixtures están en conftest.py?
- [ ] ¿Los tests tienen nombres descriptivos?
