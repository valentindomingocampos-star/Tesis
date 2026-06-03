# Changelog

Historial de cambios sustantivos sobre el HTML maestro (`index.html`) y sus datasets. Las entradas se ordenan de más reciente a más antigua.

## 2026-06-03 — Fase 4 · Exports PNG del Análisis comparado

Exportación PNG de las visualizaciones nuevas del comparado, como **láminas académicas** (mismo wrapper que los exports existentes: eyebrow, filete tinta+oro, título, subtítulo, etiquetas, leyenda, fuente y crédito). Solo exportación; sin tocar datos, cálculos, compute*, B.1–B.10 funcionalmente ni los exports previos.

### 4a — Visualizaciones comparadas (B.4 / B.5 / B.10)
- **B.4 Hemiciclos lado a lado**, **B.5 Mapas lado a lado** y **B.10 Grilla 2×2** exportan a PNG con el par y el modo de color activos (voto/familia). Botón «↓ PNG lámina» en cada sección.
- Reutiliza el wrapper académico existente (`exportSvgAsPng`) componiendo un SVG con las celdas (etiqueta caso·cámara·fecha·resultado + sub-SVG). Helpers nuevos: `getCompareLegendItems`, `buildCompareCompositeSvg`, `exportCompareVizPng`. Helpers existentes reutilizados: `exportSvgAsPng`, `rasterizeSvgToPng`, `slugifyFilename`, `getExportFigureCredit`, los builders `buildCompareHemicycleSvg`/`buildCompareMapSvg`.
- **Mapas:** el resaltado dorado de provincias que cambiaron de voto dominante **sobrevive al export** (regla `.province-path.is-changed` añadida a `SVG_EXPORT_STYLES`), con nota de LECTURA. **Fuente general comparada** (`PNG_COMPARISON_SOURCE`), no «Caso visualizado…».
- Hooks mínimos a la infraestructura (aditivos, sin romper nada): `context.legendEyebrow` opcional en `exportSvgAsPng`; parámetro opcional `customActions` en `_sectionHead`.
- Convención de nombres: `comparado_{hemiciclos|mapas}_{idA}_vs_{idB}_{voto|familia}.png` · `comparado_grilla_2x2_{hemiciclos|mapas}_{voto|familia}.png`.
- Verificado renderizando las láminas reales (hemiciclos, mapas con highlight, grilla 2×2) a alta resolución: wrapper correcto, sin clipping, texto legible; nombres correctos; modo familia OK; exports previos intactos; +3 botones (54→57); consola sin errores.

## 2026-06-03 — Pulido post-Fase A · A.1 (lectura visual) + A.3 (metodología)

Ajustes de terminación tras la rearquitectura, para que la herramienta pase de "funciona muy bien" a "pieza académica terminada". Solo presentación/markup/microcopy; sin tocar datos, cálculos, compute*, normalización, métricas, trayectoria validada ni exports.

### A.1.1 — Metadatos canónicos por modo
- **Datos:** se oculta la meta-bar (la ficha "Resumen institucional de la votación" ya es la fuente canónica del contexto institucional en ese modo) → fin de la duplicación meta-bar ↔ ficha.
- **Caso:** se quita la grilla de metadatos de la figura **en pantalla** (`showMetaCompact:false` en `renderFigureBlocks`); el contexto institucional queda canónico en la meta-bar (resumen + ficha expandible). **El export no se afecta**: `buildFigureContext` arma su contexto vía `getFigureMetaCompact` (intacta, 6 campos) → el PNG conserva los metadatos.
- **Footer "Caso visualizado…":** scopeado a Caso y Datos (era residuo del caso en Comparado/Metodología).
- Scoping en `@media screen` (print intacto). Verificado por `getComputedStyle`: meta-bar solo en Caso; footer en Caso+Datos; grilla de figura removida en pantalla; export con 6 metadatos.

### A.1.2 — Análisis comparado en tres movimientos
- El comparado se lee como un argumento en **tres actos**, no como lista plana de bloques:
  - **I · Síntesis agregada** (B.1 tabla agregada, B.2 diferencias, B.3 rankings) — ancla general de los cuatro escenarios.
  - **II · Exploración por par de escenarios** (selector par+color, B.4 hemiciclos, B.5 mapas, B.6 trayectoria) — depende del par.
  - **III · Profundización estructural** (B.7 heatmap familias, B.8 heatmap provincias, B.9 Rae, B.10 grilla plegada).
- Cada movimiento abre con cabecera editorial: numeral romano dorado (serif) + título serif + bajada, sobre filete de tinta (`--rule-ink`). Da respiración y estructura al scroll.
- **Índice de recorrido** al inicio del cuerpo (reusa el lenguaje de `.stats-index`) con saltos a los tres movimientos (`#cmp-mov-1/2/3`, anchors verificados). El selector comparativo quedó integrado como apertura del Movimiento II. Solo markup/CSS; markers B.1–B.10 y export-keys intactos; visualizaciones siguen montadas (2+2+4).

### A.3 — Metodología como aparato real
- La pestaña Metodología deja de ser una nota de 5 bullets y pasa a ser un **apéndice académico navegable** de **diez secciones** numeradas, con índice de contenido (anchors `#met-1…#met-10`), subtítulos serif y texto respirado (sin bullets eternos ni estética de FAQ):
  1. Fuentes y universo empírico · 2. Casos y cámaras · 3. Clasificación de votos · 4. Tratamiento de presidencia y no voto · 5. Bloques y familias políticas (con el espectro de las 10 familias en orden) · 6. Provincias y agregación territorial · 7. Cohesión / Rice · 8. Fragmentación / Rae · 9. Trayectoria de legisladores · 10. Criterios de lectura y exportación.
- **Solo contenido/markup**, escrito sobre lo ya implementado y validado: Rice = |Afi−Neg|/(Afi+Neg); Rae = 1−Σpᵢ²; umbrales de cohesión leídos de las constantes existentes; normalización de presidencia (incl. el caso Pampuro en Aerolíneas·Senado); match de trayectoria exacto normalizado same-chamber; familias analíticas y revisables; advertencias de lectura (Dip/Senado no equivalentes, sin alineamiento oficialismo/oposición, color voto≠familia). **Sin tocar cálculos, datasets, compute*, votos ni exports.**
- Verificado: 10 secciones, índice con anchors resueltos, espectro de 10 familias, scoping de Metodología intacto (sin selector/meta/sidebar/footer), tabs y Comparado intactos, consola sin errores, print 53 páginas (estable).

### A.1.3 — Ancho de lectura para bloques tipográficos
- En el comparado a ancho completo, los bloques simples (tabla agregada, diferencias, rankings) se estiraban con columnas muy separadas. Cap de lectura a **1080px** (scopeado a `#comparePanel`, left-aligned). Las visualizaciones SVG (dual-viz), la trayectoria (`.traj-table`/matriz) y los heatmaps **conservan el ancho completo** (1280px) donde aporta. Verificado por anchos reales: comp-table/diff-grid/ranking = 1080; heatmap/traj/dual-viz = 1280; ranking del Análisis del caso intacto (900). Solo CSS.

## 2026-06-03 — Rearquitectura de navegación · Fase A (scoping de controles por modo)

