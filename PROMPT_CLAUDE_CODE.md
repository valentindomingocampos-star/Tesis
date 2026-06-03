# Prompt para Claude Code en PyCharm

Copiá todo lo que está debajo de la línea y pegalo en Claude Code.

---

Lee el archivo `PLAN_REDISENO_COMPARADO.md` en la raíz de este proyecto. Contiene la auditoría completa del frontend y la especificación detallada de 6 features nuevas para el archivo `herramienta_votaciones/index.html`.

El archivo es un HTML autocontenido de ~11.300 líneas (vanilla JS, sin dependencias). Debe seguir siendo single-file.

Implementá las 5 fases del plan en orden:

**Fase 1 — Infraestructura:**
- Agregar `comparePair` y `compareColorMode` al objeto `state` (~línea 3833).
- Crear el selector de par de escenarios con 4 presets (Dip Aero vs YPF, Sen Aero vs YPF, Aero Dip vs Sen, YPF Dip vs Sen) al inicio de la sub-tab `compare` (~línea 4509).
- Crear clases CSS `.dual-viz` (grid 2 columnas) y `.quad-viz` (grid 2×2) con responsive.
- Crear función helper `getLegsForScenario(id)` que devuelva el array de legisladores del escenario.

**Fase 2 — Visualizaciones comparativas:**
- `renderDualHemicycle(idA, idB)` — 2 hemiciclos SVG lado a lado reutilizando `buildHemicycleLayout()`. Header editorial por hemiciclo. Toggle voto/familia propio. Sin click-to-select (solo hover/tooltip).
- `renderDualMap(idA, idB)` — 2 mapas SVG lado a lado. Las provincias que cambiaron de voto dominante entre A y B deben tener stroke grueso dorado.
- `renderQuadView(viewType)` — 4 hemiciclos o 4 mapas en grid 2×2 (Aero Dip | YPF Dip / Aero Sen | YPF Sen). Mini-SVGs de lectura.

**Fase 3 — Trayectorias (feature estrella):**
- `computeTrajectory(idA, idB)` — match legisladores por nombre normalizado (trim, lowercase, sin acentos) entre 2 escenarios. Retorna: matched (con voteA, voteB, familyA, familyB, sameVote, sameFamily), onlyA, onlyB, metrics (permanencia, consistencia), flows (mapa de transiciones voto→voto).
- Tabla de legisladores repetidos: sortable, searchable, con badges ("Mantuvo voto" verde, "Cambió voto" rojo, "Cambió familia" ocre). Filtro: todos/mantuvieron/cambiaron.
- Diagrama de flujo Sankey en SVG: barras izquierda = votos en escenario A, barras derecha = votos en escenario B, paths bezier curvos con grosor proporcional al n° de legisladores. Labels con n y %.
- Card de métricas resumen: n repetidos, tasa de permanencia, tasa de consistencia, n que cambiaron voto, n que cambiaron familia.
- Tabla de migración por familia: por cada familia con ≥2 repetidos, mostrar %Afi en A, %Afi en B, Δ, cohesión A, cohesión B.

**Fase 4 — Exports PNG:**
- `exportDualHemicyclePng()` — ambos hemiciclos en un canvas 2400px con wrapper académico (reutilizar el patrón de `exportSvgAsPng` que ya existe ~línea 6008).
- `exportDualMapPng()` — ambos mapas con wrapper.
- `exportQuadPng()` — los 4 en canvas 2400×1600 con wrapper.
- `exportTrajectoryFlowPng()` — Sankey con wrapper.
- `exportTrajectoryTablePng()` — tabla nominal con badges.
- Naming convention: `comparativo_{casoA}_vs_{casoB}_{tipo}_{contexto}.png`.

**Fase 5 — Integración:**
- Reorganizar `renderComparisonPanel()` para que las nuevas secciones (B.7–B.10) vayan ANTES de las existentes (B.1–B.6).
- Agregar navegación de saltos rápidos (reutilizar `buildStatsIndex()`).
- Cada sección nueva con botones de export CSV + PNG (patrón de `_sectionHead()`).
- Verificar que el análisis de caso no se rompió.

**Restricciones obligatorias:**
- Mantener single-file HTML autocontenido.
- Respetar el principio "voto y partido nunca compiten en la misma marca" — cada viz muestra voto O familia, nunca ambos.
- Respetar la paleta de diseño existente (CSS custom properties en `:root`).
- Mantener accesibilidad (aria-labels en todas las visualizaciones nuevas).
- Seguir el estilo de código existente (español en comentarios, English en variable names, JSDoc mínimo).

Antes de empezar, verificá si los nombres de legisladores matchean exactamente entre `AEROLINEAS_DIPUTADOS_LEGISLATORS` y `YPF_DIPUTADOS_LEGISLATORS` (y lo mismo para senadores). Si hay discrepancias, implementá normalización o fuzzy match.

Hacé cada fase, probá que renderiza, y pasá a la siguiente.
