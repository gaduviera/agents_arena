---
name: mag-dispatch
description: Motor de ejecución de agentes para el sistema multiagente. Encapsula comandos bash exactos, construcción de prompts y verificación post-dispatch para Codex, Ollama (glm-5.1:cloud), Gemini y Qwen. Invocar cuando se necesita ejecutar uno o más agentes con una tarea concreta — desde /multiagente, /mag-arena-dispatch, o cualquier workflow de dispatch. Cubre comandos exactos por agente, reglas de paralelismo, templates de prompt, y verificación de outputs. SIEMPRE usar este skill antes de despachar cualquier agente.
---

# MAG Dispatch — Motor de Ejecución de Agentes

Encapsula el CÓMO ejecutar agentes. El orquestador decide QUÉ y QUIÉN — este skill ejecuta.

## Comandos por agente

### Codex (primera opción)
```bash
codex exec "<prompt>" --full-auto --skip-git-repo-check -C "<working-dir>"
```
- `--full-auto` obligatorio en headless
- Tiene `write_file` disponible — puede crear archivos directamente
- Sin `CODEX.md` en el directorio puede cargar brainstorming skill y bloquearse

### Ollama — glm-5.1:cloud (segunda opción)
```bash
ollama-cc task "<prompt>" --model glm-5.1:cloud --auto -C "<working-dir>"
```
- Fallback si falla: `--model qwen3-coder:480b-cloud`
- Usar shell commands para escribir archivos
- Requiere conexión activa al servicio Ollama Cloud

### Gemini (UI/HTML)
```bash
gemini --approval-mode yolo -p "<prompt>"
```
- Sin `write_file` — usa shell commands internamente
- Para adaptación desde referencia: usar COPY/ADD/DO NOT OMIT explícitamente

### Qwen (análisis/investigación)
```bash
qwen -p "<prompt>" -y
```
- Máximo 3 archivos por sesión (exit code 53 si supera ~8-10 turns)
- Usar `cat <<EOF` para crear archivos — write_file falla silenciosamente
- Verificar SIEMPRE con `ls` que los archivos existen

## Template de prompt base

```
[TAREA ESPECÍFICA]
Working directory: [path absoluto]
Output: Crear [archivo(s)] en [carpeta/]
Restricciones:
  - NO modificar archivos fuera de los listados
  - Usar shell commands para escribir archivos
  - Bug fuera de scope: reportar sin tocar
Skip all skills (brainstorming, using-superpowers). Execute directly.
```

## Regla de paralelismo

Verificar ANTES de cada dispatch: ¿las tareas comparten archivos?
- **NO comparten** → un solo mensaje con múltiples Bash calls (paralelo real)
- **Comparten** → secuencial

NUNCA usar `run_in_background: true` con agentes rescue — bloquea aprobaciones.

## Verificación post-dispatch (OBLIGATORIO)

```bash
ls "<working-dir>/ruta-esperada/"
```

Si falta el archivo: re-despachar con prompt más explícito o completar manualmente.

## Orden de preferencia

| # | Agente | Cuándo |
|---|--------|--------|
| 1 | Codex | Código complejo, múltiples archivos, tests |
| 2 | Ollama glm-5.1 | Codex no disponible, preferencia open-weight |
| 3 | Gemini | UI/HTML, tareas visuales y creativas |
| 4 | Qwen | Análisis, debugging (máx 3 archivos) |