Reorganización de la experiencia para que la arquitectura refleje la lógica de la tesis: cada modo de análisis muestra solo los controles que gobierna. La herramienta deja de sentirse "primero elegí un caso, después entrá a cualquier tab" y pasa a "elegí qué tipo de análisis querés hacer". Camino A de bajo riesgo: **no se movió el selector de caso/cámara del DOM ni se tocó su handler directo**; se muestra/oculta según la tab activa. Sin tocar datos, cálculos, compute*, votos, familias, métricas, trayectoria, B.1–B.10 funcionalmente ni exports.

### Mecanismo
- `activateSubtab()` fija una clase `mode-case|compare|data|method` en `<body>` (punto único; `renderVizCard` ya lo llama al final). Reglas de scoping **solo `@media screen`** → el print no se afecta. `display:none` deja los ocultos fuera del orden de tabulación (foco/teclado resueltos).

### Reglas de visibilidad
- **Selector de caso/cámara, meta-bar y ancla de contexto flotante:** visibles en **Caso y Datos**; ocultos en Comparado y Metodología.
- **Sidebar:** visible **solo en Caso**. En Comparado/Datos/Metodología se oculta y el `workspace` pasa a **una columna (ancho completo)** — las tablas, heatmaps y trayectoria del comparado y los datos respiran más.
- **Selector comparativo (par + color):** movido de antes de B.1 a **justo antes de B.4** (lo que controla), con encabezado «Exploración por par de escenarios» + bajada que aclara que B.1–B.3 son el ancla agregada de los cuatro escenarios y no dependen del par.

### Microcopy
- Etiqueta «Votación en análisis» sobre el selector de caso/cámara. Bajada introductoria por modo en Caso y Datos (Datos nombra el caso activo: «Base nominal de la votación seleccionada — {caso · cámara}»). Cabecera de Metodología actualizada a «Metodología y criterios de clasificación» (la bajada anterior decía «sub-tab de Estadísticas», desactualizado).

### Datos / Metodología
- **Datos** se mantiene **case-scoped** (usa `activeScenario()` en su núcleo) — no se refactorizó a tabla general (alto riesgo). Se aclaró con la bajada + el selector visible. **Metodología** queda independiente (ya no usaba el escenario).

### QA
- Sintaxis OK; datos idénticos a HEAD; **consola sin errores**. Scoping verificado por `getComputedStyle` en los 4 modos (selector/meta/ancla/sidebar/columnas del workspace coinciden con la spec). Orden del comparado `B.1 B.2 B.3 → selector → B.4 … B.10`. Handler `.nav-seg` intacto (cambio de escenario OK). Render limpio a 1440 y 1280. **Print: 51 páginas (idéntico — scoping screen-only).** B.10 sigue plegada.

## 2026-06-03 — Análisis comparado: consolidación (orden, grilla plegable, print)

Consolidación del Análisis comparado tras la auditoría integral, antes de pasar a exports. Solo orden/presentación/print; sin tocar datos, cálculos, compute*, datasets, votos, familias, exportaciones ni la lógica de comparación.

### Reordenamiento + renumeración secuencial
- Las visualizaciones nuevas se renderizaban con marcadores fuera de secuencia (B.7→B.10 antes de B.1→B.6). Nuevo orden narrativo **secuencial B.1→B.10** (números → exploración → trayectoria → indicadores finos → panorámica):
  `B.1 Tabla agregada · B.2 Diferencias · B.3 Rankings · B.4 Hemiciclos lado a lado · B.5 Mapas lado a lado · B.6 Trayectoria · B.7 Heatmap familias · B.8 Heatmap provincias · B.9 Rae · B.10 Grilla panorámica 2×2`.
- El selector de par + color queda arriba de todo (tras la síntesis). Solo cambió el marcador visible de cada sección y el orden de ensamblado; los `exportKind` de cada bloque NO cambiaron (exports intactos).

### Grilla 2×2 plegada (B.10)
- La grilla (la más redundante con B.4/B.5) baja al final dentro de un `<details>` colapsado: summary «B.10 · Grilla panorámica 2×2 — Ver síntesis visual de los cuatro escenarios». Sigue disponible, no satura la lectura. `state.quadOpen` persiste el plegado entre re-renders (al togglear par/color/vista no se vuelve a colapsar).

### Print
- En `@media print` se ocultan las visualizaciones SVG exploratorias (`.cmp-viz-section`: hemiciclos lado a lado, mapas lado a lado, grilla) y el selector. El anexo impreso conserva la sustancia: tabla agregada, diferencias, rankings, **trayectoria**, heatmaps, Rae, metodología/fuentes. Print: **56 → 51 páginas**.

### QA
- Sintaxis OK; datos idénticos a HEAD. Marcadores verificados secuenciales B.1…B.10. Grilla colapsada por defecto, persistencia del plegado OK. Visualizaciones siguen funcionando en pantalla (2 hemiciclos + 2 mapas + 4 celdas de grilla + 64 filas de trayectoria montadas). Print 51 páginas.

## 2026-06-03 — Análisis comparado: trayectoria de legisladores (Fase 3)

Nueva sección **B.10 Trayectoria** dentro de Análisis comparado: qué legisladores repiten banca entre las dos votaciones del par y cómo cambia o se mantiene su voto. Alcance acotado: **same-chamber** (cross-cámara queda para una fase futura). Todo aditivo; sin tocar datasets, cálculos, compute* existentes, hemiciclo/mapa principal, análisis del caso ni exports.

### Funciones nuevas
- `computeTrajectory(pair)`: match de repetidos por nombre exacto normalizado (`trajNormName`: trim, lowercase, sin tildes vía NFD, `APELLIDO, Nombre`). Usa el voto efectivo (`_sortedLegislators`, ya normalizado). Devuelve `matched`, métricas y la matriz de transición voto A × voto B. Sólo cruza si ambas cámaras coinciden.
- `renderTrajectorySection()`: HTML de B.10 (aditivo en `renderComparisonPanel`). `trajNormName(name)` helper de normalización.
- `state.trajFilter` ('all'|'changed'|'kept') + handler delegado `[data-traj-filter]`.

### Componentes
- **Métricas resumen** (data-grid): repiten banca (% del universo base), mantienen/cambian voto, mantienen/cambian familia.
- **Matriz de transición** voto A × voto B (en vez de un Sankey frágil, por decisión: "sólido y claro primero"). Diagonal = mantuvieron (tinte verde); fuera de diagonal = cambiaron (tinte oro); con totales de fila/columna.
- **Tabla nominal** con sellos de voto (A→B), badges de estado (Mantuvo/Cambió/Cambió familia), familia y bloque A→B; filtro Todos/Cambiaron/Mantuvieron.
- **Migración por familia** (familias con ≥2 repetidos): repiten, mantienen, cambian, % afirmativo A→B con Δ.
- **Estado vacío** para pares cross-cámara o sin repetidos: mensaje sobrio en caja hundida, sin matriz/tabla rotas.

### Densidad de tablas (refinamiento)
- Las tablas de B.10 desperdiciaban espacio (filas altas, columnas anchas con huecos y las últimas columnas —Familia/Bloque/Δ— cortadas por overflow). Rediseño: la nominal consolida Voto A / Voto B / Trayectoria en menos columnas (Voto `A → B` en una celda); ambas tablas pasan a `table-layout: fixed` con anchos por `<colgroup>` (caben al 100% sin overflow), padding vertical reducido, headers que envuelven y celdas largas (nombre, familia, bloque) truncadas con ellipsis + `title`. Headers de caso abreviados (Aerolíneas Argentinas → «Aero»). Resultado: ~10 filas visibles donde antes 6, y la columna Δ siempre visible.

