# Changelog

Historial de cambios sustantivos sobre el HTML maestro (`index.html`) y sus datasets. Las entradas se ordenan de más reciente a más antigua.

## 2026-06-02 — Etapa 1 · accesibilidad del hemiciclo + contador en vivo

Primera etapa del repaso de accesibilidad. Sin cambios de datos, paleta ni separación voto/partido. Backup previo: `backups/versiones_anteriores/index_pre_etapa1_accesibilidad.html`.

### Hemiciclo navegable por teclado (roving tabindex)
- Cada banca (`<circle class="seat">`) pasa a ser un control individual: `role="button"`, `aria-label` descriptivo (nombre, voto, bloque, familia, provincia — datos textuales, no codificación cromática) y `tabindex` gestionado por roving (una sola banca tabbable a la vez).
- Nuevo `initSeatNavigation(svg)`: flechas (←↑ previa / →↓ siguiente, con wrap-around) recorren las bancas **no atenuadas por filtros**; `Home`/`End` van a los extremos; `Enter`/`Espacio` seleccionan (con `preventDefault` para no scrollear). El foco por teclado muestra el mismo tooltip que el hover del mouse.
- `applyFilters` reconcilia el roving tab-stop: si la banca tabbable queda atenuada por un filtro, el tab-stop pasa a la primera banca visible sin robar el foco.
- Foco visible ya existente (`.seat:focus-visible`) reutilizado.

### Contador de resultados con `aria-live`
- Nuevo nodo `#seatCounter` (`role="status"`, `aria-live="polite"`) entre leyenda y figura. Anuncia "Mostrando X de Y bancas según los filtros activos" o "Y bancas en total · sin filtros activos".
- Nuevo `updateSeatCounter()`, invocado desde `applyFilters`, el cambio de provincia (cubre la vista mapa) y la inicialización de `renderVizCard`.

### Refactor de apoyo
- Predicado de filtrado extraído a `legMatchesFilters(leg, q)`, compartido por `applyFilters` (atenuado de bancas) y `updateSeatCounter` (conteo) para no duplicar la lógica.

### QA
- Smoke tests con jsdom sobre los 4 escenarios: conteos intactos (257 Diputados / 72 Senado Aerolíneas, etc.), roving tabindex (1 tabbable + resto −1), aria-labels en las 257 bancas, contador correcto en carga / filtro de voto / búsqueda sin match / reset / cambio de escenario / vista mapa, y navegación por teclado (flechas, Home/End, Enter, Espacio). Filtros, `aria-pressed` y los 65 botones de exportación sin romperse.

## 2026-05-19 — v1.0 (versión estable para tesis) · estadísticas politológicas avanzadas

Iteración que convierte la sub-tab Estadísticas en una pieza fuerte de análisis legislativo comparado.

### Síntesis interpretativa automática
- Nuevos helpers `buildScenarioInsights(s)` y `buildComparisonInsights()` que generan oraciones descriptivas derivadas de los indicadores ya calculados. Sólo describe magnitudes y compara contra umbrales explícitos; nunca formula causalidad.
- Bloque visual `.insights-block` con eyebrow dorado y lista tipográfica al inicio de cada división (A.0 escenario activo, B.0 comparador).

### Δ cohesión promedio por familia entre escenarios pareados
- Nuevo helper `computeAvgFamilyCohesionDelta(scenarioA, scenarioB)`.
- Integrado a los 4 bloques de B.2 (YPF − Aerolíneas Diputados, YPF − Aerolíneas Senado, Senado − Diputados Aerolíneas, Senado − Diputados YPF).
- Permite leer si el voto, además de cambiar de magnitud, cambia de cohesión interna en las familias políticas (señal de disciplina partidaria comparada).

### Selector de métrica en heatmap familias × escenarios (B.4)
- Toggle entre 3 métricas: `% Afirmativo (presentes)`, `Cohesión Rice` y `% Ausentismo (total)`.
- Estado en `state.famHeatMetric`; re-render parcial vía event delegation.
- Gradiente cromático y aria-label adaptados a la métrica activa.

### Exports granulares nuevos
Seis pares CSV+PNG dedicados:
- `stats-rice-fam-csv/png` — Rice por familia política
- `stats-rice-blk-csv/png` — Rice por bloque político
- `stats-rae-scen-csv/png` — detalle del cálculo de Rae del escenario activo
- `stats-comp-rae-csv/png` — Rae comparado entre los 4 escenarios (ya existía, agrupado acá)
- `stats-fam-comp-wide-csv/png` — comparador wide de familias × escenarios con todas las métricas
- `stats-prov-comp-wide-csv/png` — comparador wide territorial × escenarios

Nuevo layout `simple-table` reutilizable en `exportStatsSectionPng`.

### Sub-tab Exportar reorganizada
Bloque "Estadísticas politológicas" partido en dos cards:
1. **Agregados**: indicadores escenario + comparador 4 escenarios (PNG y CSV completos).
2. **Indicadores específicos**: 12 botones para Rice/Rae/comparadores granulares.

### Tono académico
- Bajadas de cada división re-escritas con vocabulario de Ciencia Política (coaliciones, cohesión partidaria, disciplina de bloque, territorialización, fragmentación legislativa).
- Eyebrows de divisiones extendidas: "A · Escenario activo · análisis legislativo" y "B · Comparador entre escenarios · análisis legislativo comparado".

## 2026-05-19 — v1.0 (versión estable para tesis) · revisión post-auditoría

### Etapas A · B · C · D — roadmap post-auditoría

**Etapa A — fixes técnicos prioritarios**
- A1: cache `scenario._validation` para evitar recálculo en cada render.
- A2: `renderNationalPanel` ahora incluye abstenciones en la barra y el tally; usa `computeVoteSummary` en vez de `s.yes/s.no/s.absent`.
- A3: `setTimeout(60)` reemplazado por helper `afterRender()` con doble `requestAnimationFrame` (espera al próximo paint en vez de tiempo fijo).
- A4: separación visual entre `.legend` y `.viz-summary` (regla inset + microespacio).
- A5: badges de voto en tablas bumped a `font-size: 11.5px` + `padding: 3px 12px`.
- A6: viewBox del mapa **no modificado** — `0 0 500 800` ya está comprimido respecto a la geografía real; rehacerlo implica re-mapear paths/labels (riesgo alto). Decisión documentada para v1.1.

**Etapa B — robustez y accesibilidad**
- B1: heatmaps de comparador con `role="table"/"row"/"columnheader"/"rowheader"/"cell"` + `aria-label` descriptivo por celda ("FPV-PJ · Aerolíneas Diputados · 83.2% afirmativo · 119 legisladores").
- B2: descripciones de ley parametrizadas en `CASE_DESCRIPTIONS` (eliminado el ternario `caseName === 'YPF'` de `renderFooter`).
- B3: parseo de export kinds con regex (`/^stats-.+-csv$/`) en vez de `slice(length, -length)`; soporta kinds con guiones internos.
- B4: print stylesheet `@media print` — oculta toolbar/filtros/botones, mantiene contenido principal con colores correctos, evita cortes con `break-inside: avoid`.

**Etapa C — indicadores Rice y Rae**
- C1: índice de Rice por familia y bloque (`computeFamilyRice`, `computeBlockRice`). Fórmula: `|Afi − Neg| / (Afi + Neg)`. Excluye ausentes, presidencia y abstenciones. Devuelve `"No calculable"` cuando `Afi + Neg = 0`.
- C2: índice de Rae por escenario (`computeScenarioRae`). Fórmula: `1 − Σ(pᵢ²)` sobre afirmativo, negativo y abstención. Excluye ausentes y presidencia.
- C3: nueva sub-sección **A.6 · Cohesión dicotómica (Rice) y fragmentación (Rae)** en División A con KPI del Rae del escenario + top 5 Rice por familia y por bloque + nota metodológica inline. Alineamiento se renumera a A.7. Nueva sub-sección **B.6 · Fragmentación del voto · Rae comparado** en División B con ranking de los 4 escenarios.
- C4: Rice y Rae integrados a `exportStatsCsv` (escenario activo) y `exportComparatorCsv` (4 escenarios). Nuevos specs granulares: `rice-rae` (escenario activo, layout `two-pane-bars-rice`) y `comp-rae` (comparador, layout `rae-bars`).

**Etapa D — documentación**
- README, CHANGELOG, `docs/uso_herramienta.md` y `docs/checklist_v1.md` actualizados.
- Nuevo `docs/checklist_revision_final.md` con la matriz de verificación post-auditoría.



Cierre de cinco etapas de pulido editorial y QA final. La herramienta queda apta para tesis, defensa oral y anexo. Datasets, fuentes y trazabilidad sin cambios desde 2026-05-14.

### Etapa 5 — QA final y versión estable

- **Nombres PNG de tablas en español** vía `TABLE_PNG_FILENAME_SLUG`: `tabla_legisladores`, `tabla_provincias`, `tabla_familias`, `resumen_institucional`.
- **Versión v1.0** marcada en el head de `index.html` (comentario + `<meta name="version" content="1.0">`).
- **README, CHANGELOG, docs/uso_herramienta.md** actualizados.
- **Checklist de entrega v1.0** en `docs/checklist_v1.md`.
- Validación final: JS `node --check` OK · datasets `257/72/257/72` · server local 200.

### Etapa 4 — Tablas premium editoriales + PNG de tablas

- Sistema visual unificado para las 4 tablas: `tbl-card` con encabezado serif (título + bajada + meta-chips + acciones CSV/PNG), tabla con tipografía afinada, zebra suave (0.45 alpha sobre cream), hover bg-soft.
- **Tabla nominal**: legislador protagonista (bold ink), bloque en italic muted con ellipsis + `title`, familia con dot circular, voto como badge; nota de filtros al pie.
- **Tabla por provincia**: nueva columna **Composición** con mini-barras horizontales de 110×7 px segmentadas en proporciones reales (verde/burgundy/ocre/piedra).
- **Tabla por familia**: chip familia + nombre, **sub-línea pequeña con bloques incluidos** (ellipsis + title); ya no es pared de texto.
- **Resumen institucional** → ficha académica (`ficha-card`) con 5 secciones (Identificación · Trámite legislativo · Conteos · Fuentes documentales · Validación), grid K/V con dashed separator, resultado coloreado, estado de validación verde/ocre.
- **PNG académico de tablas** (nuevo, 100% SVG nativo sin dependencias):
  - Helper `rasterizeSvgToPng` extraído y compartido con hemiciclo/mapa.
  - `buildTablePngHeader` / `buildTablePngFooter` reusables.
  - `buildTablePngSvg(scenario, kind, rows)` para 3 tablas + `buildSummaryFichaSvg(scenario)` para la ficha.
  - `TABLE_PNG_SPECS` declarativo + `TABLE_CELL_RENDERERS` (text, textBold, textMutedItalic, number, voteBadge, voteBadgeOrMixed, familyChip, familyChipWithSub, miniBar).
  - **Tabla nominal larga**: `confirm()` si > 100 filas sin filtros; exporta filas filtradas por defecto; nota al pie explica el criterio.
- Sub-tab Exportar reordenada en 3 bloques: Figuras PNG · Tablas como figura PNG · Tablas CSV.

### Etapa 3 (segunda mitad) — CSV académicos para tesis

- 5 helpers de exportación: `csvLabelVote`, `csvPercent`, `csvScenarioContext`, `csvSource`, `csvValidationStatus`.
- 5 CSV reescritos con columnas formales en español, contexto institucional (Caso · Cámara · Fecha · Ley) en cada fila, fuentes para trazabilidad, porcentajes 1-decimal sobre el denominador apropiado.
- Filenames finales: `<escenario>_tabla_legisladores.csv`, `tabla_provincias.csv`, `tabla_familias.csv`, `resumen_institucional.csv`, `comparativo_votaciones_resumen.csv`.
- Sub-tab Exportar bloque "Tablas CSV" con nota de trazabilidad.

### Etapa 3 (primera mitad) — Figuras exportables con contexto académico

- Helpers nuevos: `getExportFigureEyebrow`, `getExportFigureSubtitle`, `getExportFigureReadingNote`, `getExportFigureCredit`, `getExportLegendItems`, `getExportMapNote`.
- **Eyebrow institucional** `FIGURA EXPORTADA · HERRAMIENTA DE VISUALIZACIÓN LEGISLATIVA` en dorado sobre el título.
- **Leyenda cromática embebida** en el PNG: chips con dot + label (Afirmativo (153) / Familia (119) / …), solo categorías presentes en el escenario, en orden canónico, wrap multi-fila automático.
- **Nota Malvinas** específica para mapas: *"Las Islas Malvinas se incorporan como referencia cartográfica nacional…"* como tercer foot-line `NOTA`.
- **Credit line** al pie de cada figura.
- Nombres de archivo: `<escenario>_<figura>_contexto_<voto|familia>.png` (versión académica) / `<escenario>_<figura>_<voto|familia>.png` (simple).

### Etapa 2 — Layout editorial y reducción de densidad

- **Ficha institucional compacta** con `<details>` HTML5 nativo: línea resumen "Ley · Cámara · Fecha · Resultado · tally" + toggle dorado "Ver ficha institucional"; las 3 bandas (header / trámite / fuentes) quedan colapsadas por defecto.
- **Panel central del hemiciclo simplificado**: solo cámara · fecha + RESULTADO grande; las cifras detalladas viven exclusivamente en el `summary-strip`.
- **Summary-strip promovido a resumen ejecutivo** con eyebrow editorial, posicionado entre la leyenda y el hemiciclo.
- Estilos inline de `renderNationalPanel` extraídos a clases CSS reutilizables (`.fam-card`, `.fam-bar`, etc.).
- Columna Bloque en tabla nominal con `.col-block` (ellipsis + title).
- Fila "Provincia" duplicada eliminada del panel de legislador.
- **Tab de escenario activa** con gradiente sutil + border-left dorado 5 px + box-shadow `--shadow-md` + microelevación.

### Etapa 1 — Correcciones críticas de lectura

- Badges del tooltip alineados con `VOTE_COLORS` reales (variantes claras de la paleta editorial).
- Documentación del modo familia actualizada a la regla actual (banca sólida por familia, voto en tooltip/panel/filtros/tablas).
- Sub-tab Exportar reordenada: contexto académico como botones primary, simple como secundario.
- **CSV legisladores con `VOTE_LABELS_CSV`** ("Afirmativo", "Negativo", "Abstención", "Ausente", "Presidencia") en lugar de keys raw.
- Barra inline de export reemplazada por un único botón discreto "Exportar esta figura →" que activa la sub-tab Exportar.

### Refactor previo · modo familia limpio

- Eliminado el ojo-de-buey (anillo familia + núcleo voto): cada banca codifica un único plano. Modo voto → relleno por voto · modo familia → relleno por familia. Regla metodológica explícita: voto y partido nunca compiten en la misma marca.

### Redirección estética editorial premium

- **Paleta**: off-white papel cálido (`#f3f0e8`), cream cards (`#fffdf8`), tinta profunda (`#14181f`), accent gold (`#9c7a2c`).
- **Vote colors editoriales**: verde hunter `#3f6e44` (afirmativo, deja el azul libre para familias), burgundy `#a32a2a`, ocre mostaza `#a87523`, piedra cálida `#a09b8c`.
- **Family colors editoriales**: 10 tonos profundos desaturados.
- **Estados activos** con underline / left-rule en dorado en vez de bloque negro.
- PNG con wrapper académico ya editorial (eyebrow, gold-tick rule, credit line).

## 2026-05-14

### Senado · reconstrucción territorial con doble fuente

- **Aerolíneas Senado:** 23 provincias corregidas a partir de `SEN AMPLIADOS AAAA.xlsx`.
- **YPF Senado:** 19 provincias corregidas a partir de `SEN AMPLIADOS YPF.xlsx`.
- Se preservan **nombres y bloques** del HTML actual (que provienen del acta nominal del HSN).
- **PAMPURO, José Juan Bautista** queda con `vote = PRESIDENCIA` por decisión metodológica explícita (categoría institucional cuando preside un senador del cuerpo). La metadata oficial del acta lo cuenta como afirmativo; la validación interna tolera `AFIRMATIVO + PRESIDENCIA == metadata.yes`.
- Metadata por escenario:
  - `voteSource: 'Acta nominal del Senado de la Nación'`
  - `districtSource: 'SEN AMPLIADOS XLSX (Secretaría Parlamentaria, HSN) — <archivo>.xlsx'`
  - `territorialValidation: true`
- El panel de validación detecta `voteSource + districtSource` y muestra el banner "**Datos validados con doble fuente**".
- Trazabilidad por senador guardada en `trazabilidad/sen_traza_aerolineas_senado.csv` y `trazabilidad/sen_traza_ypf_senado.csv` (16 columnas, 72 filas cada una, incluyendo nivel de confianza ALTA/MEDIA y observaciones).
- Backup previo: `backups/pre_senado/votaciones_consolidado.bak_pre_senado.html`.

### Aerolíneas Diputados · validación contra PDF oficial

