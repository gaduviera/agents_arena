# Qwen — Referencia de Delegación

## Identidad
- **Modelo:** coder-model (Alibaba)
- **Rol:** Análisis, investigación y debugging
- **Fortaleza:** Explorar código, buscar problemas, investigar patrones, research

## Comando de ejecución

```bash
qwen -p "<prompt>" -y
```

**Flags clave:**
- `-p "<prompt>"` → modo headless (non-interactive)
- `-y` → auto-aprueba todas las herramientas

## Config relevante
- `~/.qwen/settings.json` → ya tiene `"approvalMode": "yolo"`
- `AGENTS.md` en raíz del proyecto → le dice que skip skills en headless
- **NO usar `write_file` tool** → falla silenciosamente en Qwen

## Cuándo usar Qwen

**Ideal para:**
- Analizar código existente y reportar hallazgos
- Investigar por qué algo falla (debugging)
- Leer y resumir arquitectura de un proyecto
- Comparar implementaciones entre archivos
- Generar 1-3 archivos de análisis/documentación (no más)

**EVITAR para:**
- Crear más de 3 archivos en una sola sesión → riesgo de hit session limit (exit code 53)
- Tareas repetitivas de bulk file creation (e.g. "creá 10 command files")
- Tareas que requieren adaptar código complejo de referencia
- Cualquier tarea donde el output principal son archivos de código

**Rendimiento esperado:** ⭐⭐⭐⭐⭐ para análisis, ❌ para bulk creation

## Límite de sesión — IMPORTANTE

Qwen tiene un límite de turns por sesión. Con tareas largas que requieren crear muchos archivos:
- **Sale con exit code 53** (max session turns)
- Puede completar parcialmente (e.g. 5/10 archivos) sin aviso
- **Siempre verificar** con `ls` cuántos archivos creó realmente

**Regla práctica:** Si la tarea requiere crear más de 3 archivos, dividirla en subtareas o usar Codex.

## Cómo armar el prompt

Qwen tiene problemas con `write_file`. Debe usar comandos shell para crear archivos.

**Incluir siempre:**
```
[TAREA]
Skip all skills. Execute directly.
Use shell commands (cat <<EOF, echo) to create files instead of write_file tool.
```

**Para análisis (el uso ideal):**
```bash
qwen -p "Analyze the files in plugins/qwen/scripts/lib/ and produce a detailed comparison of how state management works vs what we need in plugins/ollama/scripts/lib/. Write findings to analysis/state-comparison.md using shell commands. Skip all skills. Execute directly." -y
```

**Para crear pocos archivos (máx 3):**
```bash
qwen -p "Create 2 command files in plugins/ollama/commands/: setup.md and review.md. Pattern to follow: each file has frontmatter (description, argument-hint, allowed-tools), then a bash block calling ollama-companion.mjs with the command name, then parse instructions. Use cat <<EOF to write files. Skip all skills." -y
```

**Si necesita crear más de 3 archivos — PARTIR EN TANDAS:**
```bash
# Tanda 1 (3 archivos)
qwen -p "Create setup.md, review.md, task.md in plugins/ollama/commands/..." -y
# Verificar
ls plugins/ollama/commands/
# Tanda 2 (3 archivos más)
qwen -p "Create result.md, status.md, cancel.md in plugins/ollama/commands/..." -y
```

## Verificar resultado

Qwen puede "alucinar" que creó archivos sin haberlo hecho realmente. Siempre verificar:
```bash
ls "<working-dir>/ruta-esperada/"
```

Si el exit code fue 53, verificar exactamente qué llegó a crear y hacer el resto manualmente o con Codex.

## Advertencias
- `write_file` falla silenciosamente — Qwen dice que lo creó pero no existe
- Siempre incluir "use shell commands" en el prompt
- Funciona bien para análisis, mal para output de archivos sin instrucción explícita
- **Session limit real:** más de ~8-10 turns internos → exit code 53 sin completar
- Delegar bulk file creation a Codex, no a Qwen
