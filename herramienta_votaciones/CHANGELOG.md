# Changelog

Historial de cambios sustantivos sobre el HTML maestro (`index.html`) y sus datasets. Las entradas se ordenan de más reciente a más antigua.

## 2026-06-02 — Etapa 7 · impresión y exportación

Séptima etapa del rediseño. Endurece la impresión (Cmd+P / PDF) para que conserve todo el contexto analítico y no corte contenido. Solo presentación e impresión: sin cambios de datos.

### Contexto metodológico completo en impresión
- **Meta-bar:** era un `<details>` colapsado, así que en impresión salía solo el resumen (ley·cámara·fecha·resultado). Nuevo par de listeners `beforeprint`/`afterprint` que abren la ficha institucional (sesión, expediente, orden del día, bancas/quórum, presentes y **fuentes** de voto/territorial) antes de imprimir y restauran el estado después. CSS de respaldo fuerza las filas visibles; el toggle "Ver ficha institucional" se oculta en print.
- Las notas de **validación** (`<details>`) también se abren en impresión (refuerza el fix de la Etapa 3).
- El **detalle activo** (legislador / provincia seleccionada en el sidebar) ya se imprimía; se conserva.

### No cortar contenido
- Contenedores con scroll (`.dtable-wrap` 560/520px y `.member-list` 340px) se expanden en impresión (`max-height: none; overflow: visible`): la tabla nominal de 257 filas, las tablas resumen y la lista de miembros de la provincia/bloque seleccionado ahora salen completas (antes se clipeaban). El PDF de prueba pasó de ~5,5 MB a ~7,9 MB por el contenido tabular completo.

### Estructura y cortes de página
- `@page { margin: 1.5cm }` para márgenes consistentes.
- `#statsPanel` y `#tablesPanel` arrancan en página nueva (`break-before: page`): documento ordenado [masthead + ficha + figura + resumen + detalle] / [estadísticas] / [tablas].
- Filas de tabla con `break-inside: avoid`; encabezados sticky pasan a `position: static` en impresión (evita solapamientos).

### QA
- Sintaxis JS válida; datos idénticos. `beforeprint`/`afterprint` verificado en DOM (meta-bar open: false→true→false). Layout de impresión verificado por captura (emulando print en pantalla): ficha institucional + fuentes expandidas, validación expandida, chrome oculto, figura/leyenda/resumen presentes. Paginación y expansión de tablas verificadas por CSS + crecimiento del PDF; no rasterizadas por página (sin poppler en el entorno).

## 2026-06-02 — Etapa 6 · estética premium y diseño editorial

Sexta etapa del rediseño. Auditoría de la estética (tipografía, paleta/contraste, cards/sombras, labels, estados) con dos correcciones puntuales; el resto del sistema ya estaba refinado y se documenta como verificado. Solo presentación.

### 6.3 · Jerarquía de cards y sombras
- `.ficha-card` (ficha institucional, contenido secundario) bajaba con `--shadow-md`, la misma sombra fuerte que los bloques **primarios** (`.viz-card` y `.stats-division`). Pasa a `--shadow-xs`. Ahora la sombra prominente queda reservada para la visualización y el bloque estadístico central; ficha, tablas (`.tbl-card`), exports (`.exp-card`) y notas comparten un tratamiento plano/sutil. El sidebar (`.panel`) mantiene `--shadow-sm` como acompañante.

