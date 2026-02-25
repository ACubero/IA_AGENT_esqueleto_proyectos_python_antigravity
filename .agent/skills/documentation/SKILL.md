---
name: documentation
description: Experto en documentación Python. Docstrings, Sphinx, MkDocs y ADRs.
---

# 📚 Documentation Expert

Skill para crear documentación profesional de proyectos Python.

## Cuándo Usar

- Escribir docstrings estándar
- Configurar Sphinx o MkDocs
- Crear ADRs (Architecture Decision Records)
- Documentar APIs

---

## 📝 Docstrings (Google Style)

```python
def calculate_discount(price: float, percentage: float) -> float:
    """Calculate discounted price.

    Args:
        price: Original price in dollars.
        percentage: Discount percentage (0-100).

    Returns:
        Final price after discount.

    Raises:
        ValueError: If percentage is not between 0 and 100.

    Example:
        >>> calculate_discount(100.0, 20.0)
        80.0
    """
    if not 0 <= percentage <= 100:
        raise ValueError("Percentage must be between 0 and 100")
    return price * (1 - percentage / 100)
```

---

## 📖 MkDocs Setup

```bash
pip install mkdocs mkdocs-material mkdocstrings[python]
mkdocs new .
```

```yaml
# mkdocs.yml
site_name: Mi Proyecto
theme:
  name: material
  palette:
    primary: indigo

plugins:
  - search
  - mkdocstrings:
      handlers:
        python:
          options:
            show_source: true

nav:
  - Home: index.md
  - API Reference: api.md
  - Contributing: contributing.md
```

```bash
mkdocs serve  # Desarrollo
mkdocs build  # Producción
```

---

## 📋 ADR Template

```markdown
# ADR-001: Título de la Decisión

## Estado

Aceptado | Propuesto | Deprecado

## Contexto

¿Por qué necesitamos tomar esta decisión?

## Decisión

¿Qué decidimos hacer?

## Consecuencias

### Positivas

- Beneficio 1

### Negativas

- Trade-off 1
```

---

## ✅ Checklist

- [ ] ¿Todas las funciones públicas tienen docstrings?
- [ ] ¿README.md actualizado?
- [ ] ¿CHANGELOG.md mantenido?
- [ ] ¿API documentada?
- [ ] ¿ADRs para decisiones importantes?
