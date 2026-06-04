# Uso de la herramienta

Guía corta para operar la herramienta en cada uno de sus modos.

## Apertura

Recomendado: levantar un servidor local desde la raíz del proyecto.

```bash
cd /Users/vdc78/Desktop/Tesis
python3 -m http.server 8000
```

Abrir el navegador en `http://localhost:8000/herramienta_votaciones/`.

## Selección de la votación

Dentro de las pestañas **Análisis del caso** y **Datos**, un selector de caso/cámara elige cuál de las cuatro votaciones se analiza: dos botones de caso (Aerolíneas Argentinas / YPF) y dos de cámara (Diputados / Senado). Al cambiar, todos los componentes (hemiciclo, mapa, indicadores, tablas, paneles laterales) se actualizan al instante y se limpian los filtros activos.

## Modos de color

Encima del hemiciclo hay dos botones que controlan cómo se pintan las bancas y las provincias:

- **Colorear por voto.** Verde hunter (afirmativo), rojo burgundy (negativo), piedra cálida (ausente), ocre mostaza (abstención), tinta profunda (presidencia). Es el modo que sirve para analizar el resultado legislativo.
- **Colorear por familia política.** En modo familia política, el color de cada banca identifica la familia político-partidaria. El sentido del voto se consulta en tooltip, panel individual, filtros y tablas. La banca **no intenta mostrar voto y familia al mismo tiempo** para evitar ruido visual.

La regla central es **no mezclar las dos paletas en la misma marca**. Una banca, un plano: cada modo codifica una única variable. Para cruzar voto y familia: hover (tooltip), click (panel individual), filtros de voto y tablas analíticas.

## Filtros

Debajo de los botones de modo está la barra de filtros:

- **Buscador libre.** Filtra por nombre, bloque, provincia o familia.
- **Selector de provincia.** Filtra todo el hemiciclo y muestra el panel provincial en el sidebar.
- **Pills de voto.** Todos / A favor / En contra / Abstención / Ausentes. Atenúan las bancas que no coinciden con el voto seleccionado.
- **Limpiar filtros.** Vuelve a estado inicial.

Los filtros son acumulativos: se pueden combinar para aislar, por ejemplo, "diputados negativos de Buenos Aires" o "afirmativos del FPV-PJ".

## Selección de legislador

Click en cualquier banca del hemiciclo abre el panel **Legislador seleccionado** en el sidebar derecho. Muestra:

- Nombre completo.
- Provincia.
- Bloque y familia política (etiqueta + color).
- Voto (etiqueta + color).

El click también lleva al panel provincial al distrito del legislador.

## Selección de provincia

Click en el mapa, o sobre una fila del panel provincial, filtra el hemiciclo a esa provincia. El panel provincial muestra:

- Cantidad de bancas.
- Tendencia (predominante o composición mixta).
- Barra horizontal con la distribución del voto.
- Lista completa de representantes con bloque, familia y voto.

## Las cuatro pestañas

La herramienta se organiza en cuatro pestañas en la cabecera:

- **Análisis del caso** — la votación seleccionada: hemiciclo y mapa provincial (con el toggle de vista y el de color), filtros, resumen, ficha institucional colapsable, panel de validación, los **indicadores politológicos del escenario** (resultado agregado, participación, cohesión por familia y bloque, territorialización, cohesión dicotómica de Rice y fragmentación de Rae, con síntesis interpretativa automática) y el panel lateral (legislador seleccionado · resumen nacional/provincial).
- **Análisis comparado** — comparación sistemática de las cuatro votaciones: tabla agregada, diferencias entre casos y entre cámaras, rankings, hemiciclos y mapas lado a lado (con resumen territorial de cambios), trayectoria de legisladores (intra-cámara y entre cámaras), familias por % afirmativo, provincias por voto predominante, Rae comparado y grilla panorámica 2×2.
- **Datos** — base nominal de la votación: **ficha institucional** (5 secciones: identificación · trámite · conteos · fuentes · validación) y **tabla nominal de legisladores** (buscador, headers ordenables, badges de voto, chips de familia).
- **Metodología** — criterios de clasificación de votos, fuentes y universo, definiciones de Rice/Rae, tratamiento de presidencia y no voto, bloques y familias, agregación territorial y criterios de trayectoria.

> Las tablas territorial por provincia y por familia política, y las exportaciones, se generan desde la pestaña correspondiente (Caso / Comparado / Datos) junto al contenido que describen.

## Exportar a PNG

Los botones de exportación están en cada pestaña, junto al contenido que generan (además, **Imprimir / PDF** produce el anexo completo paginado de ~54 páginas):

### Figuras PNG

