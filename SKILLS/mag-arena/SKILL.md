---
name: mag-arena
description: Skill de sesión para la Arena de Agentes en PERSONAL_AGENT. Invocar al inicio de cada sesión nueva en el proyecto PERSONAL_AGENT, cuando el usuario quiera lanzar o gestionar rondas del benchmark, o cuando mencione "arena", "ronda", "benchmark de agentes", "testear agentes", "lanzar ronda". Cubre: carga de estado del arena, planificación de ronda, dispatch paralelo de los 4 agentes con el mismo brief, evaluación de outputs, scoring y actualización del leaderboard. SIEMPRE usar este skill para cualquier actividad relacionada con el arena de agentes.
---

# MAG Arena — Sesión de Benchmark

Claude actúa como **juez y orquestador**. Objetivo: ejecutar rondas eficientemente, minimizar tokens, maximizar calidad de evaluación.

## Raíz del proyecto

```
C:/Users/Gabriel Duarte Viera/Documents/Claude/Projects/PERSONAL_AGENT/
├── ARENA_AGENTES/
└── AGENT_CONTEXT/
```

---

## Paso 1 — Inicio de sesión

Cargar siempre al activar este skill:

```
Leer: ARENA_AGENTES/STATE.md
Leer: ARENA_AGENTES/scores/leaderboard.md
```

Mostrar al usuario: ronda activa, líder actual, pendientes.

Preguntar: **"¿Lanzamos una nueva ronda?"** — proponer combinación según progreso.

---

## Paso 2 — Planificación de ronda

Leer `ARENA_AGENTES/tasks/bank.md` y proponer:

| Ronda | Code | HTML | Chart | Dificultad |
|-------|------|------|-------|-----------|
| 1 | A1 | B1 | C1 | Baja — calibración |
| 2 | A2 | B2 | C2 | Media |
| 3 | A3 | B3 | C3 | Alta |

El usuario confirma o elige otra combinación.

---

## Paso 3 — Crear estructura de la ronda

```bash
FECHA=$(date +%Y-%m-%d)
BASE="C:/Users/Gabriel Duarte Viera/Documents/Claude/Projects/PERSONAL_AGENT/ARENA_AGENTES/rounds/$FECHA"
mkdir -p "$BASE/codex" "$BASE/ollama" "$BASE/gemini" "$BASE/qwen"
```

Crear `$BASE/BRIEF.md` desde `ARENA_AGENTES/rounds/TEMPLATE.md` con las tareas elegidas.

---

## Paso 4 — Dispatch paralelo

Usar `/mag-dispatch` para comandos exactos por agente.

Despachar los 4 agentes en **UN SOLO MENSAJE** (4 Bash calls en paralelo).

**Prompt base** (adaptar descripción de tareas según el banco):

```
Arena de Agentes — Ronda [FECHA]
Working directory: [BASE]/[agente]/

Tarea 1 — challenge.py:
[descripción completa tarea A]

Tarea 2 — index.html:
[descripción completa tarea B]
Sin frameworks. CSS embebido.

Tarea 3 — main.py:
[descripción completa tarea C]
Ejecutable con `python main.py`. Solo matplotlib y stdlib.

Crear los 3 archivos en el working directory.
Usar shell commands para escribir archivos.
Skip all skills. Execute directly.
```

---

## Paso 5 — Verificación

```bash
ls "$BASE/codex/" && ls "$BASE/ollama/" && ls "$BASE/gemini/" && ls "$BASE/qwen/"
```

Cada agente debe tener: `challenge.py`, `index.html`, `main.py`.
Si falta algún archivo → re-despachar ese agente con prompt más explícito.
Intentar `python main.py` para verificar que el chart corre.

---

## Paso 6 — Scoring

Para cada agente, evaluar cada archivo en 5 dimensiones (1–5):

| Dimensión | Qué verificar |
|-----------|--------------|
| **Correctness** | ¿Corre sin errores? ¿Output esperado? |
| **Completeness** | ¿Todos los requisitos del BRIEF? |
| **Code quality** | Limpieza, nombres, estructura |
| **Creativity** | Soluciones elegantes, enfoques inesperados |
| **Efficiency** | Conciso, sin boilerplate innecesario |

**Total por agente** = Σ(5 dimensiones × 3 tareas) — máx 75 pts.

---

## Paso 7 — Crear RESULTS.md

Crear `$BASE/RESULTS.md`:

```markdown
# Resultados — Ronda [FECHA]
**Tareas:** [A?] + [B?] + [C?]

| Agente | challenge.py /25 | index.html /25 | main.py /25 | Total /75 |
|--------|-----------------|----------------|-------------|-----------|
| Codex  | | | | |
| Ollama | | | | |
| Gemini | | | | |
| Qwen   | | | | |

**Ganador:** [agente] — [pts] pts

## Observaciones del juez
[Por agente: fortalezas y debilidades observadas esta ronda]
```

---

## Paso 8 — Actualizar estado

```
Actualizar:
- ARENA_AGENTES/scores/leaderboard.md  ← agregar ronda, recalcular totales
- ARENA_AGENTES/STATE.md               ← ronda completada, nuevo líder
- AGENT_CONTEXT/sessions/latest.md     ← resumen de sesión
- AGENT_CONTEXT/agents/[agente].md     ← solo si se aprendió algo nuevo
```

---

## Principios de ahorro de tokens

- Cargar STATE.md y leaderboard al inicio — son pequeños y necesarios
- No cargar tasks/bank.md completo si ya elegiste la combinación
- RESULTS.md se crea local — no necesita estar en contexto luego de creado
- Actualizar archivos de agente solo cuando hay aprendizaje real nuevo
