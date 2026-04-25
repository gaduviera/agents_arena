# AGENT_CONTEXT — Sistema de Memoria Entre Sesiones

Archivos on-demand. **No se auto-cargan** — costo cero hasta que los pedís explícitamente.

## Principio de costo de tokens

| Ubicación | Se carga | Costo por sesión |
|-----------|----------|-----------------|
| `memory/MEMORY.md` | Automático siempre | Bajo (mantener <200 líneas) |
| `AGENT_CONTEXT/**` | Solo cuando pedís | Cero hasta que se lee |
| `ARENA_AGENTES/STATE.md` | Solo cuando pedís | Cero hasta que se lee |

## Cuándo cargar cada archivo

- "¿cómo estaba el arena?" → `ARENA_AGENTES/STATE.md`
- "¿qué sé de Codex?" → `AGENT_CONTEXT/agents/codex.md`
- "¿qué pasó en la última sesión?" → `AGENT_CONTEXT/sessions/latest.md`

## Estructura

```
AGENT_CONTEXT/
├── README.md              ← este archivo (índice)
├── agents/
│   ├── codex.md          # Comportamiento observado, fortalezas, quirks
│   ├── gemini.md
│   ├── qwen.md
│   └── ollama.md
└── sessions/
    └── latest.md         # Resumen de la última sesión de trabajo
```

## Cómo actualizar

Al cerrar una sesión importante, pedirle a Claude:
> "actualiza AGENT_CONTEXT con lo que aprendiste hoy"

Claude actualiza `sessions/latest.md` y los archivos de agente relevantes.
