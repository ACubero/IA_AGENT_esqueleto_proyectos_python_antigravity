---
name: daily-context
description: Genera o actualiza el archivo CONTEXT.md con el resumen del día de trabajo para mantener la memoria entre sesiones.
---

# 🧠 Daily Context Generator

## Propósito

Esta skill permite al usuario invocar un agente que genera o actualiza un archivo `CONTEXT.md` en la raíz del proyecto. Este archivo actúa como la "memoria persistente" del proyecto, capturando:

- Estado actual del desarrollo
- Tareas pendientes y deuda técnica
- Reglas y convenciones del proyecto
- Resumen de la última sesión de trabajo

---

## Cuándo Invocar

Invoca esta skill al **final de tu jornada de trabajo** con:

```
/daily-context
```

O pidiendo: _"Actualiza el contexto del proyecto"_ / _"Genera el resumen del día"_

---

## Proceso de Ejecución

### Paso 1: Analizar el estado actual

1. **Leer `CONTEXT.md` existente** (si existe) para mantener historial.
2. **Revisar archivos modificados** en la sesión actual.
3. **Identificar tareas completadas** desde `task.md` o commits recientes.
4. **Detectar deuda técnica** (TODOs, FIXMEs, código comentado).

### Paso 2: Recopilar información

Preguntar al usuario (si es necesario):

1. ¿Cuál fue el foco principal de trabajo hoy?
2. ¿Qué quedó pendiente para la próxima sesión?
3. ¿Hay alguna decisión técnica importante que documentar?
4. ¿Algún bloqueo o dependencia externa?

### Paso 3: Generar/Actualizar CONTEXT.md

Crear o actualizar el archivo siguiendo esta estructura:

```markdown
# 🧠 Project Context & Memory

> Última actualización: [FECHA Y HORA]

## 📍 Estado Actual

[Descripción breve de la fase actual del proyecto]

## ✅ Última Sesión ([FECHA])

### Completado

- [Lista de tareas terminadas]

### En progreso

- [Lista de tareas iniciadas pero no terminadas]

### Pendiente para próxima sesión

- [Lista de tareas a retomar]

## ⚠️ Deuda Técnica

| Archivo   | Tipo       | Descripción   |
| --------- | ---------- | ------------- |
| [archivo] | TODO/FIXME | [descripción] |

## 📏 Reglas de Oro

- [Regla 1 del proyecto, ej: "No usar `any` en TypeScript"]
- [Regla 2, ej: "Tests obligatorios para nuevas funciones"]
- [Regla 3, ej: "Comentarios en español"]

## 🔗 Dependencias y Bloqueos

- [Dependencias externas o bloqueos actuales]

## 📚 Historial de Sesiones

<details>
<summary>[FECHA ANTERIOR] - [Título resumen]</summary>

[Resumen de la sesión anterior, colapsado para no ocupar espacio]

</details>
```

### Paso 4: Confirmar con el usuario

1. Mostrar preview del `CONTEXT.md` actualizado.
2. Pedir confirmación antes de guardar.
3. Sugerir añadir regla en `Settings > Agent Behavior`:
   > "Al iniciar, lee siempre el archivo CONTEXT.md antes de responder."

---

## Ubicación del Archivo

El archivo se genera en: `[RAÍZ_PROYECTO]/CONTEXT.md`

---

## Ejemplo de Uso

**Usuario**: "Actualiza el contexto del proyecto antes de irme"

**Agente**:

1. Lee el CONTEXT.md existente
2. Analiza los cambios del día (archivos modificados, commits)
3. Pregunta: "¿Qué quedó pendiente para mañana?"
4. Genera el nuevo CONTEXT.md
5. Muestra preview y pide confirmación
6. Guarda el archivo

---

## Notas Importantes

- **No sobrescribir historial**: Las sesiones anteriores van en sección colapsable `<details>`.
- **Mantener conciso**: El archivo debe ser legible en <30 segundos.
- **Priorizar accionables**: Enfocarse en qué hacer mañana, no en detalles técnicos.
