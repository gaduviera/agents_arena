# Arena de Agentes

Benchmark paralelo donde Codex, Gemini, Qwen y Ollama reciben el mismo brief y compiten.
Claude actúa como juez y orquestador: despacha en paralelo, evalúa outputs, actualiza scores.

## Estructura de una ronda

```
rounds/2026-04-25/
├── BRIEF.md          # La tarea — idéntica para todos los agentes
├── RESULTS.md        # Evaluación del juez (Claude) post-ronda
├── codex/
├── gemini/
├── qwen/
└── ollama/
```

## Tareas por ronda (siempre las 3)

| Archivo | Descripción |
|---------|-------------|
| `challenge.py` | Reto de código Python |
| `index.html` | Página HTML sin frameworks |
| `main.py` | Gráfico con matplotlib — `python main.py` |

## Criterios de scoring (1–5 por dimensión)

| Dimensión | Descripción |
|-----------|-------------|
| **Correctness** | Corre sin errores, produce el output esperado |
| **Completeness** | Cumple todos los requisitos del BRIEF |
| **Code quality** | Limpieza, nombres, estructura |
| **Creativity** | Enfoques inesperados, soluciones elegantes |
| **Efficiency** | Conciso, mínimo boilerplate |

**Máximo por tarea:** 25 pts | **Máximo por ronda (3 tareas):** 75 pts

## Archivos clave

- `tasks/bank.md` — banco de tareas disponibles por categoría
- `STATE.md` — estado actual del arena (ronda activa, pendientes)
- `scores/leaderboard.md` — puntajes acumulados por agente
- `rounds/TEMPLATE.md` — plantilla para crear un nuevo BRIEF
