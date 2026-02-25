---
description: Registro maestro de capacidades (Skills) y guía de autoría para agentes
globs: [".agent/skills/**/*"]
---

# 🧩 Skills Registry & Authoring Protocol

Este manifiesto define las capacidades especializadas disponibles en el proyecto y las normas para crear nuevas.
**Objetivo**: Evitar "Context Rot" mediante carga dinámica de instrucciones (Progressive Disclosure)[cite: 11, 12].

## 📂 Directorio de Skills

Todas las skills del proyecto residen en: `/.agent/skills/`.
Cada skill es una carpeta que DEBE contener:

1.  `SKILL.md`: Definición y disparadores (Metadata)[cite: 20].
2.  `scripts/`: Lógica de ejecución (Python/Bash)[cite: 25, 74].
3.  `references/`: (Opcional) Plantillas o documentación estática[cite: 43].

## 📑 Catálogo de Skills Activas

| Skill Name       | Trigger / Descripción                                                                      | Estado    |
| :--------------- | :----------------------------------------------------------------------------------------- | :-------- |
| `cosmos-expert`  | "Ayuda con Cosmos", "Código Cosmos". Experto en lenguaje Cosmos (Base100) usando KB local. | 🟢 Active |
| `daily-context`  | "Actualiza el contexto", `/daily-context`. Genera/actualiza `CONTEXT.md` al final del día. | 🟢 Active |
| `python-expert`  | "Proyecto Python", "Depurar Python". Experto en Python moderno, venv y mejores prácticas.  | 🟢 Active |
| `security-audit` | "Audita seguridad", `/security-audit`. Detecta secretos expuestos y código inseguro.       | 🟢 Active |

## 🏗️ Protocolo de Creación (Authoring Standard)

Cuando el usuario pida "Crea una skill para X", sigue estrictamente esta estructura:

### 1. Definición (SKILL.md)

El "cerebro" de la skill. Debe incluir Frontmatter YAML para el enrutador semántico[cite: 46, 48].

```markdown
---
name: [nombre-kebab-case]
description: [FRASE DISPARADORA PRECISA]. Use this skill when the user asks to [INTENCIÓN ESPECÍFICA]. [cite: 59]
---

# [Nombre Legible]

Goal: [Qué logra esta skill]

## Instructions

1. Step-by-step logic.
2. Use `run_command` to execute scripts in `scripts/`.

## Constraints

- Do not output raw secrets.
```