### QA
- Sintaxis OK; datos idénticos a HEAD. Métricas verificadas: **Diputados Aero→YPF = 64 repiten** (48 mantienen, 16 cambian, 56 familia); **Senado Aero→YPF = 38** (20/18/32). Cross-cámara (Aero Dip→Sen, YPF Dip→Sen) → estado vacío (`sameChamber=false`). Toggle voto/familia no rompe B.10; cambio de par no rompe B.7–B.9; filtro nominal correcto (16 filas en "cambiaron"). Análisis del caso intacto (selección + hemiciclo 257). Print: 58 páginas (sin clipping; la regla print de `.dtable-wrap` libera overflow). Sin Sankey (matriz como visualización de transición); exports de trayectoria → Fase 4.

## 2026-06-03 — Análisis comparado: visualizaciones gráficas (Fase 1+2)

Primer entregable del rediseño del comparado (División B), que hasta ahora era 100% tabular. Agrega visualizaciones gráficas comparativas. Sin tocar datasets, cálculos ni la lógica del análisis de caso. Decisiones de alcance acordadas: trayectoria same-chamber (fase futura), resaltado en el segundo mapa (sin tercer mapa diff), grilla 2×2 fija.

### Infraestructura (Fase 1)
- `state.comparePair` (par de escenarios), `state.compareColorMode` (voto/familia, independiente del caso) y `state.quadView` (hemiciclo/mapa de la grilla). Constantes `COMPARE_PRESETS` (4 presets) y `QUAD_ORDER`.
- `ensureScenarioPrepared(s)`: preproceso idempotente (orden de bancas, layout con `_seat`, `_provinceData`) extraído de `renderVizCard` — antes era lazy sólo para el escenario activo; ahora cualquier escenario puede prepararse para renderizar varios a la vez. `getLegsForScenario(id)` centraliza el acceso (infra para la trayectoria).

### Visualizaciones (Fase 2)
- **Selector de par + color**: barra de controles con 4 presets (Diputados/Senado · Aero vs YPF, y Aero/YPF · Dip vs Sen) y toggle voto/familia, en el lenguaje de control plano del sistema.
- **B.7 Hemiciclos lado a lado**: `buildCompareHemicycleSvg()` — versión compacta read-only (sin panel central ni selección, con tooltip), reutilizando el layout precomputado. Cada uno en card editorial con header (caso · cámara · fecha · resultado) y leyenda compartida.
- **B.8 Mapas lado a lado**: `buildCompareMapSvg()` — reutiliza el choropleth; el segundo mapa resalta con borde dorado las provincias cuyo voto dominante cambió respecto del primero (`.is-changed`).
- **B.9 Grilla panorámica 2×2**: los cuatro escenarios fijos, con toggle hemiciclos/mapas compartido.
- Montaje vía `mountCompareVisuals()` tras inyectar el HTML del panel; handlers delegados para par/color/vista. Read-only (tooltips + aria, sin mutar filtros del caso). Respeta la regla voto≠partido (cada marca codifica una sola dimensión).

### QA
- Sintaxis JS OK; datos idénticos a HEAD. Test headless de interactividad: 2+2 hemiciclos/mapas y 4 celdas de grilla montados; 5 provincias resaltadas en B.8; toggles de color (relleno voto→familia), vista (grilla→mapas) y par (→Senado) funcionando. Análisis del caso intacto (selección + hemiciclo 257). Print: 50 páginas (45 previas + 5 de las nuevas secciones, sin clipping). Pendiente (fases siguientes): trayectoria/Sankey + tabla de repetidos + migración por familia, y los exports PNG de todo.

## 2026-06-03 — Resolución de auditoría experta (críticos + medios seguros)

Resolución de los hallazgos de una auditoría técnica externa. Se atacaron los 2 críticos y los medios de bajo riesgo; los medios estructurales (IIFE/use-strict) se dejaron documentados por riesgo > beneficio (ver abajo). Sin tocar datos, cálculos ni lógica de negocio.

### Críticos
- **Debounce en los buscadores**: el tecleo en el buscador del hemiciclo (`searchInput`) y en el de la tabla (`tableSearch`) re-renderizaba en cada keystroke (con 257 legisladores → thrashing). Nuevo helper `debounce()`; el atenuado de bancas + contador `aria-live` siguen **en vivo** (baratos, feedback inmediato) y sólo el re-render de tablas se agrupa (180 ms). El buscador de tabla conserva foco/cursor de forma natural mientras se tipea.
- **Foco accesible**: la regla global `:focus { outline: none; }` suprimía el indicador para TODOS los elementos antes de que `:focus-visible` lo restaurara — en navegadores sin soporte de `:focus-visible` (Edge legacy, algunos WebView) el foco de teclado quedaba invisible. Acotada a `:focus:not(:focus-visible)`: en esos navegadores el selector se invalida y el outline por defecto sigue visible (accesibilidad preservada); en los modernos sólo se oculta el outline por puntero.

### Medios resueltos
- **`:root` duplicado**: las variables de movimiento (`--ease-*`, `--dur-*`) se definían en un segundo `:root` tardío (línea ~3357) pero se usaban desde la ~280. Movidas al `:root` principal; eliminado el bloque duplicado.
- **SVG sin texto alternativo**: hemiciclo y mapa pasan a `role="img"` + `aria-labelledby` con `<title>` (nombre) y `<desc>` (resumen: conteos de voto y resultado en el hemiciclo; criterio cromático en el mapa). Complementa los `aria-label` por banca.

### No resueltos (decisión explícita, riesgo > beneficio)
- **IIFE + `'use strict'` (scope global)**: el JS vive en dos `<script>` separados; envolver cada uno en su IIFE los aislaría y rompería referencias cruzadas, y `'use strict'` sobre ~7,7K líneas en modo sloppy puede convertir un global implícito latente en un throw duro. Es higiene que no afecta la funcionalidad actual; conviene como pase propio con test dedicado.
- **Escapado de interpolaciones numéricas/de clase en innerHTML**: defensa en profundidad; los datos son hardcodeados (sin input de usuario) → sin XSS real. ~120 sitios; no se tocó para evitar churn.
- **`aria-describedby` en el tooltip**: no-issue en la práctica — cada banca ya expone un `aria-label` completo (nombre/voto/bloque/familia/provincia), así que el lector de pantalla obtiene el dato del propio elemento enfocable; el tooltip visual es redundante para usuarios sin lector.

### QA
- Sintaxis JS OK; datos idénticos a HEAD. Test headless: `<title>`/`<desc>`/`role`/`aria-labelledby` presentes en el SVG; tokens de timing resuelven desde el `:root` consolidado; debounce confirmado (atenuado inmediato 251/257, re-render agrupado). Print: 45 páginas (idéntico a HEAD). Render visual sin regresiones.

## 2026-06-03 — Rediseño "Atlas parlamentario académico" · Etapa 5 (metodología + polish final)

