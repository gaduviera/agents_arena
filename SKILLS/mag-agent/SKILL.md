---
name: mag-agent
description: >
  Activa el modo multiagente para delegar tareas a Codex, Ollama, Gemini y/o Qwen CLI.
  Usar cuando Gabriel diga: "modo multiagente", "delegá", "delegar", "usa codex",
  "usa ollama", "usa qwen", "usa gemini", "trabajen los tres", "activa delegación",
  "trabaja con los agentes", "que lo haga codex/qwen/gemini/ollama", "multi-agent mode",
  "delegate". Orden de preferencia de agentes: Codex → Ollama (glm-5.1:cloud) → Gemini → Qwen.
  Cuando esta skill está activa, Claude actúa como orquestador y delega ejecución
  real a los agentes CLI en vez de hacerlo él mismo.
---

# Modo Multiagente — Skill de Delegación

Cuando esta skill está activa, sos el **orquestador**. Tu trabajo es decidir quién
hace qué, planificar antes de despachar, y verificar resultados.

---

## Orden de preferencia de agentes

**Orden general:** `Codex → Ollama (glm-5.1:cloud) → Gemini → Qwen`

| # | Agente | Modelo | Rol principal | Cuándo usarlo |
|---|--------|--------|--------------|---------------|
| 1 | **Codex** | gpt-5.4 | Implementación y código | Primera opción para código, features, refactors |
| 2 | **Ollama** | `glm-5.1:cloud` (principal)<br>`qwen3-coder:480b-cloud`<br>`minimax-m2.5:cloud` | Código + razonamiento | Si Codex no está disponible, código complejo open-weight |
| 3 | **Gemini** | gemini-2.5-flash | Diseño y frontend | HTML/CSS, UI, generación creativa, tareas simples |
| 4 | **Qwen** | coder-model | Análisis e investigación | Debugging, exploración, research |

---

## Cuándo usar cada uno (o varios)

**Solo Codex:** implementación, features, tests, refactors.
**Solo Ollama (glm-5.1:cloud):** Codex no disponible, o preferencia open-weight.
**Solo Qwen:** análisis, debugging, investigación de código existente.
**Solo Gemini:** UI/HTML, render functions, tareas creativas bien delimitadas.
**Todos en paralelo:** tareas independientes asignadas por fortaleza de agente.

---

## Sistema de planificación integrado

### Cuándo planificar antes de despachar

| Situación | Acción |
|---|---|
| Tarea simple, 1 agente, resultado claro | Dispatch directo — no crear plan |
| Tarea compleja, 2+ agentes, múltiples archivos | **Crear plan primero** |
| Riesgo de scope creep (schema, specs, configs) | **Crear plan primero** |

### Formato del plan multi-agente

Guardar en: `docs/plans/<fecha>-<nombre>.md` dentro del proyecto.

Estructura mínima por tarea:

```markdown
## Task N — <nombre>

**Agente:** Codex | Gemini | Qwen | Ollama
**Modelo:** <modelo específico>
**Archivos a crear/modificar:**
  - path/al/archivo.ext
**Archivos PROHIBIDOS tocar:**
  - path/al/otro.ext
**Criterio de éxito:** <qué debe existir/pasar cuando termina>
**Dependencias:** Task M debe completar antes (o "ninguna")
```

### Asignación de tareas por fortaleza

| Tipo de tarea | Agente ideal |
|---|---|
| Código complejo, features, tests | Codex |
| Análisis, debugging, research | Qwen |
| UI/HTML, render functions | Gemini |
| Código sin depender de OpenAI | Ollama glm-5.1:cloud |
| Código complejo open-weight | Ollama qwen3-coder:480b-cloud |
| Bulk creation >3 archivos | Codex o Ollama (NUNCA Qwen) |

### Paralelismo — regla antes de cada dispatch

Antes de despachar Task N, verificar: ¿comparte archivos con Task N+1?
- **NO comparte** → despachar ambas en paralelo (un solo mensaje, múltiples Bash calls)
- **Comparte** → secuencial (esperar que N termine antes de N+1)

---

## Templates de dispatch

### Template base (tarea directa sin plan)

```
[TAREA ESPECÍFICA]
Working directory: [path absoluto del proyecto]
Output: Crear [archivo] en [carpeta/]
Restricciones duras:
  - NO modificar archivos fuera de los listados arriba
  - NO cambiar schemas/specs/plans ya commiteados
  - Si encontrás un bug fuera de scope, reportalo sin tocarlo
Skip all skills (brainstorming, using-superpowers). Execute directly.
```

### Template por referencia a plan (cuando existe plan file)

```
Proyecto: [path absoluto al repo]
Tarea: Task N de docs/plans/<nombre>.md
Acción: leé esa sección, ejecutá los pasos EXACTAMENTE como están, commit según el plan.
Restricciones duras:
  - NO modificar archivos fuera del scope de Task N
  - NO cambiar schemas/specs/plans ya commiteados en commits anteriores
  - Si encontrás un bug fuera de scope, reportalo en tu respuesta, NO lo toques
Reporte: hash del commit + lista de archivos tocados
Skip all skills. Execute directly.
```

Usar este template siempre que exista un plan file — reduce ~70% los tokens vs inline.

---

## Cómo ejecutar cada agente

Usar `/mag-dispatch` para comandos exactos, templates de prompt y reglas de paralelismo.
Los archivos de referencia por agente siguen disponibles como respaldo detallado:

- Codex → `references/codex.md`
- Ollama → `references/ollama.md`
- Gemini → `references/gemini.md`
- Qwen → `references/qwen.md`

---

## Flujo de orquestación

```
1. Recibís la tarea de Gabriel
2. Evaluás complejidad:
   - Simple (1 agente, resultado claro) → dispatch directo con template base
   - Compleja (2+ agentes, múltiples archivos) → crear plan en docs/plans/ primero
3. Asignás tareas por fortaleza de agente
4. Verificás paralelismo: ¿Tasks sin dependencias entre sí? → dispatch en paralelo
5. Anunciás el plan: "Voy a usar [agente] para [parte]"
   (Si Gabriel dijo "hacelo", podés saltar esta confirmación)
6. Ejecutás los agentes con Bash tool
7. Verificás resultado post-dispatch (OBLIGATORIO):
   git log --oneline -1    ← ¿commiteó?
   git status              ← ¿hay cambios sin committear?
   ls <carpetas esperadas> ← ¿creó los archivos?
8. Si alguna check falla → completar manualmente antes de avanzar
9. Reportás resultado a Gabriel
```

### Lectura del return de Agent (A6)

`Agent` tool **sin** `run_in_background: true` es **síncrono** — cuando devuelve, ya terminó.

```
# ❌ Error
Agent(codex-rescue, ...) devuelve resultado
→ "Codex corriendo, esperando notificación..." ← YA TERMINÓ, no hay notificación

# ✅ Correcto
Agent(codex-rescue, ...) devuelve resultado
→ Leer el resultado → procesar → continuar
```

`<task-notification>` **solo** existe para `Bash(run_in_background: true)`, nunca para `Agent`.

---

## Activación y desactivación

**Activar:** "modo multiagente", "delegá X a codex", "que lo haga qwen", etc.
**Desactivar:** "modo normal", "hacelo vos", "sin delegar", "solo vos"

---

## Qué NO hacer

- No ejecutes los agentes sin avisar qué vas a hacer
- No delegues todo ciegamente — si la tarea es trivial, hacela vos directamente
- No uses `write_file` en prompts a Qwen/Gemini — usan shell commands
- No asumas que los agentes crearon archivos sin verificar con `ls` o `Read`
- No pegues código inline en el prompt si existe un plan file — usá el template por referencia
- No dispatches más de 3 archivos a Qwen en una sola sesión — usa Codex para bulk creation
