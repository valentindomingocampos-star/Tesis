# Checklist de entrega v1.0

Versión estable de la herramienta de visualización legislativa.
Fecha de cierre: **2026-05-19**.

Este documento se usa para verificar — antes de dar por entregada la herramienta a la tesis — que cada componente funciona, que cada salida es válida y que la trazabilidad metodológica está completa.

## 1. Integridad de datos

- [x] Datasets de los 4 escenarios intactos: `257 / 72 / 257 / 72` legisladores/senadores.
- [x] Voto, nombre, bloque, familia y provincia de cada registro sin modificar desde la última reconstrucción (2026-05-14).
- [x] `validateScenario` y `expectedDistribution` sin modificar.
- [x] `voteSource` y `districtSource` declarados en cada escenario.
- [x] `trazabilidad/` preserva los CSV por senador (Senado AAAA y YPF) con 72 filas cada uno.

## 2. Validación técnica

- [x] `node --check` OK sobre el JS extraído de `index.html`.
- [x] Server local respondiendo `HTTP/1.0 200 OK` en `http://localhost:8000/index.html`.
- [x] HTML autocontenido (sin dependencias externas, sin CDN, sin fonts remotos).

## 3. Modos de visualización

- [x] **Modo voto** funciona en hemiciclo y mapa; bancas/provincias coloreadas por sentido del voto.
- [x] **Modo familia política** funciona en hemiciclo y mapa; bancas/provincias coloreadas por familia (10 categorías), sin ojo-de-buey ni doble codificación.
- [x] Toggle modo voto ↔ familia no rompe filtros ni selecciones.
- [x] Tooltip muestra voto + familia + bloque + provincia al hover sobre banca.
- [x] Click en banca abre el panel de legislador con voto destacado.
- [x] Click en provincia (mapa o sidebar) filtra el hemiciclo y abre panel provincial.

## 4. Filtros

- [x] Buscador libre filtra por nombre, bloque, provincia, familia.
- [x] Selector de provincia filtra hemiciclo + abre panel provincial.
- [x] Pills de voto (Todos / A favor / En contra / Abstención / Ausentes) atenúan las bancas no coincidentes manteniendo el color del modo activo.
- [x] Botón "Limpiar filtros" restituye estado base.
- [x] Cambio de escenario limpia filtros automáticamente.
- [x] Filtros se reflejan en la tabla nominal y en el conteo "(filtrados)".

## 5. Ficha institucional (meta-bar)

- [x] Línea resumen visible: Ley · Cámara · Fecha · Resultado · tally.
- [x] Toggle "Ver ficha institucional ▾" expande las 3 bandas (header / trámite / fuentes).
- [x] Ningún campo institucional queda vacío. Fallbacks aplicados:
  - "No consignado" para campos faltantes.
  - "No corresponde · distrito en acta nominal" para `districtSource` en Diputados.
  - "Sin dato en fuente" para campos sin fuente conocida.

## 6. Panel de validación

- [x] Estado **verde "Datos validados"** en los 2 escenarios de Diputados.
- [x] Estado **verde "Datos validados con doble fuente"** en los 2 escenarios de Senado.
- [x] No aparece estado amarillo ni rojo.

## 7. Tablas en pantalla

Sub-tab **Tablas**, 4 cards visualmente distintas pero con misma estética editorial:

- [x] **Ficha institucional** — grid K/V en 5 secciones (Identificación · Trámite · Conteos · Fuentes · Validación) con tick dorado por sección.
- [x] **Tabla nominal** — buscador interno, contador "X de 257 (filtrados)", bloque en italic muted con ellipsis, familia con dot circular, voto como badge.
- [x] **Resumen territorial por provincia** — mini-barras de composición visibles, voto y familia predominante como badges/chips.
- [x] **Resumen por familia política** — chip familia + sub-línea pequeña con bloques incluidos, mini-barras, % afirmativo.

Cada card lleva botones de exportación CSV + PNG en su encabezado.

## 8. Exportación PNG con contexto académico

Probar en los 4 escenarios × 2 modos = 8 PNG por figura.

