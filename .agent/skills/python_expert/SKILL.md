---
name: python-expert
description: Experto en desarrollo Python moderno. Usa esta skill para crear proyectos, depurar código, gestionar dependencias y aplicar mejores prácticas.
---

# 🐍 Python Expert Skill

## Propósito

Asistente especializado en desarrollo Python que aplica las mejores prácticas modernas, gestión de entornos virtuales y patrones de código limpio.

---

## Cuándo Invocar

Invoca esta skill cuando el usuario:

- Pida crear un nuevo proyecto Python
- Necesite depurar errores de imports o dependencias
- Quiera refactorizar código siguiendo PEP8/SOLID
- Pregunte sobre type hints, dataclasses o patrones modernos

---

## Instructions

### 1. Gestión de Entorno Virtual

**SIEMPRE** verificar y usar entorno virtual antes de cualquier operación:

```bash
# Verificar si existe
if [ -d ".venv" ]; then
    source .venv/bin/activate  # Linux/Mac
    # .venv\Scripts\activate   # Windows
fi

# Crear si no existe
python -m venv .venv
```

### 2. Estructura de Proyecto Estándar

Al crear nuevos proyectos, usar esta estructura:

```
proyecto/
├── src/
│   └── nombre_paquete/
│       ├── __init__.py
│       └── main.py
├── tests/
│   ├── __init__.py
│   └── test_main.py
├── .venv/
├── pyproject.toml
├── requirements.txt
└── README.md
```

### 3. Código Moderno (Python 3.10+)

Priorizar siempre:

- **Type Hints**: `def foo(x: int) -> str:`
- **Dataclasses**: `@dataclass` sobre diccionarios raw
- **Pattern Matching**: `match/case` para flujos complejos
- **Walrus Operator**: `:=` cuando mejore legibilidad
- **f-strings**: Nunca usar `.format()` o `%`

### 4. Dependencias

1. Usar `pip install` solo con venv activo
2. Actualizar `requirements.txt`: `pip freeze > requirements.txt`
3. Para proyectos complejos, preferir `pyproject.toml` con `setuptools` o `poetry`

### 5. Debugging de Imports

Si hay `ModuleNotFoundError`:

1. Verificar venv activo: `which python` / `where python`
2. Verificar instalación: `pip list | grep nombre`
3. Verificar PYTHONPATH si es proyecto local
4. Verificar `__init__.py` en paquetes

---

## Constraints

- **NUNCA** instalar paquetes globalmente (`pip install` sin venv)
- **SIEMPRE** incluir type hints en código nuevo
- **SIEMPRE** seguir PEP8 naming: `snake_case` para funciones/variables
- **EVITAR** `import *` - importaciones explícitas siempre
- **PREFERIR** `pathlib.Path` sobre `os.path`

---

## Herramientas Recomendadas

| Herramienta | Uso                  |
| ----------- | -------------------- |
| `ruff`      | Linting + Formatting |
| `pytest`    | Testing              |
| `mypy`      | Type checking        |
| `rich`      | Output bonito en CLI |
| `httpx`     | HTTP client moderno  |
| `pydantic`  | Validación de datos  |
