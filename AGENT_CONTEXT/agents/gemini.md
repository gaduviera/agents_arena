# Gemini — Contexto del Agente

**Modelo:** gemini-2.5-flash
**Rol:** UI/HTML, creatividad, tareas simples bien delimitadas
**Última actualización:** 2026-04-25

## Fortalezas observadas
- HTML/CSS/UI — dashboards, componentes visuales
- Render/format functions para terminal
- Tareas creativas sin dependencia de código existente
- Sin skills directory → nunca carga brainstorming en headless

## Quirks conocidos
- NO tiene `write_file` — crea archivos via shell commands (comportamiento natural)
- Adaptación de referencia: tiende a crear su propia versión en lugar de adaptar fielmente
- Para adaptación: usar COPY/ADD/DO NOT OMIT explícitamente
- Verificar exports con `grep "^export"` post-ejecución

## Comando
```bash
gemini --approval-mode yolo -p "<prompt>"
```

## Arena — Performance
**Rondas jugadas:** 0 | **Promedio:** — | **Mejor score:** —
**Dimensión más fuerte:** — | **Dimensión más débil:** —

## Notas del juez
*(se completa después de rondas)*