Quinta y última etapa: la pestaña **Metodología** y un repaso de pulido global para barrer los restos de estética "dashboard". Solo presentación; sin tocar datos, cálculos ni lógica.

### Metodología
- La pestaña ya heredó el sistema en la Etapa 4 (usa `.stats-division` + `.methodology-note`). Verificada: lee como **apéndice académico** — lámina plana, cabecera con eyebrow en oro «Apéndice metodológico», título serif «Criterios de cálculo», y los criterios como aparato crítico en dos columnas.

### Polish final (restos fuera del sistema cálido)
- **Tabla territorial de auditoría**: la fila "normalizada" (`terr-nor`) dejaba un **púrpura frío** (`rgba(91,33,182,…)`) off-palette → tinte neutro cálido. Wrapper (`.terr-wrap`) de blanco translúcido → `--card` con radio chico. Código inline de validación: gris azulado → tinta cálida.
- **Resumen ejecutivo**: el tinte slate del gradiente de celdas secundarias (`rgba(15,23,42,…)`) → tinta cálida (`rgba(20,24,31,…)`).
- **Trackings y radios**: número de figura (`.fig-num`) .16em → .14em; figuras-tabla (`.tbl-figure` header/footer) de radio grande → `--radius-xs`.
- Confirmado: ya no quedan grises fríos, púrpura ni azul UI en la capa visible (los `rgba(15,23,42,…)` restantes son tokens de sombra y trazos faint del SVG del hemiciclo; los focos azules de teclado se conservan por accesibilidad). `.exp-card` (CSS muerto, sin uso en markup) no se tocó.

### QA
- Sintaxis JS OK; datos idénticos a HEAD. Render de la pestaña Metodología verificado a 1440px. Print: 45 páginas (idéntico a HEAD). Exports no tocados. Con esto, **toda la capa visible queda bajo el mismo sistema editorial Atlas** (Etapas 1–5).

## 2026-06-03 — Rediseño "Atlas parlamentario académico" · Etapa 4 (capa analítica)

Cuarta etapa, amplia: toda la **capa analítica** pasa a un mismo sistema editorial (lámina académica · anexo técnico · matriz analítica · aparato comparado), para que ninguna parte parezca "dashboard". Solo presentación; sin tocar datos, cálculos, índices Rice/Rae, votos, nombres, bloques, provincias, familias, exportaciones (funcionalmente) ni handlers. Hecho en orden controlado y verificado bloque por bloque.

### 1 · Estadísticas del caso
- `.stats-division` → lámina plana (sin sombra, borde firme, radio chico); cabecera de división como **banda hundida** (`--paper-sunk`) con filete de archivo tinta+oro (antes gradiente). Índice de secciones hundido. KPI hero / data-grid / rankings / h-bars: tracking de rótulos unificado a .14em; track de barras editorial (hundido + filete interno).

### 2 · Análisis comparado (División B)
- Síntesis interpretativa: gradiente → banda hundida. Tabla comparativa y heatmaps como **matrices** (encabezados y filas-grupo hundidas, bordes firmes, radio chico). Tags de patrón territorial → sellos con borde. **Selector de métrica** del heatmap convertido al control plano del sistema (registro plano, hairlines, filete dorado en el activo — antes pill con relleno tinta).

### 3 · Datos / tablas
- `.tbl-card` y `.ficha-card` → láminas planas; cabecera compartida como banda hundida con filete tinta+oro. Encabezados de tabla (`th`) hundidos, wrappers con borde firme, hover de header cálido (antes gris frío `#e8eaee`). **Sellos de voto** en tablas con borde (presidencia deja el gris azulado frío `#e2e8f0`). Mini-bars territoriales editoriales (hundido + filete). Ficha institucional con tick de sección en oro del sistema; anexo técnico de exportaciones como contenedor plano firme.

### 4 · Ajuste de consistencia
- Foco de campos de formulario (buscadores + select) pasa de **anillo azul UI** (`--accent` / rgba azul) a **foco editorial cálido** (oro). Verde frío `#eef4ee` del panel de validación → token cálido. Notas analíticas (`methodology-note` / `align-note`) → hundido del sistema. Trackings dispares (.22/.18em) unificados a .14em. Los focos de **navegación por teclado** (`:focus-visible`, azul de alto contraste) se conservan por accesibilidad.

### QA
- Sintaxis JS OK; datos idénticos a HEAD (conteos y arrays). Render verificado a 1440px en los tres tabs (Análisis del caso, Análisis comparado, Datos) con selección activa. Print: 45 páginas (idéntico a HEAD, sin clipping). Builders SVG/PNG de exportación no tocados → exports intactos. ids/handlers preservados; sin cambios de markup.

## 2026-06-03 — Rediseño "Atlas parlamentario académico" · Etapa 3 (ficha lateral)

Tercera etapa: el sidebar pasa a leerse como una **ficha analítica de archivo legislativo**. Solo presentación; sin tocar datos, cálculos, conteos ni lógica de selección. Alcance acotado al sidebar — no se tocaron tablas, comparado, exports ni metodología (esas son Etapas 4–5).

### Panel → ficha de archivo
- `.panel` plano: borde `--line-strong`, radio chico, **sin sombra** y **filete dorado superior** (`::before`), igual que la lámina principal. La elevación viene del montaje + borde.

### Detalle del legislador
- Nombre en **serif** (pieza titular, coherente con `fig-title`/masthead). Línea de catálogo (caso · cámara · fecha) y rótulos de campo (`Bloque`/`Familia`/`Rol`) en **versalitas** (`all-small-caps`), distintas de los eyebrows ALL-CAPS institucionales: leen como rótulos de registro.
- Voto como **sello**: marca con borde en el color del voto sobre tinte tenue, no relleno pleno. La doctrina "Presidencia no es relleno" se cumple también aquí (el sello de presidencia pasa de relleno tinta+blanco a sello con borde).

### Resumen provincial / nacional
- Título de provincia en serif. Chips de tendencia y familia como sellos planos con borde. Barras editoriales (radio 2px, filete interno, fondo hundido). Lista de miembros y tarjetas de familia sobre **hundido** (`--paper-sunk`); badges de la lista alineados con el sello (pres deja de ir relleno; se agrega la clase `abst` que faltaba).

### Estado vacío
- Nota de registro sobria en caja hundida con filete punteado (sin itálica). Microcopy ajustado para no duplicar el subtítulo del header ("Sin banca seleccionada…").

### QA
- Sintaxis JS OK; datos idénticos a HEAD (conteos y arrays de legisladores). Render verificado a 1440 y 1280px con legislador y provincia seleccionados, y en estado vacío + panel nacional. Print: 45 páginas (idéntico a HEAD, sin clipping). Builders SVG de los PNG descargables (ficha individual / resumen) no tocados → exports intactos. ids/handlers preservados; cambio de markup: solo microcopy del estado vacío.

## 2026-06-03 — Rediseño "Atlas parlamentario académico" · Etapa 2 (lámina principal)

Segunda etapa: el hemiciclo/mapa pasa a leerse como una figura de tesis (lámina). Solo presentación; sin tocar datos, cálculos ni lógica. Alcance acotado a la figura principal — no se tocó sidebar, tablas, comparado, exports ni metodología (se cuidó el escopeo para no afectar los headers compartidos del sidebar).

