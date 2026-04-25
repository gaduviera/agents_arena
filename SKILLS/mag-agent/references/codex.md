# Codex — Referencia de Delegación

## Identidad
- **Modelo:** gpt-5.4
- **Rol:** Implementación y creación de código — el agente más confiable para tareas complejas
- **Fortaleza:** Crear archivos, adaptar código de referencia, features completas, tests, refactors

## Comando de ejecución

```bash
codex exec "<prompt>" --full-auto --skip-git-repo-check -C "<working-dir>"
```

**Flags clave:**
- `--full-auto` → aprueba automáticamente todas las herramientas (no pregunta)
- `--skip-git-repo-check` → no falla si el directorio no es un repo git
- `-C "<path>"` → cambia al directorio antes de ejecutar

## Config relevante
- `~/.codex/config.toml` — tiene `trust_level = "trusted"` para el proyecto actual
- `CODEX.md` en raíz del proyecto → le dice que skip skills en exec mode

## Cuándo usar Codex (MVP)

**Ideal para:**
- Código complejo que requiere entender patrones y adaptarlos (e.g. "adaptá el client de qwen para hacer REST")
- Crear múltiples archivos relacionados (lib/ directory, scripts, tests)
- Adaptación de código a partir de un archivo de referencia existente
- Tests (node:test, jest, vitest) — excelente con fixtures y mocks
- Entry points / main scripts con múltiples handlers
- Cualquier tarea que requiera razonar sobre lógica de negocio

**Rendimiento esperado:** ⭐⭐⭐⭐⭐ para código, ⭐⭐⭐⭐⭐ para tests

## Cómo armar el prompt

Codex tiene `write_file` tool disponible en `--full-auto`. Puede crear archivos directamente.

**Incluir siempre:**
```
[TAREA]
Skip all skills (brainstorming, using-superpowers). Execute directly. Create files immediately.
```

**Para tareas de adaptación de referencia — dar el archivo fuente explícito:**
```
Read <ruta-al-archivo-fuente> first, then create <ruta-destino> adapting it for [X].
Key changes: [listar cambios específicos]
Keep: [qué preservar igual]
Replace: [qué cambiar y por qué]
Skip all skills. Execute directly.
```

**Para múltiples archivos — listar cada uno:**
```
Create the following files in <working-dir>/scripts/lib/:
1. ollama-client.mjs — OllamaClient class with ping(), generate(), listModels()
2. ollama-models.mjs — exports: SUPPORTED_MODELS, listLocalModels(), validateModel()
Read ../reference-project/similar-file.mjs as reference for patterns.
Skip all skills. Execute directly.
```

**Ejemplo completo:**
```bash
codex exec "Read C:/Projects/qwen-plugin/scripts/qwen-companion.mjs as reference, then create plugins/ollama/scripts/ollama-companion.mjs adapting it: replace ACP/stdio with REST fetch calls to localhost:11434, replace qwenClient with OllamaClient from lib/ollama-client.mjs, replace login flow with ping() check. Keep the background spawn pattern for task-worker identical. Skip all skills. Execute directly." --full-auto --skip-git-repo-check -C "C:/Projects/ollama-plugin"
```

## Verificar resultado

Después de ejecutar, verificar con:
```bash
ls "<working-dir>/scripts/lib/"
```
O usar `Read` tool para confirmar contenido.

## Advertencias
- Si no tiene CODEX.md en el directorio, puede cargar brainstorming skill y bloquearse
- Si el path tiene espacios, poner entre comillas dobles
- No funciona bien sin `--full-auto` en headless
- Para prompts muy largos en bash, usar heredoc o archivo temporal

## Comparación con otros agentes

| Tarea | Codex | Gemini | Qwen |
|-------|-------|--------|------|
| Código complejo | ⭐⭐⭐⭐⭐ | ⭐ | ⭐⭐ |
| Adaptación de referencia | ⭐⭐⭐⭐⭐ | ⭐⭐ | ⭐⭐ |
| Tests | ⭐⭐⭐⭐⭐ | ⭐ | ⭐⭐ |
| Múltiples archivos similares | ⭐⭐⭐⭐ | ⭐⭐⭐ | ❌ (limit) |
| UI/Rendering/HTML | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐ |
