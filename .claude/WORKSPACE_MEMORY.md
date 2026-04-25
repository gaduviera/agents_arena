---
name: WORKSPACE Technical Documentation
description: Internal technical reference for WORKSPACE architecture and workflow
type: reference
---

# WORKSPACE — Documentación Técnica Interna

Información completa sobre la estructura y flujo de trabajo del WORKSPACE compartido.

## Estructura esperada

```
WORKSPACE/
├── README.md          ← guía pública
├── STATE.md           ← tracking de sesión activa
├── codex/             ← cada agente crea su carpeta
├── gemini/
├── qwen/
└── ollama/
```

## Flujo de trabajo completo

1. **Asignación**: Usuario o orquestador define tarea → la asigna al WORKSPACE
2. **Auto-creación**: Cada agente recibe el brief y:
   - Crea su carpeta: `WORKSPACE/{agente}/`
   - Guarda `TASK.md` con el brief
3. **Ejecución**: Agente resuelve ejercicios dentro de su carpeta
4. **Tracking**: STATE.md se actualiza con progreso
5. **Reporte**: Agente crea `RESULT.md` y reporta a Claude
6. **Evaluación**: Claude evalúa y actualiza STATE.md

## Estructura típica dentro de carpeta de agente

```
WORKSPACE/{agente}/
├── TASK.md              # Brief (copiado de la tarea)
├── challenge.py         # Solución 1
├── index.html           # Solución 2
├── main.py              # Solución 3
├── RESULT.md            # Resumen: duración, errores, notas
└── (logs/outputs)       # Archivos generados durante ejecución
```

## STATE.md — Campos y actualización

**Se actualiza:**
- Al inicio: cuando se asigna nueva tarea
- Durante: cada agente reporta progreso
- Al fin: cuando completa (con timestamp_completed)

**Campos clave:**
- `task_name`: nombre único de la tarea
- `timestamp_assigned`: cuándo se asignó (ISO 8601)
- `status`: "pending" → "in_progress" → "completed"
- `agent_status.{agente}`: objeto con estado por agente

## Diferencia con ARENA_AGENTES/rounds/

| Aspecto | WORKSPACE | rounds/ |
|---------|-----------|---------|
| **Propósito** | Pruebas rápidas, experimentación | Evaluación formal, competencia |
| **Estructura** | Carpetas dinámicas por agente | Rondas con fecha fija |
| **Evaluación** | Informal, feedback rápido | Formal, scoring en 5 dimensiones |
| **Historial** | STATE.md simple | Evaluaciones guardadas por ronda |

## Cómo asignar una tarea (desde Claude)

```
1. Actualiza WORKSPACE/STATE.md → sección "Tarea Actual"
2. Despacha brief a cada agente con instrucciones
3. Agentes crean carpetas y trabajan
4. Actualiza STATE.md con progreso
5. Lee RESULT.md de cada agente
6. Resumen final en STATE.md
```

## Convenciones

- Nombres de carpeta: lowercase (`codex/`, no `Codex/`)
- Nombres de archivo: UPPERCASE para metadatos (`TASK.md`, `RESULT.md`)
- Estado: valores fijos (pending, in_progress, completed)
- Timestamps: siempre ISO 8601 UTC