### Hemiciclo y mapa
- [x] PNG de hemiciclo en modo voto: eyebrow, título, bajada, meta-bar, hemiciclo centrado, leyenda cromática con conteos, lectura, fuente, credit. Filename `<escenario>_hemiciclo_contexto_voto.png`.
- [x] PNG de hemiciclo en modo familia: mismo wrapper con leyenda de familias. Filename `<escenario>_hemiciclo_contexto_familia.png`.
- [x] PNG de mapa en modo voto: incluye nota cartográfica de Malvinas al pie. Filename `<escenario>_mapa_contexto_voto.png`.
- [x] PNG de mapa en modo familia. Filename `<escenario>_mapa_contexto_familia.png`.

### Solo gráfico (opción secundaria)
- [x] PNG hemiciclo simple: `<escenario>_hemiciclo_<voto|familia>.png`.
- [x] PNG mapa simple: `<escenario>_mapa_<voto|familia>.png`.

### Tablas como figura
- [x] PNG tabla nominal: `<escenario>_tabla_legisladores.png` (con filas filtradas; confirm si > 100 sin filtros).
- [x] PNG resumen provincial: `<escenario>_tabla_provincias.png`.
- [x] PNG resumen por familia: `<escenario>_tabla_familias.png`.
- [x] PNG resumen institucional (ficha): `<escenario>_resumen_institucional.png`.

## 9. Exportación CSV

Probar en los 4 escenarios.

- [x] **Legisladores** — `<escenario>_tabla_legisladores.csv`. 11 columnas. Voto en español formal. Fuente del voto y Fuente territorial en cada fila.
- [x] **Provincias** — `<escenario>_tabla_provincias.csv`. 17 columnas. Porcentajes 1-decimal sobre total del distrito.
- [x] **Familias** — `<escenario>_tabla_familias.csv`. 17 columnas. Provincias y bloques como listas separadas por `; ` ordenadas alfabéticamente (es-ES).
- [x] **Resumen institucional** — `<escenario>_resumen_institucional.csv`. 20 columnas. Una fila. Sin campos vacíos.
- [x] **Comparativo general** — `comparativo_votaciones_resumen.csv`. 19 columnas. 4 filas (uno por escenario). Margen, % afirmativo y % negativo sobre presentes, estado de validación.

Todos en UTF-8 con BOM (acentos correctos en Excel y Numbers).

## 10. Trazabilidad documental

- [x] Cada PNG con contexto incluye Fuente del voto + Fuente territorial al pie.
- [x] Cada CSV incluye Fuente del voto + Fuente territorial por fila.
- [x] Cada figura PNG incluye crédito "ELABORACIÓN PROPIA A PARTIR DE FUENTES LEGISLATIVAS OFICIALES."
- [x] `docs/metodologia_fuentes.md` describe el cruce.
- [x] `docs/validacion_datos.md` documenta los criterios.

## 11. Documentación

- [x] `README.md` refleja el estado v1.0.
- [x] `CHANGELOG.md` con entradas para Etapas 1-5.
- [x] `docs/uso_herramienta.md` describe los 4 escenarios, los 2 modos, los filtros, las 4 tablas y las exportaciones PNG + CSV.
- [x] `docs/metodologia_fuentes.md` sin tocar (es la fuente metodológica).
- [x] `docs/validacion_datos.md` sin tocar.

## 12. Backups

- [x] Backups por reconstrucción de dataset preservados en `backups/pre_*`.
- [x] Backups por etapa de pulido editorial preservados en `backups/versiones_anteriores/`.
- [x] Ningún backup eliminado ni sobreescrito.

## 13. Versión

- [x] Comentario `Versión: v1.0` en el head de `index.html`.
- [x] `<meta name="version" content="1.0">` en el head.
- [x] Versión declarada en `README.md`.
- [x] Versión declarada en `CHANGELOG.md` (entrada del 2026-05-19).

## 14. Cierre

Esta herramienta queda apta para:

1. **Tesis impresa.** PNG con contexto académico, 2,5× de resolución, sin recortes.
2. **Defensa oral.** Hemiciclo y mapa con resultado claro en pantalla, ficha institucional compacta, modo voto/familia para preguntas sobre coaliciones.
3. **Anexo metodológico.** CSV con columnas formales, fuentes documentales por fila, comparativo entre los 4 escenarios.
4. **Reproducibilidad.** HTML autocontenido + datasets validados + trazabilidad por escenario + documentación metodológica.

Próximas etapas eventuales (post-tesis): responsive mobile, más coaliciones, integración con bases sectoriales. Fuera del scope de v1.0.