### Viz-card → lámina
- Sin sombra (`shadow-md` fuera), borde `--line-strong`, radio chico, y **filete dorado superior** (firma de lámina, vía `::before`). La elevación la dan el montaje + el borde.

### Figura con número de catálogo
- Nuevo `Figura 1` (número de catálogo en oro) sobre el título; el título de la figura principal sube a `--t-xl` con presencia editorial (escopeado a `#figureHeader`, el sidebar conserva su tamaño).

### Controles de figura
- `.seg-btn` (Hemiciclo/Mapa · voto/familia) pasa de pill con relleno en tinta (app) a **control plano con filete dorado en el activo** — mismo lenguaje que el selector Caso×Cámara y las tabs. `.seg-group` como registro plano.

### Resumen y bandas internas
- "Resumen ejecutivo", pie de figura (nota+fuente), export contextual y filtros plegables pasan a fondo **hundido** (`--paper-sunk`) con hairlines; sin sombras internas. Tracking de eyebrows bajado.

### QA
- Sintaxis JS OK; datos idénticos. Render verificado a 1440 y 1024px (figura como lámina numerada con controles planos). Print: 8 páginas (sin pérdida de contenido respecto al commit previo). Markup tocado: una línea (`<div class="fig-num">Figura 1</div>`); ids/handlers intactos.

## 2026-06-03 — Rediseño "Atlas parlamentario académico" · Etapa 1 (identidad base)

Primera etapa del rediseño editorial (dirección: convención académica llevada a su versión más refinada — "serio por convención, hermoso por diseño"). Solo presentación; sin tocar datos, cálculos ni lógica. Alcance acotado: tokens, montaje de papel, sombras, filetes, masthead, selector Caso×Cámara, meta-bar, tabs, doctrina del dorado. NO se tocaron aún hemiciclo, sidebar, tablas, comparado, exports ni metodología.