- Cruce 257/257 contra `VOTO DIPUTADOS AAAA.pdf` (Exp. 6538-D-08, OD 1342).
- 0 cambios de voto, 0 cambios de provincia.
- Normalización cosmética del bloque `Solidaridad e Igualdad (SI) - ARI (T.D.F` → `(T.D.F.)` en 9 legisladores.
- **FELLNER, Eduardo Alfredo** conservado como `block: 'Presidencia'` por decisión metodológica.
- **KATZ, Daniel:** AFIRMATIVO (versión final del acta tras la modificación del 03/12/2008 19:50, que cambió ABSTENCIÓN → AFIRMATIVO).
- **DE MARCHI, Omar Bruno:** AUSENTE institucional. El acta deja constancia de su voto negativo a viva voz como observación, pero no afecta el conteo.
- Backup previo: `backups/pre_aerolineas_diputados/votaciones_consolidado.bak_pre_aaaadip.html`.

### YPF Diputados · reconstrucción completa desde PDF oficial

El dataset previo era una mezcla de composición 2008 con votos 2012. Se reconstruyó íntegramente desde `VOTO DIPUTADOS YPF.pdf` (Exp. 29-S-12, OD 288).

- **2 reemplazos de persona** (composición 2008 → 2012):
  - Sale BALDATA, Griselda Angela (mandato terminó 10/12/2011) → entra FAUSTINELLI, Hipólito.
  - Sale PÉREZ, Adrián (no era diputado en 5/2012) → entra PEREZ, Alberto José.
- **67 correcciones de provincia** desde el acta oficial.
- **54 cambios de etiqueta de bloque** según versión 2012 del acta (`Peronismo Federal` → `Frente Peronista`, `Propuesta Republicana` → `PRO`, etc.).
- **0 cambios de voto.**
- Mapeo bloque → familia política con criterio metodológico explícito: **identidad partidaria**, sin mezclar con alineamiento legislativo coyuntural. Nuevo Encuentro queda en Centroizquierda; Frente Cívico por Santiago queda en Provinciales/otros.
- Distribución final por familia: FPV-PJ 119 · UCR 38 · Provinciales/otros 27 · Peronismo disidente 27 · Centroizquierda 17 · Socialista 11 · PRO 11 · Coalición Cívica 7 = 257.
- 24/24 jurisdicciones cuadran al cupo legal (L 22.847).
- Backup previo: `backups/pre_ypf_diputados/votaciones_consolidado.bak_pre_ypfdip.html`.

### Validación territorial — refuerzo de la herramienta

- Se incorporaron al `validateScenario` las reglas territoriales:
  - Senado: 24 jurisdicciones × 3 senadores = 72.
  - Diputados: distribución por distrito (L 22.847).
- Normalización de provincias vía `PROVINCE_ALIASES` (Cdad. Aut. Bs. As. → CABA; Rio Negro → Río Negro; Tierra del Fuego, Antártida e Islas del Atlántico Sur → Tierra del Fuego; etc.).
- Tres estados del panel: verde "Datos validados", amarillo "Dataset bajo revisión territorial" (con tabla provincia × esperada × cargada × diff × estado), rojo "Inconsistencias críticas".
- Indicador visual en la pestaña del escenario cuando el dataset está bajo revisión territorial.

### QA visual y robustez

- `--accent: #2563eb` definido en `:root` (cuatro selectores lo usaban sin definición).
- Pill `data-vote="ABSTENCION"` con dot amarillo `#f59e0b`.
- `exportSvgAsPng` inyecta un bloque `<style>` con las reglas críticas (text-anchor, font-size, fill, font-family Helvetica/Arial) dentro del SVG clonado, para que el PNG rasterizado mantenga la apariencia del SVG vivo.
- Media query intermedia `(min-width: 1101px) and (max-width: 1366px)` para laptops estándar (sidebar ancho 320px).
- Limpieza de CSS huérfano `.empty-state` / `.es-*`.
- Stroke en modo familia bajado de 2.2px a 1.4px para no saturar visualmente; ABSTENCION pasa a tener stroke amarillo (antes caía al `else` y se pintaba como presidencia).

## Antes de la consolidación

- Versión inicial unificó dos prototipos: `votaciones_legislativas.html` (UI madura con toggle voto/familia, exportaciones y validación, pero faltaba Aerolíneas Senado) y `diagrama_votaciones_v2_1.html` (4 datasets completos, mapa dinámico con paths embebidos, mejor responsive). El consolidado conserva los 4 datasets y porta encima las features faltantes.
- Versiones intermedias archivadas en `backups/versiones_anteriores/` cuando aparezcan.
