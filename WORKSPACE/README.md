# WORKSPACE — Área de Trabajo Compartida

Espacio donde cada agente crea su propia carpeta para trabajar en tareas asignadas.

## Estructura esperada

```
WORKSPACE/
├── README.md          ← este archivo
├── STATE.md           ← estado actual: quién está trabajando en qué
├── codex/             ← cada agente crea su propia carpeta
├── gemini/
├── qwen/
└── ollama/
```

## Flujo de trabajo

1. **Asignación**: Usuario (o juez Claude) define una tarea y la asigna a todos los agentes
2. **Auto-creación**: Cada agente, al recibir el brief:
   - Crea su propia carpeta en WORKSPACE si no existe: `WORKSPACE/{agente}/`
   - Dentro, organiza el trabajo como considere necesario
3. **Ejecución**: Agente trabaja dentro de su carpeta
4. **Actualización**: STATE.md se actualiza con progreso/estado
5. **Reporte**: Cuando termina, agente reporta resultado con ruta completa

## Estructura típica dentro de carpeta de agente

```
WORKSPACE/codex/
├── TASK.md                # Brief de la tarea
├── challenge.py           # Solución 1
├── index.html             # Solución 2
├── main.py                # Solución 3
└── RESULT.md              # Resumen de ejecución (duración, errores, notas)
```

## STATE.md — Tracking de sesión

Actualizado al inicio y fin de cada tarea:
- Qué tarea está en progreso
- Cuál agente está trabajando
- Timestamp inicio/fin
- Estado (pending, in_progress, completed)

---

**Nota**: El WORKSPACE se complementa con `ARENA_AGENTES/rounds/` para rondas formales con evaluación. Este es el espacio de prueba libre.
