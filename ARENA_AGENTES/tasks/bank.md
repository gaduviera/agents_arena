# Banco de Tareas — Arena de Agentes

Cada ronda usa una tarea de cada categoría. El juez (Claude) elige la combinación.

---

## Categoría A — Code Challenge (`challenge.py`)

### A1 · BIM Namer ★☆☆
Clase `BIMElement` con métodos:
- `__init__(project, discipline, sequence, element_type)`
- `to_iso19650() -> str` — genera nombre completo según norma
- `validate() -> bool` — verifica campos válidos (alfanumérico, longitud max)
- `from_string(code: str) -> BIMElement` (classmethod) — parsea desde string

**Criterio mínimo:** instanciar, convertir, parsear y validar sin errores

---

### A2 · Matrix Spiral ★★☆
Función `spiral_order(matrix: list[list[int]]) -> list[int]` que recorre una matriz N×N
en espiral (afuera→adentro, sentido horario). Sin librerías externas.
Incluir al menos 3 assert con matrices de distinto tamaño.

---

### A3 · Token Budget ★★★
Clase `TokenBudget`:
- `add(model, tokens_in, tokens_out)` — registra llamada
- `summary() -> dict` — total_in, total_out, total_cost_usd
- `cheapest_model(target_tokens) -> str` — modelo más barato para ese volumen
Incluir precios de referencia para claude-sonnet-4-6, gpt-5.4, gemini-2.5-flash.

---

## Categoría B — HTML Challenge (`index.html`)

### B1 · BIM Dashboard ★☆☆
Tabla de 6 elementos BIM: Código, Disciplina, Tipo, Estado.
- Filtro por disciplina (dropdown JS)
- Color de fila según estado: En diseño (azul), En construcción (naranja), Completado (verde)
- Sin frameworks, CSS embebido

---

### B2 · Project Timeline ★★☆
Gantt chart horizontal en HTML/CSS puro (sin canvas).
- 5 fases con fechas de inicio/fin
- Barra de progreso por fase (% hardcodeado)
- Leyenda de colores por disciplina

---

### B3 · Agent Scoreboard ★★★
Scoreboard interactivo:
- Tabla de agentes con puntajes por ronda (datos en JS)
- Ordenar por columna al hacer click en header
- Sparkline SVG inline mostrando evolución por agente
- Dark mode toggle

---

## Categoría C — Python Chart (`main.py`)

### C1 · Bézier Cúbica ★☆☆
Graficar curva de Bézier cúbica con matplotlib.
- 4 puntos de control hardcodeados (o argparse)
- Mostrar curva + puntos de control + polígono de control
- Título, leyenda, ejes etiquetados
- Ejecutable con `python main.py`

---

### C2 · Fibonacci Spiral ★★☆
Espiral de Fibonacci con rectángulos áureos:
- N primeros rectángulos (default 8, configurable)
- Curva espiral aproximada con arcos de cuarto de círculo
- Anotar ratio áureo (φ ≈ 1.618) en el gráfico

---

### C3 · BIM Progress Radar ★★★
Gráfico radar (spider chart) con progreso de 6 disciplinas BIM:
- Datos: ARQ, EST, MEP, CIV, INT, EXT (valores 0–100)
- Comparar 2 fases: diseño vs construcción
- Colores diferenciados, leyenda, título del proyecto

---

## Combinaciones sugeridas

| Ronda | Code | HTML | Chart | Dificultad total |
|-------|------|------|-------|-----------------|
| Ronda 1 | A1 | B1 | C1 | Baja — calibración inicial |
| Ronda 2 | A2 | B2 | C2 | Media |
| Ronda 3 | A3 | B3 | C3 | Alta |
| Mix 1 | A1 | B3 | C2 | Media-alta |
