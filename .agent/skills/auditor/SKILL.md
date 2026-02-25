---
name: auditor
description: Audita el código en busca de vulnerabilidades de seguridad, revelación de secretos, dependencias inseguras y patrones de código peligroso. Genera un informe detallado.
---

# 🕵️ Auditor Skill

## Propósito

Auditoría profunda del código fuente que detecta y clasifica por severidad:

- 🔴 **Crítico**: Secretos expuestos, credenciales hardcodeadas
- 🟠 **Alto**: Inyecciones SQL/OS, deserialización insegura
- 🟡 **Medio**: Dependencias con CVEs, configuraciones inseguras
- 🔵 **Bajo**: Malas prácticas, falta de validación

---

## Cuándo Invocar

Invoca esta skill con:

- "Audita el código"
- "Busca vulnerabilidades"
- "Revisa secretos"
- "Haz una auditoría de seguridad"
- `/auditor`

---

## Instructions

### Fase 1: Detección de Secretos Expuestos

#### 1.1 Patrones de búsqueda obligatorios

Ejecutar `grep_search` con estos patrones en todo el proyecto:

```
# Credenciales directas
password\s*=\s*['"][^'"]+
passwd\s*=\s*['"][^'"]+
api_key\s*=\s*['"][^'"]+
secret\s*=\s*['"][^'"]+
token\s*=\s*['"][^'"]+
auth\s*=\s*['"][^'"]+

# Claves de servicios conocidos
AWS_ACCESS_KEY_ID\s*=\s*['"]?AK
AWS_SECRET_ACCESS_KEY\s*=\s*['"]
sk-[a-zA-Z0-9]{20,}          # OpenAI
ghp_[a-zA-Z0-9]{36}          # GitHub Personal Token
gho_[a-zA-Z0-9]{36}          # GitHub OAuth
glpat-[a-zA-Z0-9\-]{20,}     # GitLab
xoxb-[0-9]{10,}              # Slack Bot Token
SG\.[a-zA-Z0-9_-]{22,}       # SendGrid
sk_live_[a-zA-Z0-9]{24,}     # Stripe

# Conexiones de base de datos
(postgres|mysql|mongodb|redis)://[^'">\s]+

# Claves privadas
-----BEGIN (RSA |EC |DSA )?PRIVATE KEY-----
```

#### 1.2 Archivos de alta prioridad

Revisar SIEMPRE estos archivos/ubicaciones:

| Archivo                    | Riesgo                            |
| -------------------------- | --------------------------------- |
| `.env`, `.env.*`           | Credenciales directas             |
| `config.py`, `settings.py` | Configuración con secretos        |
| `docker-compose.yml`       | Claves en environment             |
| `*.yaml`, `*.yml`          | Configuración con passwords       |
| `Dockerfile`               | ARG/ENV con secretos              |
| `*.json` (config)          | API keys embebidas                |
| `*.sql`                    | Passwords en scripts de migración |
| Historial git              | Secretos committeados previamente |

#### 1.3 Verificar .gitignore

Confirmar que estos patrones están presentes:

```gitignore
# Secretos — OBLIGATORIO
.env
.env.*
.env.local
.env.production
*.pem
*.key
*.p12
*.pfx
secrets/
credentials/

# Datos sensibles
*.sqlite
*.db
*.dump

# Entornos
.venv/
venv/
__pycache__/
*.pyc

# IDEs
.idea/
.vscode/settings.json
```

---

### Fase 2: Vulnerabilidades en Código

#### 2.1 Patrones de código peligroso

| Patrón                               | Severidad  | Riesgo                         | Solución                             |
| ------------------------------------ | ---------- | ------------------------------ | ------------------------------------ |
| `eval(input_usuario)`                | 🔴 Crítico | Ejecución de código arbitrario | `ast.literal_eval()` o validación    |
| `exec(string_dinámica)`              | 🔴 Crítico | Ejecución de código arbitrario | Refactorizar con dispatch dict       |
| `os.system(f"cmd {var}")`            | 🔴 Crítico | Inyección de comandos OS       | `subprocess.run([...], shell=False)` |
| `subprocess.run(cmd, shell=True)`    | 🟠 Alto    | Inyección de comandos OS       | `shell=False` + lista de args        |
| `pickle.load(fuente_externa)`        | 🟠 Alto    | Deserialización insegura (RCE) | `json.load()` o `msgpack`            |
| `yaml.load(data)`                    | 🟠 Alto    | Deserialización insegura       | `yaml.safe_load(data)`               |
| `f"SELECT ... {var}"`                | 🔴 Crítico | SQL Injection                  | Parámetros `?` o ORM                 |
| `cursor.execute(f"...")`             | 🔴 Crítico | SQL Injection                  | `cursor.execute("...?", (var,))`     |
| `requests.get(url, verify=False)`    | 🟡 Medio   | MITM / Bypass SSL              | Eliminar `verify=False`              |
| `hashlib.md5(password)`              | 🟡 Medio   | Hash débil                     | `bcrypt` o `argon2`                  |
| `random.random()` (para crypto)      | 🟡 Medio   | PRNG predecible                | `secrets.token_hex()`                |
| `tempfile.mktemp()`                  | 🟡 Medio   | Race condition                 | `tempfile.mkstemp()`                 |
| `xml.etree.ElementTree.parse()`      | 🟡 Medio   | XXE                            | `defusedxml`                         |
| `__import__(user_input)`             | 🔴 Crítico | Carga de módulo arbitrario     | Whitelist de módulos                 |
| `setattr(obj, user_input, val)`      | 🟠 Alto    | Manipulación de atributos      | Validar contra lista permitida       |
| `DEBUG = True` en producción         | 🟡 Medio   | Exposición de info interna     | `DEBUG = False`                      |
| `CORS(app, origins="*")`             | 🟡 Medio   | Acceso no restringido          | Lista explícita de orígenes          |
| Bare `except:` / `except Exception:` | 🔵 Bajo    | Oculta errores reales          | Excepciones específicas              |

