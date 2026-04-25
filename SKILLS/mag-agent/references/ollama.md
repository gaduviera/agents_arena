# Ollama — Referencia de Delegación

## Identidad
- **Modelo principal:** glm-5.1:cloud
- **Modelos alternativos (en orden de preferencia):**
  1. `glm-5.1:cloud` — modelo principal, razonamiento general
  2. `qwen3-coder:480b-cloud` — especializado en código complejo
  3. `minimax-m2.5:cloud` — alternativa para tareas creativas y multimodal
- **Rol:** Implementación y código — segunda línea después de Codex
- **Fortaleza:** Código, razonamiento estructurado, tareas que requieren modelos open-weight potentes

## Comando de ejecución

```bash
ollama-cc task "<prompt>" --model glm-5.1:cloud --auto -C "<working-dir>"
```

**Flags clave:**
- `--model glm-5.1:cloud` → selecciona el modelo cloud
- `--auto` → aprueba automáticamente todas las herramientas (no pregunta)
- `-C "<path>"` → cambia al directorio antes de ejecutar

**Alternar modelo si el principal falla:**
```bash
# Si glm-5.1:cloud falla o está lento, usar:
ollama-cc task "<prompt>" --model qwen3-coder:480b-cloud --auto -C "<working-dir>"
```

## Config relevante
- `~/.ollama/config.json` → credenciales y configuración cloud
- `AGENTS.md` en raíz del proyecto → le dice que skip skills en headless
- Los modelos `:cloud` requieren conexión activa al servicio Ollama Cloud

## Cuándo usar Ollama (en el orden de agentes)

**Posición en el flujo:** Segunda opción después de Codex.

**Ideal para:**
- Implementación cuando Codex no está disponible o falla
- Código que aprovecha modelos open-weight de alto rendimiento (GLM, Qwen-Coder)
- Tareas donde se prefiere un modelo alternativo a OpenAI
- Análisis + código combinado (GLM-5.1 es fuerte en ambos)

**Con `qwen3-coder:480b-cloud` — usar para:**
- Código muy complejo que requiere razonamiento profundo
- Refactors grandes con múltiples archivos
- Algoritmos y lógica de negocio exigente

**Con `minimax-m2.5:cloud` — usar para:**
- Tareas creativas o de generación de texto/docs
- Cuando los otros modelos tienen problemas con el input

## Cómo armar el prompt

Los modelos Ollama cloud usan shell commands para crear archivos.

**Incluir siempre:**
```
[TAREA]
Skip all skills. Execute directly.
Use shell commands (cat <<EOF, echo) to create files.
```

**Para implementación de código:**
```bash
ollama-cc task "Create <archivo> in <directorio> with [descripción funcional]. Use shell commands to write files. Skip all skills. Execute directly." --model glm-5.1:cloud --auto -C "<working-dir>"
```

**Para código complejo (usar qwen3-coder):**
```bash
ollama-cc task "Read <referencia> first, then create <destino> adapting: [cambios específicos]. Skip all skills. Execute directly. Use shell commands." --model qwen3-coder:480b-cloud --auto -C "<working-dir>"
```

## Verificar resultado

```bash
ls "<working-dir>/ruta-esperada/"
```

Si el modelo no creó el archivo esperado, verificar primero:
```bash
# Listar modelos disponibles
ollama-cc list-models
```

## Advertencias
- Los modelos `:cloud` requieren conexión — verificar disponibilidad si hay timeout
- Si `glm-5.1:cloud` no está disponible localmente, intentar con `qwen3-coder:480b-cloud`
- Mismo patrón que Gemini/Qwen: usar shell commands para escribir archivos
- Paths con espacios deben ir entre comillas dobles

## Comparación con otros agentes

| Tarea | Codex | Ollama (glm-5.1) | Gemini | Qwen |
|-------|-------|-------------------|--------|------|
| Código complejo | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐ | ⭐⭐ |
| Múltiples archivos | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐ | ❌ |
| Análisis + código | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐⭐ |
| UI/HTML | ⭐⭐⭐ | ⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐ |
| Sin depender de OpenAI | ❌ | ✅ | ✅ | ✅ |
