# 🕹️ SKILLS MAG (Multi-Agent Grid)

Orquestación central para el flujo multiagente.

| Skill | Comando | Descripción |
|---|---|---|
| **MAG Agent** | `/mag-agent` | Orquestador de alto nivel. Recibe tareas complejas y decide qué agentes deben participar según sus perfiles. |
| **MAG Dispatch** | `/mag-dispatch` | Motor de ejecución interna. Traduce las decisiones del orquestador en comandos específicos para cada plugin instalado. |
| **MAG Arena** | `/mag-arena` | Controlador de benchmark. Gestiona la rotación de tareas en el `ARENA_AGENTES`, recolecta métricas y actualiza el leaderboard. |

## Uso típico

1. El usuario invoca un "modo multiagente".
2. `mag-agent` analiza el `AGENT_CONTEXT/` para seleccionar candidatos.
3. `mag-dispatch` envía las tareas al `WORKSPACE/`.
4. `mag-arena` evalúa los resultados finales en 5 dimensiones.
