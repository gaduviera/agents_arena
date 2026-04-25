# WORKSPACE STATE — Sesión Activa

**Última actualización:** 2026-04-25 17:25  
**Estado general:** Vacío, listo para recibir tareas

---

## Tarea Actual

| Campo | Valor |
|-------|-------|
| **Nombre** | — |
| **Brief** | — |
| **Agentes asignados** | — |
| **Timestamp asignación** | — |
| **Status** | — |

---

## Estado por Agente

| Agente | Carpeta | Status | Nota |
|--------|---------|--------|------|
| **Codex** | `WORKSPACE/codex/` | ⏳ pending | Listo para crear carpeta |
| **Gemini** | `WORKSPACE/gemini/` | ⏳ pending | Listo para crear carpeta |
| **Qwen** | `WORKSPACE/qwen/` | ⏳ pending | Listo para crear carpeta |
| **Ollama** | `WORKSPACE/ollama/` | ⏳ pending | Listo para crear carpeta |

---

## Historial de Sesiones

(Se registra cada tarea asignada)

```
[Sesión 1: 2026-04-25 — Sin tareas aún]
```

---

## Instrucciones para Agentes

Cuando recibas un brief del WORKSPACE:

1. Lee `WORKSPACE/README.md` para entender la estructura
2. **Crea tu carpeta**: `WORKSPACE/{tu_nombre}/` (ej: `WORKSPACE/codex/`)
3. Dentro de tu carpeta:
   - Guarda el brief en `TASK.md`
   - Resuelve las tareas (archivos según brief)
   - Al terminar, crea `RESULT.md` con resumen
4. **Reporta a Claude** con ruta completa: `WORKSPACE/{tu_nombre}/RESULT.md`
5. Claude actualiza este STATE.md

---

## Cómo Asignar una Tarea

```bash
# Usuario corre algo como:
# /multiagente brief="..." workspace=true
# O simplemente:
# Claude: "lanzo esta tarea al WORKSPACE para los 4 agentes"
```

Claude:
1. Actualiza la sección "Tarea Actual" arriba
2. Despacha el brief a cada agente con instrucciones de carpeta
3. Espera reportes en `WORKSPACE/{agente}/RESULT.md`
4. Actualiza esta tabla de Status
