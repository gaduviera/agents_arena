# Analisis de arquitectura - PERSONAL_AGENT

Fecha: 2026-04-25

## Resumen ejecutivo

Este repositorio no es todavia una aplicacion ejecutable tradicional. Es un sistema documental y operativo para coordinar benchmarks entre agentes de IA. La arquitectura actual funciona como un "control plane" basado en archivos Markdown: define tareas, estados, perfiles de agentes, plantillas de ronda y leaderboard.

El diseno es liviano, facil de auditar y adecuado para una etapa inicial. La principal deuda arquitectonica es que las reglas del sistema viven en texto libre y dependen de ejecucion manual por Claude/agentes. Todavia no hay validadores, scripts, contratos de datos ni automatizacion que garanticen consistencia entre estado, rondas y puntajes.

## Componentes principales

### 1. README raiz

Archivo: `README.md`

Responsabilidad:
- Explica el proposito general del framework.
- Documenta la estructura esperada del repo.
- Define el flujo conceptual: brief -> dispatch paralelo -> ejecucion -> evaluacion -> leaderboard.
- Enumera agentes soportados y comandos/plugin setup.

Rol arquitectonico:
- Es la fachada publica del sistema.
- Centraliza onboarding y modelo mental.

Riesgo:
- Duplica parte de la informacion que tambien vive en `ARENA_AGENTES/README.md`, `WORKSPACE/README.md` y `.claude/WORKSPACE_MEMORY.md`.

### 2. ARENA_AGENTES/

Responsabilidad:
- Sistema formal de evaluacion competitiva.
- Guarda estado de la arena, banco de tareas, plantillas de ronda y leaderboard.

Archivos clave:
- `ARENA_AGENTES/README.md`: contrato operativo de una ronda formal.
- `ARENA_AGENTES/STATE.md`: estado actual de la arena.
- `ARENA_AGENTES/tasks/bank.md`: catalogo de tareas reutilizables.
- `ARENA_AGENTES/rounds/TEMPLATE.md`: plantilla para briefs/evaluaciones de rondas.
- `ARENA_AGENTES/scores/leaderboard.md`: acumulado historico de puntajes.

Rol arquitectonico:
- Es el modulo de evaluacion formal.
- Define los inputs estandarizados que reciben los agentes.
- Define el esquema humano de scoring.

Estado actual:
- Arena inicializada.
- No hay rondas completadas.
- Ronda 1 pendiente.

### 3. WORKSPACE/

Responsabilidad:
- Area liviana para pruebas rapidas fuera de rondas formales.
- Cada agente crea su propia carpeta y trabaja de forma aislada.

Archivos clave:
- `WORKSPACE/README.md`: guia del flujo de trabajo informal.
- `WORKSPACE/STATE.md`: tracking de sesion activa y estado por agente.

Rol arquitectonico:
- Es un sandbox colaborativo.
- Permite iteracion rapida sin afectar el historial formal del arena.

Estado actual:
- Workspace vacio y listo para tareas.
- Carpetas de agentes ignoradas por git mediante `.gitignore`.

### 4. AGENT_CONTEXT/

Responsabilidad:
- Memoria on-demand por agente y por sesion.
- Guarda fortalezas, quirks, comandos y observaciones historicas.

Archivos clave:
- `AGENT_CONTEXT/agents/codex.md`
- `AGENT_CONTEXT/agents/gemini.md`
- `AGENT_CONTEXT/agents/qwen.md`
- `AGENT_CONTEXT/agents/ollama.md`
- `AGENT_CONTEXT/sessions/latest.md`

Rol arquitectonico:
- Es la base de conocimiento operacional.
- Reduce re-aprendizaje entre sesiones sin cargar todo siempre.

Fortaleza:
- Buen principio de bajo costo de contexto: los archivos se cargan solo cuando hacen falta.

Riesgo:
- No hay esquema para actualizar perfiles de forma consistente despues de cada ronda.

### 5. .claude/

Responsabilidad:
- Documentacion tecnica interna del flujo de workspace.

Archivo clave:
- `.claude/WORKSPACE_MEMORY.md`

Rol arquitectonico:
- Sirve como referencia interna para el orquestador.
- Duplica parte del contrato de `WORKSPACE/README.md`, pero con mas detalle tecnico.

### 6. SKILLS/

Responsabilidad:
- Contiene skills locales del sistema MAG (Multi-Agent Grid).
- Define orquestacion multiagente, dispatch de agentes y ejecucion de rondas de arena.
- Incluye referencias operativas especificas para Codex, Gemini, Qwen y Ollama.

Archivos clave:
- `SKILLS/mag.md`: indice del sistema MAG.
- `SKILLS/agents.md`: indice de skills por agente/plugin.
- `SKILLS/mag-agent/SKILL.md`: orquestador de alto nivel para delegacion multiagente.
- `SKILLS/mag-dispatch/SKILL.md`: comandos y reglas de ejecucion por agente.
- `SKILLS/mag-arena/SKILL.md`: workflow especifico para rondas del arena.
- `SKILLS/mag-agent/references/*.md`: fichas operativas por agente.

Rol arquitectonico:
- Es una capa de automatizacion/orquestacion encima de `ARENA_AGENTES/`, `WORKSPACE/` y `AGENT_CONTEXT/`.
- Traduce la arquitectura documental del repo en instrucciones ejecutables para Claude/agentes.

Encaje en el repo:
- Tiene sentido que exista dentro del repo si estas skills son parte del producto `PERSONAL_AGENT`.
- No deberia tratarse como una instalacion local descartable si el objetivo es versionar y evolucionar el framework multiagente.

Riesgos:
- Puede duplicar informacion con `AGENT_CONTEXT/agents/*.md`.
- Puede confundirse con una instalacion global de skills si no hay README que explique como se cargan o instalan.
- Actualmente `SKILLS/agents.md` y `SKILLS/mag.md` estan versionados, pero las carpetas `mag-agent/`, `mag-arena/` y `mag-dispatch/` aparecen como no trackeadas. Si son parte del sistema, conviene agregarlas a git.

## Flujo de datos y operacion

### Flujo formal de arena

1. El juez elige tareas desde `ARENA_AGENTES/tasks/bank.md`.
2. Crea una ronda desde `ARENA_AGENTES/rounds/TEMPLATE.md`.
3. Cada agente recibe el mismo brief.
4. Cada agente produce archivos en su carpeta de ronda.
5. Claude evalua cada output en 5 dimensiones.
6. Se actualiza `ARENA_AGENTES/scores/leaderboard.md`.
7. Se actualiza `ARENA_AGENTES/STATE.md`.
8. Se registran aprendizajes en `AGENT_CONTEXT/`.

### Flujo informal de workspace

1. Usuario o juez define una tarea rapida.
2. `WORKSPACE/STATE.md` registra la tarea activa.
3. Cada agente crea `WORKSPACE/{agente}/`.
4. Cada agente guarda `TASK.md`, produce artefactos y escribe `RESULT.md`.
5. Claude lee resultados y actualiza estado.

## Fortalezas arquitectonicas

- Separacion clara entre evaluacion formal (`ARENA_AGENTES/`) y experimentacion rapida (`WORKSPACE/`).
- Memoria de agentes separada del flujo de ejecucion (`AGENT_CONTEXT/`).
- Contratos simples basados en archivos, faciles de inspeccionar en git.
- Buen uso de `.gitignore` para evitar versionar outputs temporales de agentes.
- Plantilla de ronda ya define restricciones globales y formato de evaluacion.
- El banco de tareas tiene niveles de dificultad y categorias balanceadas.

## Riesgos y deuda tecnica

### 1. Estado manual y propenso a drift

`STATE.md`, `leaderboard.md`, perfiles de agentes y resultados de ronda se actualizan manualmente. Eso puede generar inconsistencias, por ejemplo:
- leaderboard no coincide con resultados de ronda;
- estado dice "pendiente" aunque existan outputs;
- perfiles de agentes no reflejan el ultimo benchmark.

### 2. No hay schema formal

Los estados son tablas Markdown y texto libre. Esto es legible, pero dificil de validar automaticamente.

Impacto:
- No se puede detectar facilmente si falta un campo obligatorio.
- Es dificil calcular metricas acumuladas sin parser ad hoc.

### 3. Sin automatizacion de ronda

No existen scripts para:
- crear una ronda;
- copiar tareas del banco al brief;
- preparar carpetas por agente;
- recolectar resultados;
- recalcular leaderboard;
- validar que los archivos esperados existen.

### 4. Duplicacion documental

El flujo de workspace aparece en:
- `README.md`
- `WORKSPACE/README.md`
- `.claude/WORKSPACE_MEMORY.md`

Esto esta bien en fase inicial, pero si cambia una regla puede quedar inconsistente.

### 5. Encoding/legibilidad en terminal

La lectura de archivos en PowerShell mostro caracteres mojibake en emojis y acentos. Puede ser problema de encoding de consola, pero conviene estandarizar a UTF-8 sin BOM y reducir emojis en documentos operativos si se van a procesar por scripts.

### 6. Seguridad y aislamiento operativo

Los comandos de agentes incluyen modos automaticos/yolo/full-auto. El repo separa carpetas, pero no define una politica de permisos por agente ni validaciones antes de ejecutar outputs generados.

## Recomendaciones

### Prioridad alta

1. Agregar schemas ligeros en YAML/JSON junto a los Markdown.
   - Ejemplo: `ARENA_AGENTES/STATE.yml`, `leaderboard.yml`, `round.yml`.
   - Mantener Markdown como vista humana.

2. Crear scripts minimos de arena.
   - `scripts/new_round.py`
   - `scripts/validate_round.py`
   - `scripts/update_leaderboard.py`

3. Definir un contrato de output por agente.
   - `TASK.md`
   - `RESULT.md`
   - archivos esperados segun tipo de tarea
   - estado final: completed, failed, partial

4. Agregar validacion antes de actualizar leaderboard.
   - Verificar que cada tarea esperada existe.
   - Verificar ejecucion de `challenge.py` y `main.py`.
   - Registrar errores de `index.html` como revision estatica/manual.

### Prioridad media

5. Reducir duplicacion documental.
   - Mantener `WORKSPACE/README.md` como contrato fuente.
   - Hacer que `.claude/WORKSPACE_MEMORY.md` apunte a ese contrato y solo agregue notas internas.

6. Agregar `CONTRIBUTING.md`.
   - Como agregar tareas.
   - Como correr una ronda.
   - Como actualizar contexto de agentes.

7. Crear convenciones de nombres.
   - Fechas: `YYYY-MM-DD-round-N`.
   - IDs de tareas: `A1`, `B1`, `C1`.
   - Agentes: lowercase fijo.

### Prioridad baja

8. Generar reportes agregados.
   - Tendencia por agente.
   - Fortaleza por dimension.
   - Dificultad vs score.

9. Incorporar tests del framework cuando existan scripts.
   - Unit tests para calculo de scores.
   - Tests de validacion de estructura de ronda.

## Arquitectura objetivo sugerida

```text
PERSONAL_AGENT/
  README.md
  ARENA_AGENTES/
    STATE.md
    STATE.yml
    tasks/
      bank.md
      bank.yml
    rounds/
      TEMPLATE.md
      2026-04-25-round-1/
        round.yml
        BRIEF.md
        RESULTS.md
        codex/
        gemini/
        qwen/
        ollama/
    scores/
      leaderboard.md
      leaderboard.yml
  WORKSPACE/
    README.md
    STATE.md
    STATE.yml
  AGENT_CONTEXT/
    README.md
    agents/
    sessions/
  SKILLS/
    README.md
    mag.md
    agents.md
    mag-agent/
      SKILL.md
      references/
    mag-dispatch/
      SKILL.md
    mag-arena/
      SKILL.md
  scripts/
    new_round.py
    validate_round.py
    update_leaderboard.py
  tests/
    test_round_validation.py
    test_leaderboard.py
```

## Diagnostico final

La arquitectura actual esta bien planteada para una version 0: simple, inspeccionable y suficientemente modular. La division `ARENA_AGENTES` / `WORKSPACE` / `AGENT_CONTEXT` es correcta y ofrece una base clara para evolucionar.

La carpeta `SKILLS/` tambien encaja, pero deberia definirse explicitamente como "skills del proyecto" y no como instalacion local accidental. Su lugar es razonable porque las skills son especificas de este framework; si fueran skills personales reutilizables entre muchos repos, seria mejor moverlas a una ubicacion global como `~/.codex/skills` y dejar aqui solo documentacion o un script de instalacion.

El siguiente paso arquitectonico no deberia ser agregar complejidad de producto, sino automatizar la confiabilidad del flujo existente: contratos de datos, validadores y scripts pequenos. Eso convertiria el repo de una guia operativa manual en un framework reproducible de evaluacion multiagente.