### Tokens
- Tres capas de papel: `--paper-mount` (#ece4d1, fondo/paspartú), `--paper-sunk` (#f1ece0, hundidos), sobre el `--card` existente. Filetes: `--gold-rule` (#b89243), `--rule-ink` (#1f2329).
- `body` pasa a `--paper-mount`: las láminas "se montan" sobre el paspartú en vez de flotar.

### Masthead (portada de tesis)
- Nueva jerarquía: eyebrow «El péndulo del poder · Herramienta de visualización legislativa» → H1 «Reestatizaciones de YPF y Aerolíneas Argentinas» (serif 31px) → subtítulo «Análisis legislativo comparado de las leyes 26.466 y 26.741 en Diputados y Senado.» → marco institucional (leyes + BO). Tracking del eyebrow bajado (.22→.14em); filete tinta+oro de archivo al pie.

### Selector Caso × Cámara
- De segmento con relleno en tinta (app) a control plano refinado: registro sobre papel, segmentos separados por hairline, activo marcado por filete dorado de 2px (sin relleno).

### Meta-bar
- Ficha institucional: sin sombra, radio chico, filete dorado lateral (marca de registro), borde `--line-strong`.

### Tabs
- Navegación de informe: subrayado dorado estático e inset (no deslizante de dashboard).

### Doctrina del dorado / sombras
- Dorado sólo como acento de archivo (filetes, subrayado activo, marca de registro), no como relleno. Sombras retiradas de los componentes tocados (la elevación la dan montaje + borde + filete).

### QA
- Sintaxis JS OK; datos idénticos. Render verificado a 1440 y 1280px. Print: el montaje pasa a blanco, filetes y meta-bar (expandida) se conservan. Markup tocado: solo el texto del masthead (eyebrow/h1/lede); ids/handlers intactos.

## 2026-06-02 — Figuras comparadas: fuente general en el footer

En los PNG comparados, el footer mostraba la FUENTE (y fuente territorial) del escenario activo — engañoso en una figura de 4 escenarios. `buildTablePngFooter` acepta `opts.generalSource`: cuando se pasa, imprime una sola FUENTE general y omite la fuente territorial de un solo escenario. Texto: «Elaboración propia sobre los cuatro datasets validados de votaciones nominales HCDN/HSN — leyes 26.466 y 26.741.» Aplicado a los granulares comparados (`fam-comp-wide`, `prov-comp-wide`, `comp-rae`) vía `spec.noScenarioInName`. El `comparator-png` ya tenía su propio footer general (se conserva). Los PNG de un solo escenario (tablas, fichas, Rice/Rae por escenario, indicadores) conservan su fuente específica. Solo presentación; sin cambios de datos.

## 2026-06-02 — Figuras comparadas: sin meta-chips de un solo escenario

En los PNG de **Análisis comparado** (comparador 4 escenarios, comparador wide de familias y de provincias), el header mostraba los chips "CASO · CÁMARA · FECHA · LEY" del escenario activo — engañoso, porque la figura compara los 4 casos (y el caso/cámara/fecha de cada votación ya va en los encabezados de columna). `buildTablePngHeader` acepta `opts.noMetaChips`: se omiten los chips (y el header se acorta) cuando el export es comparado. Se detecta por `spec.noScenarioInName` (granulares wide) y explícito en el comparador. Los PNG de un solo escenario (tablas, fichas, Rice/Rae por escenario, indicadores) conservan los chips. Solo presentación; sin cambios de datos.

## 2026-06-02 — Fix: textos encimados en los PNG exportables

Los PNG de tabla mostraban encabezados y meta-chips encimados (p. ej. "CÁMARADiputados", "ABSTENCIONES/AUSENTES/%AFIRMATIVO/PROVINCIAS" pisándose). Causa raíz: los anchos de texto se estimaban por conteo de caracteres (sin medir, sin letter-spacing) y las columnas tenían anchos fraccionales fijos demasiado angostos para sus encabezados. Solo presentación de exports; sin cambios de datos.

### Medición real en vez de estimación por caracteres
- Nuevo `_measureTextW(text, px, weight, family, letterSpacing)` con `canvas.measureText` (suma el letter-spacing aparte). Reemplaza los cálculos `length * N`.
- **Meta-chips** (`buildTablePngHeader`, usados por todos los PNG de tabla y las fichas): el avance del cursor key→value usa anchos medidos → se acabó el "CÁMARADiputados".
- **Columnas de tabla** (`buildTablePngSvg`): cada columna se dimensiona al **máximo entre su fracción de diseño y el ancho medido de su encabezado** (+ margen); el lienzo se ensancha lo necesario. Las columnas de **badge de voto** además garantizan el ancho del pill más largo (p. ej. "Presidencia · no votó") para que no se desborde a la columna siguiente.
- **Leyenda de la figura con contexto** (`exportSvgAsPng`): el posicionamiento de cada chip usa ancho medido → sin encimado entre familias.
- **Badges de voto** (renderers `voteBadge`/`voteBadgeOrMixed`): el rect del pill se ajusta al texto medido.

### Tablas de estadísticas granulares (mismo bug)
- El layout `simple-table` de `exportStatsSectionPng` (Rice, Rae, comparadores wide de familia/territorio) usaba el **mismo** esquema fraccional fijo → mismos encabezados encimados. Ahora dimensiona cada columna al máximo entre su fracción y el ancho medido del encabezado, ensancha el lienzo si hace falta o escala para llenar.

### Auditoría completa por render real (todos los PNG descargables)
Se rendereó y verificó **cada** export: tabla nominal / provincias / familias; hemiciclo y mapa con contexto académico; comparador 4 escenarios; indicadores; granulares Rice familia/bloque, Rae escenario/comparador, comparadores wide familia/territorio; ficha institucional, ficha de legislador y resumen provincial/nacional. En todos: meta-chips, encabezados, badges y leyendas sin encimarse. Las figuras "solo gráfico" (sin contexto) no tienen tablas de texto. Datos y cálculos intactos.

## 2026-06-02 — Scroll independiente del sidebar (desktop)

UX de scroll del sidebar derecho. Solo CSS (sin JS). En desktop/notebook el sidebar queda sticky con scroll interno propio, independiente de la columna izquierda; debajo de 1100px vuelve al comportamiento normal en flujo.

### `.sidebar` (≥1101px, vía base + reset <1100)
- `position: sticky; top: 50px` (despeja el ancla de contexto sticky al scrollear).
- `max-height: calc(100vh - 66px)` + `overflow-y: auto` → scroll interno independiente.
- `overscroll-behavior: contain` (no arrastra la página al llegar al final), `scrollbar-gutter: stable` (sin salto de layout).
- Scrollbar discreto editorial: `scrollbar-width: thin; scrollbar-color: var(--line-strong) transparent` + pseudo-elementos webkit (thumb `--line-strong` redondeado, inset 2px).
- `@media (min-width: 1101px) .sidebar .member-list{ max-height: none; overflow: visible }`: el sidebar es el **único** contenedor con scroll → sin doble scroll. Debajo de 1100px la `.member-list` conserva su cap de 340px (comportamiento previo).
- `@media (max-width: 1100px) .sidebar{ position: static; max-height: none; overflow: visible }`.

### Impresión
- `@media print .sidebar{ position: static; max-height: none; overflow: visible }`: la ficha/resumen activos no se recortan al imprimir.

### QA
- Estilos computados verificados: 1440/1280 → sticky + overflow-y:auto + max-height + member-list:none; 1024 → static + visible + member-list 340px. Render real (Buenos Aires, 70 legisladores): scroll interno del sidebar independiente del de la izquierda, top despeja el ancla. PDF de impresión sin recorte. Sin cambios de datos, cálculos ni estructura de tabs.

## 2026-06-02 — Pulido de movimiento (Parte 2, lote 2) + count-up robusto

Continuación del pulido. Fuente: se mantiene el stack de sistema (sin Google Fonts ni fuente embebida). Solo presentación; datos y cálculos intactos. Estado final de toda animación = visible; respeta print y reduced-motion.

- **Count-up de KPIs por delegación:** el listener de tabs pasa a `document` (sobrevive a los re-render de `renderVizCard` que reconstruyen los subtabs). El flag `omCounted` evita doble conteo; los KPIs recién renderizados arrancan de su valor final correcto.
- **#8 Barras que crecen desde 0:** `omBarGrow` (scaleX 0→1, `transform-origin` izquierda, fill `backwards`). Aplica a `.h-bar-fill` y `.mini-bar` en el panel activo y a `.prov-bar`/`.fam-bar` del sidebar. El estado base/final es scaleX(1) = barra completa: si no corre (o reduced-motion), la barra queda llena. Sin JS, sin riesgo de barra en 0.
- **#11 Texto adaptativo en heatmaps:** `_heatTextDark(hex,pct)` mezcla el color de celda sobre el papel y mide luminancia; si la celda fuera oscura, el texto pasa a claro (`.is-dark`). Con la escala actual (alpha ≤ .55) ninguna celda llega a oscura, así que la tinta se mantiene (ya con ≥4.8:1); queda como red de seguridad. No cambia datos ni escala.
- **#15 Crossfade de subpaneles:** `omFade` al activarse el subpanel (termina y base en opacity:1; nunca invisible).
- **#16 Estado vacío sobrio:** si la tabla nominal no devuelve filas, muestra un mensaje académico centrado (`.dtable-empty`) en vez de un hueco.
- **#18 Tiempos unificados:** transiciones sueltas de los controles principales (`.seg-btn`, `.nav-seg`, `.subtab`, `.search-input`, `.select-input`) pasan a `--dur-*`/`--ease-*`.
- **#19 Ritmo:** revisado; el sistema ya usa `--space-*`/`--radius-*` de forma consistente, sin inconsistencias que justifiquen tocar layout (no se modificó).
- **#20 Print / reduced-motion:** guard de impresión ampliado a `.subpanel` y a las barras (`opacity:1`/`transform:none`); `prefers-reduced-motion` (`*{animation-duration:.001ms}`) neutraliza también `omBarGrow`/`omFade`.
- **No implementados (por pedido):** #9 crossfade de color de bancas, #14 progreso de scroll.

### QA
- 11/11 headless: estado vacío + restauración, `omBarGrow` en mini-bar/h-bar-fill/fam-bar, `omFade` en subpanel, `_heatTextDark` correcto (intensa→tinta, real-oscura→claro), transiciones tokenizadas. Render real: todo visible con barras completas. PDF de impresión sin error. Datasets idénticos.

## 2026-06-02 — Capa de movimiento editorial (Parte 1) + pulido acoplado

Capa de animación y microinteracciones sobre el sistema editorial existente. Solo presentación; reutiliza tokens. Regla respetada: el estado final de toda animación es el estado visible (`animation … backwards` → revierte a opacity:1; si no corre, queda en el valor base visible). Datos y cálculos intactos.

### Parte 1 (movimiento)
- Nuevos tokens `--ease-out/--ease-soft/--dur-1..4`. Entrada de página escalonada (masthead → nav → workspace) con `omRise`. Entrada de secciones (`.stats-section/.tbl-card/.ficha-card`) al activarse su panel.
- **Floración del hemiciclo** (`omSeatBloom`) al cambiar de escenario: cada banca anima con delay `calc(var(--seat-i) * 1.5ms)` (seteado en `renderHemicycle`); se dispara solo al cambiar de escenario (`state._bloomScenario`), no en cada toggle de color/vista, y respeta `prefers-reduced-motion`.
- Subrayado dorado deslizante en subtabs; microinteracciones de hover (pills, btn-exp, stats-index, member-row, heatmap-cell).
- **Count-up de KPIs** (`.kpi-hero-value`) al activar el tab; sin IntersectionObserver; desactivado bajo movimiento reducido.
- Guards: `@media print` fuerza `opacity:1`/`transform:none` en lo animado; `prefers-reduced-motion` (`*{animation-duration:.001ms}`) neutraliza todo.

### Parte 2 — ítems acoplados (de bajo riesgo)
- #13 latido del halo en la banca seleccionada (`is-selected` en select/deselect).
- #10 hover de banca con `scale(1.25)` (desactivado bajo reduced-motion).
- #12 tooltip con micro fade+rise; el flip contra bordes ya existía.

### QA
- Sintaxis OK; datasets idénticos. Floración verificada (florece en 1er render y al cambiar de escenario; NO al togglear color). `--seat-i` en bancas, halo is-selected toggle, CSS de subrayado presente. **Render real confirma que nada queda invisible** (el opacity:0 en virtual-time headless es el frame `backwards`-fill durante la animación, no persistente). PDF de impresión sin error.

## 2026-06-02 — Presidencia como rol: voto efectivo según el acta

"Presidencia" deja de ser una categoría de voto que mezcla situaciones distintas. Quien preside se clasifica por su voto efectivo según el acta oficial; "Presidencia · no votó" queda solo para quien presidió sin voto computado.

### Normalización en el origen
- Nueva `normalizeScenarioVotes(s)` (corre una vez por escenario antes del primer render): si el voto de quien preside está computado en el resultado oficial (`afirmativos_dataset + presidencia == yes` y `afirmativos != yes`), reclasifica su `vote` de PRESIDENCIA a AFIRMATIVO y marca `leg.presided = true`. Como todo el render lee `leg.vote`, la regla queda pareja en hemiciclo, leyenda, KPIs, barras, tablas, comparado, CSV y PNG **de una sola vez**. No altera los datasets del archivo (es presentación en runtime) ni las funciones `compute*`.
- Caso real: **Pampuro · Aerolíneas Senado → Afirmativo** (el acta lo cuenta en los 42). **Fellner · Aerolíneas Diputados** y **Domínguez · YPF Diputados → "Presidencia · no votó"** (presidieron sin voto computado; cuentan solo en el total de bancas, como remanente). YPF Senado: sin caso.

### Categoría y rol
- `VOTE_LABELS`/`VOTE_LABELS_CSV` de PRESIDENCIA → **"Presidencia · no votó"** (la categoría significa únicamente eso).
- El rol institucional se conserva para quien votó: tooltip y ficha lateral muestran "Rol: presidió la sesión"; la ficha PNG agrega una fila "ROL · Presidió la sesión"; el CSV de legislador agrega columna "Rol"; el aria-label lo incluye.

### Coherencia verificada (24/24 headless)
- Conteos == acta oficial en los 4 escenarios (afirmativos/negativos/ausentes); `validateScenario` OK en los 4.
- **Hemiciclo == resumen == acta:** Aerolíneas Senado muestra 42 bancas verdes = "42 a favor" = 42 oficiales; FPV-PJ 35 a favor (Pampuro incluido). Leyenda sin chip de Presidencia donde el presidente votó; "Presidencia · no votó (1)" donde no votó.
- Datasets del archivo fuente idénticos (la reclasificación es en runtime).

## 2026-06-02 — Regla: la presidencia no se cuenta como voto

La categoría PRESIDENCIA (quien preside la sesión) no es un voto: deja de mostrarse como segmento ni de computarse como ausente en las composiciones de voto, de forma pareja en toda la herramienta. La banca sigue contando en el total (denominador); aparece como remanente vacío de la barra (igual que ya hacía el sidebar). Sin cambios de datos ni de `compute*`: solo cómo se presenta la presidencia.

### Corregido (presidencia ya no se folda en ausentes ni se dibuja como segmento)
- PNG de resumen nacional/provincial (`buildProvSummarySvg`): barra nacional y barras por familia sacan el segmento de presidencia y escalan al total (remanente vacío); el mini-conteo usa solo `AUSENTE`. `_svgBar` acepta un total de escala explícito.
- CSV de resumen (`exportProvSummaryCsv`): columna Ausentes = `AUSENTE` (antes `AUSENTE + PRESIDENCIA`).
- Tabla por familia (mini-bar de composición): `AUSENTE` (antes `AUSENTE + PRESIDENCIA`).
- Tabla por provincia (mini-bar de composición): segmento ausente = `AUSENTE` (antes `AUSENTE + PRESIDENCIA`).
- El sidebar (`renderNationalPanel`/`renderProvincePanel`) ya cumplía la regla; sin cambios.

### Se conservó (metadata explícita, no composición de voto)
- Las filas "Presidencia: N · categoría institucional" en Estadísticas y en la ficha institucional, y la presidencia dentro del total de bancas (denominador). Son metadata transparente, no presentan presidencia como voto ni como ausente.

## 2026-06-02 — Rediseño del PNG de resumen nacional/provincial

`buildProvSummarySvg` pasa de una lista textual ancha (900px, plana, sin barras, con mucho espacio vacío) a una **ficha vertical editorial compacta** (840px de ancho, alto según familias). Solo se modificó esa función (más el helper `_svgBar`); ningún otro export, dato ni cálculo se tocó.

### Nuevo diseño
- **Header académico reutilizado** (`buildTablePngHeader`): eyebrow "FIGURA EXPORTADA · HERRAMIENTA DE VISUALIZACIÓN LEGISLATIVA", título serif ("Resumen nacional del cuerpo" / "Resumen provincial — {Provincia}"), subtítulo y chips CASO·CÁMARA·FECHA·LEY.
- **Bloque 1 · Composición del voto:** ámbito + total de bancas, **barra horizontal apilada** por voto, y números grandes coloreados con label ("153 a favor · 84 en contra · 19 ausentes").
- **Bloque 2 · Comportamiento por familia:** tarjeta sutil por familia con dot del color de familia, nombre, total (serif), **barra apilada por voto dentro de la familia** y mini-conteo (solo valores no-cero).
- **Footer académico reutilizado** (`buildTablePngFooter`): FUENTE · FUENTE TERRITORIAL · ELABORACIÓN PROPIA.
- Identidad mantenida: fondo papel cálido, tinta, dorado editorial; **colores de voto en las barras** (verde/rojo/ocre/piedra/tinta), color de familia **solo como dot**; cards y barras con bordes suaves.

### QA
- Render real verificado (SVG inyectado y capturado) para nacional (257 bancas, 10 familias) y provincial (Córdoba, 18 bancas): barras, jerarquía, header/footer y datos correctos. Sintaxis OK. Sin cambios de datos ni de otros exports.

## 2026-06-02 — Fichas del sidebar descargables (CSV + PNG) + título dinámico

Las dos fichas del panel lateral (Ficha individual del legislador, Resumen provincial) ahora se descargan en CSV y PNG, y el título del resumen refleja el ámbito. Solo presentación/exportación: sin cambios de datos ni de cálculo.

### Título dinámico del resumen
- Sin provincia seleccionada → **"Resumen nacional"** (sigue mostrando el cuerpo completo, "Total cuerpo · N bancas"); con provincia → **"Resumen provincial — {Provincia}"**. La ficha individual lleva el nombre: **"Ficha individual — {Apellido, Nombre}"** cuando hay selección.

### Descarga de las dos fichas
- **Ficha individual:** botones ↓CSV / ↓PNG, **solo cuando hay un legislador seleccionado** (sin selección queda el "Seleccioná una banca…"). CSV = una fila con caso/cámara/fecha/ley + nombre, voto, bloque, familia, provincia. PNG = figura editorial (nombre en serif, voto coloreado, bloque/familia/provincia).
- **Resumen provincial/nacional:** botones ↓CSV / ↓PNG **siempre**; exporta la provincia seleccionada o el resumen nacional si no hay selección (coincide con el título). CSV = una fila por familia (bancas, afirmativos/negativos/abstención/ausentes, % del ámbito). PNG = composición del voto + comportamiento por familia.

### Implementación
- Nuevas funciones de export (`exportLegislatorCsv/Png`, `exportProvSummaryCsv/Png`) y builders SVG (`buildLegislatorFichaSvg`, `buildProvSummarySvg`) modelados en `buildSummaryFichaSvg`, reutilizando `buildTablePngHeader/Footer`, `rasterizeSvgToPng`, `exportCsv` y los helpers `csv*`. Nuevas keys `legislator-csv/png`, `provsum-csv/png` despachadas por la delegación existente (`handleExportClick`). Datos leídos de `state` + agregados ya calculados (`_provinceData`, `computeVoteSummary`); ninguna función `compute*` modificada.
- Botones ocultos en impresión (`.panel-actions` → print hidden list).

### QA
- 18/18 headless: títulos (nacional/provincial/ficha con nombre), botones condicionales (ficha solo con selección), `_summaryExportData` rutea provincia vs nacional, familias nacionales suman 257, SVGs válidos con el contenido esperado, CSV disparan descarga sin throw, delegación rutea las keys nuevas. Verificación visual del sidebar con selección. Datos idénticos.

## 2026-06-02 — Fix · foco del buscador de la tabla nominal

El buscador interno de la tabla nominal (`#tableSearch`) perdía el foco en cada tecla: su handler `input` llama a `renderTables()`, que reescribe todo el `#tablesPanel` y recrea el input. Solo se podía escribir de a una letra. Ahora, tras el re-render, se restaura el foco y la posición del cursor al input recreado. Bug pre-existente al patrón de re-render; sin cambios de datos ni de la lógica de filtrado (`getFilteredLegislators` intacta). Verificado en DOM headless: tipear de corrido acumula el texto, mantiene el foco, filtra y limpia bien.

## 2026-06-02 — Reorganización IA · cuatro tabs por nivel de lectura

Reestructura de arquitectura de información (no rediseño visual). Las 4 sub-tabs pasan de **Visualización / Estadísticas / Tablas / Exportar** a **Análisis del caso / Análisis comparado / Datos / Metodología**. "Exportar" deja de ser tab: las exportaciones se reubican contextualmente. **Sin perder ninguna funcionalidad ni ningún botón de export** (verificado: los 30 `data-export` siguen presentes). Sin cambios de datos ni de cálculo.

### Nueva arquitectura de tabs
- **Análisis del caso** (`data-subtab="case"`): figura (hemiciclo/mapa) + controles vista/color + filtros plegables + leyenda + aviso modo familia + contador + summary-strip + export contextual de figura + validación del escenario + **División A** (lectura estadística del caso, ruteada desde `renderScenarioIndicators`). "Ver + interpretar el escenario activo."
- **Análisis comparado** (`compare`): **División B** completa (`renderComparisonPanel`: tablas comparadas, heatmaps, Rae comparado). "Comparar entre escenarios."
- **Datos** (`data`): las 4 tablas (`renderTables`) + sus exports CSV/PNG inline + disclosure **Exportaciones avanzadas**.
- **Metodología** (`method`): `renderMethodologyNotes`. "Fuentes, criterios y límites."
- `renderStatsTab` ahora rutea A→`#caseStatsPanel`, B→`#comparePanel`, metodología→`#methodPanel` (mismas funciones, distinto contenedor). `state.subtab` inicial `'viz'`→`'case'`. `activateSubtab(state.subtab)` se llama al final de `renderVizCard` para sincronizar la tab activa en cada re-render.

### Reubicación de exportaciones (ninguna se borró)
- **Figura** (`hemicycle/map-png[-ctx]`): export contextual `.ctx-export` en Análisis del caso, **view-aware** (muestra la figura activa) + botón **Imprimir / PDF** (`data-export="print"` → `window.print()`).
- **Indicadores del caso** (`stats-png/csv`): ya inline en el header de División A.
- **Comparador** (`comparator-png/csv`) + **comparativo general** (`summary-all-csv`, antes solo en la tab Exportar): en el header de División B.
- **Tablas** (`legislators/provinces/families/summary-png/csv`): ya inline en cada `.tbl-card`.
- **Granulares** (Rice, Rae, comparadores wide × csv/png, 12 botones): en el disclosure `.adv-export` "Exportaciones avanzadas" dentro de Datos.
- Exports por **delegación** (`handleExportClick`) intactos: mover los botones no rompe handlers.

### Filtros plegables
- Buscador, select de provincia, pills de voto, limpiar — dentro de `<details class="filter-disclosure">` "Filtrar y buscar". Arranca cerrado; **se abre automáticamente si hay filtros activos** (con indicador "· filtros activos"). El contador `aria-live` se mantiene. El primer impacto vuelve a ser el gráfico.

### Índice de secciones (División A)
- Nuevo `buildStatsIndex()`: arma una barra de saltos rápidos (A.1 · A.2 · …) a partir de los marcadores existentes, asignando ids a cada `.stats-section`. No duplica ni borra contenido; A.7 (nota secundaria) se incluye como salto.

### Impresión
- Hidden list: fuera `.viz-export-bar` (eliminado); dentro `.ctx-export, .adv-export, .filter-disclosure, .stats-index`. `break-before: page` reapuntado a `#comparePanel, #tablesPanel, #methodPanel`.

### QA
- Batería headless **35/35**: datos en los 4 escenarios; 4 tabs nuevas + Exportar eliminada; A/B/metodología/tablas ruteadas y pobladas; índice con ≥5 saltos; `activateSubtab` en las 4; disclosure de filtros abre/cierra según filtros; nav por ejes, modo color, mapa, contador, selección toggle, roving tabindex, aria-labels, export por delegación sin throw. Render verificado de las 4 tabs a 1280px; PDF de impresión sin error (~7,9 MB). Los 30 `data-export` presentes.

## 2026-06-02 — Etapa 8 · polish final y batería de pruebas

Octava y última etapa del plan de rediseño. Verificación integral + limpieza. Solo presentación.

### Limpieza
- Eliminado el único bloque de CSS muerto que dejó el rediseño: `.legend-note` (la nota chica de la leyenda en modo familia, reemplazada por `.color-notice` en la Etapa 4). Los demás huérfanos potenciales (`.scenario-tab`, `.st-*`, `.legend-note-family`) ya se habían limpiado en sus etapas (0 ocurrencias).

### Verificado (ya estaba resuelto)
- `prefers-reduced-motion` usa selector `*` → cubre todos los elementos, incluidos los nuevos (ancla, avisos, heatmaps).
- Tooltip (`z-index 100`) por encima del ancla de contexto (`z-index 50`): no se tapan. El tooltip ya hace flip en bordes derecho/inferior.
- `:focus-visible` unificado (incluye `.nav-seg`); hover de bancas sin cambio de `stroke-width`.

### Batería de pruebas (headless, 20/20 ✓)
- **Datos:** conteos (afirmativo/negativo/ausente) y total de bancas correctos en los 4 escenarios.
- **Interacción:** navegación por dos ejes (cambia caso y cámara), modo color (aviso de familia aparece/oculta), vista mapa↔hemiciclo, filtros (contador refleja), selección por toggle (selecciona→deselecciona).
- **Teclado/accesibilidad:** `navigableSeats` no vacío, `focusSeat` fija un único tab-stop (roving tabindex), todas las bancas con `aria-label`.
- **Render:** verificado a 1440 / 1366 / 1280 / 1024 px sin roturas; impresión verificada en la Etapa 7.

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
