# Codex — Contexto del Agente

**Modelo:** gpt-5.4
**Rol:** Implementación y código — primera línea
**Última actualización:** 2026-04-25

## Fortalezas observadas
- Código complejo con lógica de negocio
- Múltiples archivos relacionados en una sola sesión
- Tests (node:test, jest, vitest)
- Adaptación desde archivo de referencia existente

## Quirks conocidos
- Sin `CODEX.md` en el directorio puede cargar brainstorming skill y bloquearse
- Requiere `--full-auto` obligatorio en headless
- Paths con espacios deben ir entre comillas dobles

## Comando
```bash
codex exec "<prompt>" --full-auto --skip-git-repo-check -C "<working-dir>"
```

## Arena — Performance
**Rondas jugadas:** 0 | **Promedio:** — | **Mejor score:** —
**Dimensión más fuerte:** — | **Dimensión más débil:** —

## Notas del juez
*(se completa después de rondas)*
