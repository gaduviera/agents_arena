# Qwen — Contexto del Agente

**Modelo:** coder-model (Alibaba)
**Rol:** Análisis, investigación, debugging
**Última actualización:** 2026-04-25

## Fortalezas observadas
- Explorar código y reportar hallazgos
- Debugging e investigación de causa raíz
- Análisis y comparación de arquitecturas
- Generar 1–3 archivos de documentación/análisis

## Quirks conocidos
- `write_file` falla silenciosamente — siempre usar shell commands en el prompt
- Límite de sesión: exit code 53 si supera ~8–10 turns internos
- Máximo 3 archivos por sesión — más de eso usar Codex
- Puede "alucinar" que creó archivos — verificar siempre con `ls`

## Comando
```bash
qwen -p "<prompt>" -y
```

## Arena — Performance
**Rondas jugadas:** 0 | **Promedio:** — | **Mejor score:** —
**Dimensión más fuerte:** — | **Dimensión más débil:** —

## Notas del juez
*(se completa después de rondas)*
