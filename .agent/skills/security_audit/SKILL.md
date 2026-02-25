---
name: security-audit
description: Audita la seguridad del proyecto. Usa esta skill para detectar secretos expuestos, vulnerabilidades en dependencias y código inseguro.
---

# 🔐 Security Audit Skill

## Propósito

Auditoría de seguridad automatizada que detecta:

- Secretos y credenciales expuestas
- Vulnerabilidades en dependencias
- Patrones de código inseguro
- Configuraciones peligrosas

---

## Cuándo Invocar

Invoca esta skill con:

- "Audita la seguridad"
- "Revisa secretos"
- "Verifica vulnerabilidades"
- `/security-audit`

---

## Instructions

### 1. Detección de Secretos Expuestos

Buscar patrones peligrosos en el código:

```bash
# Patrones a buscar con grep_search
grep -rn "password\s*=\s*['\"]" --include="*.py" --include="*.js"
grep -rn "api_key\s*=\s*['\"]" --include="*.py" --include="*.js"
grep -rn "secret\s*=\s*['\"]" --include="*.py" --include="*.js"
grep -rn "AWS_" --include="*.py" --include="*.env"
grep -rn "sk-[a-zA-Z0-9]" .  # OpenAI keys
grep -rn "ghp_" .             # GitHub tokens
```

**Archivos a revisar prioritariamente:**

- `.env` (NO debe estar en git)
- `config.py`, `settings.py`
- `docker-compose.yml`
- Cualquier archivo con "secret", "key", "password" en nombre

### 2. Verificar .gitignore

Asegurar que estos patrones estén excluidos:

```gitignore
# Secretos - OBLIGATORIO
.env
.env.*
*.pem
*.key
secrets/
credentials/

# Entornos virtuales
.venv/
venv/
__pycache__/

# IDEs
.idea/
.vscode/
```

### 3. Auditar Dependencias

```bash
# Python - vulnerabilidades conocidas
pip install safety
safety check -r requirements.txt

# Si usa pip-audit (más moderno)
pip install pip-audit
pip-audit

# Node.js
npm audit
```

### 4. Patrones de Código Inseguro

| Patrón Peligroso            | Riesgo                   | Solución                  |
| --------------------------- | ------------------------ | ------------------------- |
| `eval()`                    | Ejecución arbitraria     | Usar `ast.literal_eval()` |
| `exec()`                    | Ejecución arbitraria     | Refactorizar lógica       |
| `shell=True` en subprocess  | Inyección                | Usar lista de args        |
| `pickle.load()` sin validar | Deserialización insegura | Usar JSON                 |
| SQL con f-strings           | SQL Injection            | Usar parámetros `?`       |
| `verify=False` en requests  | MITM                     | Mantener verificación SSL |

### 5. Verificar Configuraciones

- [ ] `DEBUG = False` en producción
- [ ] `SECRET_KEY` no hardcodeada
- [ ] CORS configurado correctamente
- [ ] Logs no exponen datos sensibles

---

## Output del Audit

Generar reporte en formato:

```markdown
# 🔐 Security Audit Report

**Fecha**: [FECHA]
**Proyecto**: [NOMBRE]

## ⚠️ Hallazgos Críticos

- [Lista de secretos/vulnerabilidades críticas]

## 🟡 Advertencias

- [Lista de mejoras recomendadas]

## ✅ Verificaciones Pasadas

- [Lista de checks OK]

## 📋 Acciones Requeridas

1. [Acción prioritaria 1]
2. [Acción prioritaria 2]
```

---

## Constraints

- **NUNCA** mostrar valores de secretos en output
- **SIEMPRE** recomendar rotación si se detecta exposición
- **PARAR** y alertar al usuario ante hallazgos críticos
- Si un secreto fue committeado, recomendar:
  1. Rotar la credencial inmediatamente
  2. Usar `git filter-branch` o BFG para limpiar historial