#### 2.2 Búsqueda automatizada

Ejecutar `grep_search` con IsRegex=true para cada patrón crítico:

```bash
# Inyección SQL
grep -rn "execute.*f['\"]" --include="*.py"
grep -rn "execute.*%" --include="*.py"
grep -rn "execute.*\.format" --include="*.py"

# Ejecución de código
grep -rn "eval(" --include="*.py"
grep -rn "exec(" --include="*.py"
grep -rn "os\.system(" --include="*.py"
grep -rn "__import__(" --include="*.py"

# Shell injection
grep -rn "shell=True" --include="*.py"

# Deserialización insegura
grep -rn "pickle\.load" --include="*.py"
grep -rn "yaml\.load(" --include="*.py"

# SSL bypass
grep -rn "verify=False" --include="*.py"
grep -rn "verify\s*=\s*False" --include="*.py"

# CORS abierto
grep -rn 'origins.*\*' --include="*.py"
```

---

### Fase 3: Auditar Dependencias

```bash
# Opción 1: pip-audit (recomendado)
pip install pip-audit
pip-audit

# Opción 2: safety
pip install safety
safety check -r requirements.txt

# Verificar versiones obsoletas
pip list --outdated
```

**Verificar además:**

- ¿Hay dependencias sin versión fijada?
- ¿Hay dependencias abandonadas (sin actualizaciones >2 años)?
- ¿Hay dependencias con licencias incompatibles?

---

### Fase 4: Configuración y Deployment

Verificar estos puntos:

- [ ] `DEBUG = False` en configuración de producción
- [ ] `SECRET_KEY` cargada desde variable de entorno
- [ ] HTTPS forzado para producción
- [ ] Headers de seguridad configurados (HSTS, CSP, X-Frame-Options)
- [ ] Rate limiting implementado en endpoints públicos
- [ ] Logs no contienen datos sensibles (PII, passwords, tokens)
- [ ] Archivos estáticos servidos por CDN/nginx, no por Python
- [ ] `Dockerfile` usa imagen base minimal (alpine/slim)
- [ ] `Dockerfile` no copia `.env` ni secretos
- [ ] `docker-compose.yml` no hardcodea secretos (usa `env_file` o vault)

---

### Fase 5: Permisos y Acceso

- [ ] Principio de mínimo privilegio en accesos DB
- [ ] Autenticación implementada donde se requiere
- [ ] Autorización verificada por endpoint (no solo autenticación)
- [ ] Tokens con expiración adecuada
- [ ] Inputs de usuario validados y sanitizados
- [ ] Rate limiting en endpoints de login
- [ ] Protección contra fuerza bruta (lockout temporal)

---

## Output: Informe de Auditoría

Generar reporte con este formato:

````markdown
# 🕵️ Informe de Auditoría de Seguridad

**Fecha**: [FECHA]
**Proyecto**: [NOMBRE]
**Archivos analizados**: [NÚMERO]
**Líneas de código**: [NÚMERO aprox.]

---

## 📊 Resumen Ejecutivo

| Severidad  | Hallazgos |
| ---------- | --------- |
| 🔴 Crítico | X         |
| 🟠 Alto    | X         |
| 🟡 Medio   | X         |
| 🔵 Bajo    | X         |

---

## 🔴 Hallazgos Críticos

### [HALL-001] Título del hallazgo

- **Archivo**: `ruta/archivo.py:línea`
- **Severidad**: 🔴 Crítico
- **Categoría**: Secreto expuesto / SQL Injection / etc.
- **Descripción**: Explicación clara del riesgo
- **Código afectado**:
  ```python
  # Código vulnerable (SIN mostrar secretos reales)
  ```
````

- **Solución recomendada**:
  ```python
  # Código corregido
  ```
- **Impacto**: Qué podría pasar si se explota

---

## 🟠 Hallazgos Altos

[Mismo formato]

## 🟡 Hallazgos Medios

[Mismo formato]

## 🔵 Hallazgos Bajos

[Mismo formato]

---

## ✅ Verificaciones Pasadas

- [Lista de checks que están correctos]

---

## 📋 Plan de Acción Priorizado

1. 🔴 [Acción inmediata 1]
2. 🔴 [Acción inmediata 2]
3. 🟠 [Acción a corto plazo]
4. 🟡 [Mejora recomendada]

```

---

## Constraints

- **NUNCA** mostrar valores de secretos encontrados — solo indicar su ubicación
- **NUNCA** ejecutar código potencialmente malicioso para "verificar" vulnerabilidades
- **SIEMPRE** recomendar rotación inmediata si se detecta un secreto expuesto
- **SIEMPRE** clasificar por severidad usando el sistema 🔴🟠🟡🔵
- **PARAR** y alertar al usuario inmediatamente ante hallazgos críticos
- **PRIORIZAR** hallazgos que sean explotables remotamente
- Si un secreto fue committeado al historial de git, recomendar:
  1. Rotar la credencial **inmediatamente**
  2. Usar `git filter-branch` o [BFG Repo Cleaner](https://rtyley.github.io/bfg-repo-cleaner/) para limpiar historial
  3. Notificar al proveedor del servicio afectado
```