### 6.2 · Contraste (auditoría + 1 fix)
- **Fix:** el badge de **Abstención** (`.vote-badge.abst`) usaba el ocre `--vote-abst` (#a87523) sobre fondo soft → ~3.95:1 (bajo AA). Su texto pasa a `--accent-gold-text` (#7c6020) → ~5.5:1. **No** cambia el color de voto en hemiciclo/mapa, solo el texto del badge.
- Verificado y aceptable: dorado tipográfico (#7c6020) ~5:1; `--muted` ~5:1; badges yes/no ~4.7–4.9:1; badge ausente ya usaba `--ink-2` (texto oscuro, alto contraste). `--muted-soft` (~2.7:1) queda solo en texto decorativo (separadores "·", placeholders, marcador "—"), donde es aceptable.

### 6.1 · Tipografía (evaluado, sin cambios)
- El stack serif (`Iowan Old Style → Palatino Linotype → Palatino → Book Antiqua → Georgia`) es todo humanista-Palatino: Mac cae en Iowan, Windows en Palatino Linotype (muy similares), y Georgia es el fallback universal. La variación entre plataformas es modesta y el stack es robusto. Embeber una webfont agregaría 100KB+ y rompería el "sin dependencias / una sola página liviana". **Decisión: mantener el stack de sistema.**

### 6.4 / 6.5 · Labels y estados (verificado, ya resuelto)
- El sistema ya es de dos niveles: eyebrows de sección en mayúscula con tracking vs. labels de dato sin mayúscula (p. ej. `.sc-label`, comentado explícitamente en el código). El tracking (.18–.22em) es un device editorial consistente, no inflación azarosa.
- Estados ya unificados: `:focus-visible` con anillo azul UI común; hover de bancas intencionalmente sin cambio de `stroke-width` (evita salto geométrico, comentado); `prefers-reduced-motion` desactiva transiciones.

## 2026-06-02 — Etapa 5 · estadísticas, tablas y heatmaps

Quinta etapa del rediseño. Jerarquiza secciones sin datos, explica la intensidad de los heatmaps y mejora la lectura de las tablas. Solo presentación: sin cambios de datos ni de cálculo.

### 5.1 · Sección sin datos con peso reducido
- **A.7 Alineamiento** (que solo informa "no se calcula con el dataset actual") deja de renderizarse como sección analítica completa (título serif + marcador + bajada, igual que A.1–A.6). Pasa a una **nota secundaria** `.stats-section-aside`: una etiqueta chica muted ("A.7 · Alineamiento… · no se calcula con el dataset actual") + la nota explicativa `.align-note` que ya existía. La información metodológica se conserva íntegra; solo baja su peso visual.

### 5.2 · Mini-leyenda de intensidad en heatmaps
- Los dos heatmaps comparados (familias×escenarios y provincias×escenarios) no explicaban qué significa la saturación del color. Se agregó una `.heat-scale` debajo de cada uno:
  - Familias: barra de gradiente del color base de la métrica activa, con "menor → mayor" y "Intensidad ∝ [métrica]".
  - Territorial: muestras de color (afirmativo / negativo / mixta) + "Color = voto predominante · intensidad ∝ magnitud".
- Las muestras se agregaron al `print-color-adjust: exact` (imprimen con color).
- Contraste verificado: el alpha del fondo de celda está capeado en 0.55, así que el texto `--ink` sobre la celda más intensa mantiene ~5:1 (AA). No se requirió texto adaptativo.

### 5.3 · Tablas
- `.col-pct` (% Afirmativo, dato analítico clave que estaba en 11px muted) sube a `--t-sm`, color `--ink-2` y peso semibold: legible sin competir con el resto.
- Verificado que las columnas numéricas ya alinean th y td a la derecha con `tabular-nums` (`.col-num`/`.col-pct`), header sticky y notas al pie. Las tablas ya estaban sólidas; sin cambios estructurales.

### QA
- Sintaxis JS válida; datos idénticos al commit previo. Render headless de la sub-tab Estadísticas: A.7 demotada, ambos heatmaps con su mini-leyenda, KPIs y rankings intactos. Sticky-first-column de tablas: evaluado y **no** aplicado (las tablas entran en desktop/notebook; se deja como opción futura).

## 2026-06-02 — Etapa 4 · modo de color y riesgo interpretativo

Refuerza la separación voto/partido en modo familia política (la auditoría la marcó como trampa interpretativa). Solo presentación: sin cambios de datos ni de paleta.

### Aviso prominente del modo de color
- Nuevo `.color-notice`: cinta dorada con ícono que aparece **solo en modo familia**, arriba de la leyenda: "⚠ Modo familia política. El color identifica la familia o bloque político, **no el sentido del voto**. El voto de cada legislador se lee en el tooltip, el panel lateral y la tabla nominal." Inyectada condicionalmente en el template de `renderVizCard` (que se re-renderiza al cambiar de modo).
- Se quitó la nota chica redundante (`legend-note-family`) de `renderLegend`: la aclaración vive ahora en el aviso prominente; la leyenda muestra solo los chips de familia.
- El control de modo ya quedó separado de los filtros globales y pegado a la figura en la Etapa 3 (`.fig-controls`). El pie de figura y los exports PNG con contexto ya traían la nota de lectura por modo (`getFigureReadingNote`/`getExportFigureReadingNote`), que se conserva.
- El aviso se mantiene visible en impresión (contexto metodológico necesario al exportar la figura en modo familia).

### Pendiente señalado (no ejecutado sin confirmación)
- Proximidad de tono entre dos colores de familia y dos de voto: **PRO `#b08227` (oro) ≈ Abstención `#a87523` (ocre)** y, en menor medida, **Socialista `#9f1239` (carmín) ≈ Negativo `#a32a2a` (bordó)**. Como cambiar la paleta de familias afecta exports y el orden de espectro establecido, se deja a decisión del autor; el aviso prominente mitiga la confusión como primera línea.

## 2026-06-02 — Fix · deselección de banca (toggle)

`selectLegislator(idx)` ahora alterna: volver a activar la banca ya seleccionada la **deselecciona** y regresa a la vista completa (limpia `selectedIndex`, el filtro de provincia que la selección había fijado, y oculta el halo). Antes re-seleccionaba sin salir nunca. Aplica por igual al clic en banca, Enter/Espacio por teclado y clic en fila de la tabla nominal (los tres pasan por la misma función). Nuevo helper `deselectLegislator()`. Verificado en DOM headless (select → re-clic deselecciona → clic vuelve a seleccionar). Sin cambios de datos.

## 2026-06-02 — Etapa 3 · la visualización como protagonista

Tercera etapa del rediseño. Sube el hemiciclo, comprime el chrome superior y ordena la lectura de la figura. Solo presentación, markup y reorganización de controles: **sin cambios de datos, conteos, paleta ni separación voto/partido** (verificado por diff de contenido).

### El gráfico sube (3.1)
- El panel de validación en estado **OK** pasa de tarjeta verde prominente a **nota colapsable secundaria** (`<details class="validation is-ok">`): por defecto muestra solo "✓ Datos validados" + "ver detalle"; el detalle metodológico completo se conserva al expandir. Los estados de **advertencia/crítico siguen como banners prominentes** (sin cambios). En impresión el detalle se fuerza visible (no se pierde contexto).
- La antigua primera fila de la toolbar (vista/color) se quita de arriba: el chrome previo a la figura baja de 2 filas + tarjeta a 1 fila + nota colapsada.

### Toolbar comprimida y controles junto a la figura (3.2, 3.3)
- La toolbar global queda con **una sola fila de filtros** (buscador, provincia, pills de voto, limpiar). Estos filtros se mantienen arriba porque afectan también a la tabla nominal (`legMatchesFilters`).
- Los controles **vista** (Hemiciclo/Mapa) y **modo de color** (voto/familia) —exclusivos de la figura— se mueven a `.fig-controls`, pegados a la leyenda que explican. El binding no cambia (selectores globales `[data-view]`/`[data-color]`).

### Microcopy de lectura (3.4)
- Nueva línea `.viz-readhint` junto a la figura, view-aware (seteada en `renderFigureBlocks`): hemiciclo → "Cada banca es un legislador · clic o Enter para ver su ficha · el color responde al modo seleccionado"; mapa → variante por jurisdicción. Agrupada con el contador en vivo en una `.viz-figmeta` (lectura izq · conteo der), lo que de paso alinea el contador que antes quedaba a sangre.

### Resumen ejecutivo jerarquizado (3.5)
- El summary-strip deja de tener todos los KPIs al mismo peso: **A favor / En contra** son tier primario (número mayor); **abstención, ausentes y margen** pasan a tier secundario (número menor, fondo apenas marcado). No se quita ningún dato; solo se jerarquiza.

### Impresión
- `.fig-controls` y `.viz-figmeta` agregadas a la lista de ocultos en `@media print` (controles y microcopy de pantalla, no de figura impresa).

### QA
- Datos, conteos y resultados idénticos al commit previo (diff vacío). Sintaxis JS válida. Binding de vista/color/filtros intacto (selectores globales). Render verificado con capturas headless a 1440px, 1280px y 1100px; PDF de impresión generado sin error. Pendiente de revisión interactiva: modo familia y vista mapa (wiring verificado, no re-testeados visualmente esta vez).

## 2026-06-02 — Etapa 2 · navegación por dos ejes + ancla de contexto

Segunda etapa del rediseño (jerarquía, navegación y orientación). Solo presentación, markup y lógica de navegación: **sin cambios de datos, conteos, paleta ni separación voto/partido** (verificado por diff de contenido contra el commit previo). Desarrollada sobre la copia `index-redesign-v1.html` y luego volcada al `index.html` maestro; el original previo queda preservado en git (commit `d9d3c7a`).

### Navegación por dos ejes (Caso × Cámara)
- Las 4 tarjetas-tab densas y repetitivas (`.scenario-tab`, con caso/cámara/fecha/bancas/ley en cada una) se reemplazan por dos controles segmentados: `.scenario-nav` con un eje **Caso** (Aerolíneas / YPF) y un eje **Cámara** (Diputados / Senado). El escenario activo se lee en el segmento relleno en tinta de cada eje (`.nav-seg[aria-pressed="true"]`).
- La combinación de ambos ejes reconstituye el `scenarioId` (`${caso}_${camara}`), que sigue siendo la única fuente de verdad. La lógica de selección de escenario y el reseteo de estado al cambiar son idénticos a los de las tabs previas.
- `renderTabs()` reescrito: descompone `state.scenarioId` y marca el segmento pulsado de cada eje (en vez de marcar una tarjeta completa).
- El indicador de "dataset bajo revisión territorial" (antes marcado por-tarjeta) se preserva como etiqueta discreta `.nav-review` junto al selector, alimentada por la misma `validateScenario()`.

### Ancla de contexto persistente
- Nuevo `.context-anchor` (`position: fixed`): barra fina que aparece solo al scrollear, cuando el selector sale del viewport (vía `IntersectionObserver` sobre `.scenario-nav`). Muestra caso · cámara · fecha · resultado, con el resultado coloreado igual que la meta-bar y manteniendo la etiqueta analítica (AFIRMATIVO/NEGATIVO), sin vocabulario nuevo.
- Nuevo `updateContextAnchor()`, invocado desde `render()`. `IntersectionObserver` inicializado una vez; si no está disponible, el ancla no aparece (degradación limpia, sin error).
- Oculta en `@media print` para no duplicar el masthead.

### Reducción de repetición de metadata
- Al sacar fecha/ley/bancas de cada tarjeta, esa metadata deja de repetirse en la navegación: queda en la meta-bar (detalle completo) y en el ancla (orientación al scrollear). Jerarquía: caso → cámara → fecha → resultado → ley/metadatos.

### Ajustes CSS dependientes
- `@media print`: `.scenario-tabs` → `.scenario-nav`, y `.context-anchor` agregada a la lista de ocultos.
- Responsive ≤720px, `:focus-visible` (`.nav-seg`) y `prefers-reduced-motion` (el ancla aparece sin deslizamiento) actualizados a las clases nuevas.
- Eliminada la regla muerta `.scenario-tab.is-under-review .st-status` (sin elementos tras el reemplazo).

### QA
- Datasets, conteos y resultados idénticos al commit previo (diff de contenido vacío). Sintaxis JS válida (`node` parse). Las 4 combinaciones Caso×Cámara mapean a IDs válidos y marcan exactamente 2 segmentos. Render verificado con capturas headless a 1440px y 1024px; PDF de impresión generado sin error. jsdom no disponible en el entorno, smoke tests de render no ejecutados esta vez.

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