- **Hemiciclo / mapa con contexto académico (recomendado para tesis).** Wrapper editorial completo: eyebrow "FIGURA EXPORTADA · HERRAMIENTA DE VISUALIZACIÓN LEGISLATIVA", título en IBM Plex Sans, bajada, franja de metadatos institucionales, gráfico centrado, leyenda cromática con conteos, nota de lectura, fuentes documentales, nota de Malvinas (mapa), crédito al pie. Filename: `<escenario>_<figura>_contexto_<voto|familia>.png` (ej. `aerolineas_diputados_hemiciclo_contexto_voto.png`).
- **Solo gráfico PNG.** El SVG crudo sin wrapper, para usos editoriales donde el contexto se compone aparte. Filename: `<escenario>_<figura>_<voto|familia>.png`.

### Tablas como figura PNG

Cuatro figuras editoriales:

- **Tabla nominal** — `<escenario>_tabla_legisladores.png`. Por defecto exporta las filas visibles tras filtros y búsqueda. Si no hay filtros activos y son > 100 filas, el browser pide confirmación antes (porque el PNG resultante es alto y denso); para la nómina completa de 257 legisladores se recomienda usar el CSV.
- **Resumen provincial** — `<escenario>_tabla_provincias.png`. ~24 jurisdicciones con mini-barras de composición.
- **Resumen por familia** — `<escenario>_tabla_familias.png`. ~10 familias políticas con bloques como sub-línea.
- **Resumen institucional** — `<escenario>_resumen_institucional.png`. Ficha académica formal, 5 secciones con tick por sección.

Características de los PNG: fondo blanco `#ffffff`, rasterizado 2,5×, **tipografía IBM Plex Sans embebida** (idéntica online u offline, en cualquier máquina), badges/chips/mini-barras como SVG primitives.

## Exportar a CSV

Cinco salidas CSV, cada una desde su tabla o panel:

- **Legisladores** — `<escenario>_tabla_legisladores.csv` (del escenario activo, respeta filtros y búsqueda activos).
- **Provincias** — `<escenario>_tabla_provincias.csv` (con porcentajes 1-decimal sobre el total del distrito).
- **Familias políticas** — `<escenario>_tabla_familias.csv` (con provincias donde aparece y bloques incluidos como listas).
- **Resumen institucional** — `<escenario>_resumen_institucional.csv` (20 columnas, una sola fila, sin campos vacíos).
- **Comparativo general** — `comparativo_votaciones_resumen.csv` (4 filas, los 4 escenarios; con margen, % afirmativo/negativo sobre presentes, fuentes y estado de validación).

Características del CSV:

- Codificación UTF-8 con BOM (Excel y Numbers reconocen acentos).
- Voto en formato académico (Afirmativo / Negativo / Abstención / Ausente / Presidencia), no las keys técnicas.
- Cada fila incluye contexto institucional (Caso · Cámara · Fecha · Ley) y fuentes documentales (Fuente del voto · Fuente territorial).
- Fallbacks editoriales: "No consignado" / "No corresponde" / "Sin dato en fuente". Nunca celdas institucionales vacías.

## Revisión previa a usar una imagen en la tesis

Antes de pegar un PNG en el documento final, conviene chequear:

1. **Modo correcto.** El nombre del archivo confirma el modo (`_voto` o `_familia`).
2. **Versión con contexto académico.** Para la tesis, usar las versiones con contexto (incluyen título, fuente, leyenda y crédito embebidos). Las versiones "solo gráfico" son para editorial fino donde el contexto se compone aparte.
3. **Filtros y selecciones residuales.** Si la imagen se generó con filtros activos o con una provincia destacada por click, esa información queda en el PNG. Para vista de conjunto, limpiar filtros antes de exportar.
4. **Tabla nominal larga.** El PNG sin filtros sale denso. La forma académicamente correcta es: filtrar al subconjunto que se va a analizar (por voto, provincia o búsqueda) y exportar ese; o usar el CSV para la nómina completa.

## Atajos útiles

- **Limpiar filtros**: restablece búsqueda, provincia, voto, tabla y orden.
- **Cambio de escenario**: limpia todos los filtros automáticamente.
- **Toggle voto/familia**: no afecta los filtros, sólo cambia los colores.

## Si algo no se ve bien

- El hemiciclo o el mapa están en blanco → revisar consola del navegador (F12). Es probable que el archivo no se haya cargado por una restricción de `file://`. Solución: usar el servidor local.
- Los PNG no salen con la tipografía esperada → la fuente IBM Plex Sans va embebida en el HTML; si se editó el archivo, confirmar que el bloque `<style id="plex-fonts">` con los `@font-face` sigue presente.
- Una provincia falta en el mapa → revisar `PROVINCE_PATHS` en el HTML. Si todas las 24 jurisdicciones están definidas, el problema es otro.
