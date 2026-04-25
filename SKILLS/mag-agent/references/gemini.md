# Gemini — Referencia de Delegación

## Identidad
- **Modelo:** gemini-2.5-flash (Google)
- **Rol:** Diseño, frontend, creatividad y tareas shell simples
- **Fortaleza:** HTML/CSS/UI, generación creativa, render functions, output formatting

## Comando de ejecución

```bash
gemini --approval-mode yolo -p "<prompt>"
```

**Flags clave:**
- `--approval-mode yolo` → aprueba automáticamente todas las herramientas
- `-p "<prompt>"` → modo headless (non-interactive)

## Config relevante
- `~/.gemini/settings.json` → configuración básica
- `AGENTS.md` en raíz del proyecto → le dice que skip skills en headless
- **NO tiene `write_file` tool** — siempre usa shell commands internamente

## Cuándo usar Gemini

**Ideal para:**
- HTML/CSS/UI — landing pages, dashboards, componentes visuales
- Render/format functions (texto para mostrar al usuario, no lógica)
- Tareas creativas donde el output no depende de código existente
- Tareas simples y bien delimitadas donde no necesita leer mucho contexto previo

**EVITAR para:**
- Adaptar código de un archivo de referencia existente → tiende a crear "su propia versión" en lugar de adaptar
- Lógica de negocio compleja que requiere entender patrones del proyecto
- Archivos que deben integrarse con state/jobs/backend del proyecto sin instrucción muy explícita
- Cualquier tarea donde "leer el archivo de referencia y adaptarlo" sea el núcleo

**Rendimiento esperado:** ⭐⭐⭐⭐⭐ para UI/HTML, ⭐⭐⭐⭐ para render functions simples, ⭐ para adaptación compleja de referencia

## El problema de adaptación de referencia — IMPORTANTE

Gemini interpreta "adaptá esto de la referencia" como "creá tu propia versión inspirada en el concepto". NO lee el archivo de referencia y hace merge cuidadoso.

**Síntoma:** El archivo creado tiene la estructura correcta en líneas generales, pero le faltan exports, funciones, o partes completas que estaban en el archivo de referencia.

**Solución — ser ultra-explícito:**

En lugar de:
```
Create state.mjs adapting from ../qwen-plugin/scripts/lib/state.mjs for OLLAMA
```

Usar:
```
Read ../qwen-plugin/scripts/lib/state.mjs completely.
Create plugins/ollama/scripts/lib/state.mjs that:
COPY exactly (changing only import paths and "qwen" → "ollama"):
  - resolveStateDir(), loadState(), saveState()
  - generateJobId(), upsertJob(), listJobs()
  - writeJobFile(), readJobFile(), resolveJobFile(), resolveJobLogFile()
  - ensureStateDir()
ADD these new functions (not in the reference):
  - resolveGlobalConfigFile() → ~/.ollama-plugin-cc/config.json
  - loadGlobalConfig(), saveGlobalConfig(), updateGlobalConfig()
Do NOT skip any function from the COPY list.
```

## Cómo armar el prompt

Gemini no tiene `write_file` tool disponible en headless. Crea archivos mediante comandos shell. Es su comportamiento natural.

**Incluir siempre:**
```
[TAREA]
Skip all skills. Execute directly.
Use shell commands to create files.
```

**Para UI/HTML (el uso ideal):**
```bash
gemini --approval-mode yolo -p "Create plugins/ollama/scripts/lib/render.mjs with render functions for terminal output. Functions needed: renderSetupReport(report), renderStatusReport(report), renderJobStatusReport(job), renderSwitchReport(result), renderListModelsReport(models), renderRescueReport(diagnosis). Each function receives a plain object and prints formatted text to stdout. Use shell commands to write the file. Skip all skills."
```

**Para adaptación con merge explícito:**
```bash
gemini --approval-mode yolo -p "Read X. Create Y. COPY functions A, B, C exactly. ADD new functions D, E. Do NOT omit any listed function. Skip all skills."
```

## Verificar resultado — OBLIGATORIO para adaptaciones

Gemini puede crear un archivo que parece completo pero le faltan exports o funciones.
Siempre verificar con grep después de adaptar:

```bash
# Verificar que todos los exports están presentes
grep "^export" "<archivo-creado>"
# Comparar con referencia
grep "^export" "<archivo-referencia>"
```

## Ventaja vs otros agentes
- Gemini **no tiene skills directory** → nunca carga brainstorming ni using-superpowers
- Es el más "limpio" para headless sin configuración previa
- Excelente para tareas creativas y de UI/UX
- Bueno para render/format functions donde no necesita integración compleja

## Advertencias
- Sin `write_file` nativo → necesita usar shell commands (comportamiento por defecto)
- Para adaptación de código existente → ser ultra-explícito con "COPY", "ADD", "DO NOT omit"
- Para código que se integra con el resto del proyecto → Codex es más confiable
- Requiere `--approval-mode yolo` obligatoriamente en headless
- Verificar SIEMPRE que todos los exports esperados están en el archivo creado
