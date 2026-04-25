# Ollama — Contexto del Agente

**Modelo principal:** glm-5.1:cloud
**Modelos alternativos:** qwen3-coder:480b-cloud, minimax-m2.5:cloud
**Rol:** Implementación y código — segunda línea (open-weight)
**Última actualización:** 2026-04-25

## Fortalezas observadas
- Código cuando Codex no está disponible
- Análisis + código combinado (GLM-5.1 fuerte en ambos)
- Tareas sin dependencia de OpenAI
- Código complejo con qwen3-coder:480b-cloud

## Quirks conocidos
- Modelos `:cloud` requieren conexión activa al servicio Ollama Cloud
- Usar shell commands para escribir archivos (mismo patrón que Gemini/Qwen)
- Si glm-5.1 falla → intentar qwen3-coder:480b-cloud antes de escalar

## Comando
```bash
ollama-cc task "<prompt>" --model glm-5.1:cloud --auto -C "<working-dir>"
```

## Arena — Performance
**Rondas jugadas:** 0 | **Promedio:** — | **Mejor score:** —
**Dimensión más fuerte:** — | **Dimensión más débil:** —

## Notas del juez
*(se completa después de rondas)*
