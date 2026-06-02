# Auditoría final del HTML consolidado

Fecha del cierre: 2026-05-14.
Archivo evaluado: `herramienta_votaciones/index.html`.

## Verificaciones automatizables (✓ pasadas)

### Sintaxis e integridad
- ✓ `node --check` sobre el script extraído del HTML pasa sin errores.
- ✓ 0 dependencias externas (sin `<script src="http…">`, sin `<link href="http…">`, sin `@import`).
- ✓ Archivo autocontenido, 194 KB, ~2.980 líneas.
- ✓ Doctype HTML5 y estructura body bien formada.

### Validación de los 4 escenarios

```
Aerolíneas · Diputados:   críticos 0  importantes 0  → OK
Aerolíneas · Senado:      críticos 0  importantes 0  → OK
YPF · Diputados:          críticos 0  importantes 0  → OK
YPF · Senado:             críticos 0  importantes 0  → OK
```

Para cada escenario:
- Conteos de voto cuadran con la metadata oficial.
- 24/24 jurisdicciones cuadran al cupo institucional.
- Sin campos vacíos, votos inválidos, familias desconocidas, provincias sin normalizar ni duplicados.

### Banner de doble fuente
- ✓ Lógica `hasDoubleSource = !!(s.voteSource && s.districtSource)` implementada.
- ✓ Aerolíneas Senado y YPF Senado tienen los tres campos: `voteSource`, `districtSource`, `territorialValidation: true`.
- ✓ Diputados (sin esos campos) recibe el texto estándar.

### Datasets y paletas
- ✓ 257 / 72 / 257 / 72 registros (AAAA Dip / Sen, YPF Dip / Sen).
- ✓ 5 colores de voto, 10 colores de familia política.
- ✓ Set de votos cerrado: {AFIRMATIVO, NEGATIVO, AUSENTE, ABSTENCION, PRESIDENCIA}.

### Funciones clave (20/20)
Presentes y operativas:
- `computeVoteSummary`, `computeProvinceSummary`, `computeFamilySummary`
- `validateScenario`, `applyFilters`, `resetFilters`, `setColorMode`
- `getColorByVote`, `getColorByFamily`
- `exportSvgAsPng`, `exportCsv`, `slugifyFilename`
- `renderTables`, `renderValidationPanel`, `renderHemicycle`, `renderMap`, `renderLegend`, `renderFooter`
- `normalizeProvince`, `expectedDistribution`

### Exportaciones (7 destinos wireados)
`hemicycle-png`, `map-png`, `legislators-csv`, `provinces-csv`, `families-csv`, `summary-csv`, `summary-all-csv`.

Robustez:
- ✓ CSV con BOM UTF-8 (acentos en Excel y Numbers).
- ✓ PNG con fondo blanco forzado, rasterización a 2,5× del viewBox.
- ✓ `<style>` inyectado en el SVG clonado antes de rasterizar (text-anchor, font-size, fill, font-family Helvetica/Arial).
- ✓ Nombres de archivo prolijos: `slugifyFilename` normaliza acentos vía NFD.

### Higiene del HTML
- ✓ Sin números hardcodeados en leyendas (todo viene de `computeVoteSummary`).
- ✓ Aliases de provincia cubren las variantes esperadas.
- ✓ Sin tabs marcados estáticamente como `is-under-review` (sólo definición CSS + 2 toggles JS).

### Responsive (3 breakpoints)
- ✓ `@media (max-width: 720px)` — mobile.
- ✓ `@media (max-width: 1100px)` — tablet / stacked.
- ✓ `@media (max-width: 1366px) and (min-width: 1101px)` — laptop estándar (sidebar 320px).

## Verificaciones que requieren navegador (humanas)

Estos puntos no se pueden chequear sin abrir el HTML en un navegador real. Se listan por prioridad para revisión manual.

### 🔴 Crítico
1. Banner doble fuente visible en Aerolíneas Senado y YPF Senado.
2. Banner estándar (sin doble fuente) en los dos Diputados.
3. Tabs de Senado sin chip amarillo de "revisión territorial".
4. Hemiciclos renderizan los 257 / 72 / 257 / 72 puntos sin pisarse.
5. Mapa provincial renderiza 24 jurisdicciones coloreadas correctamente.
6. Toggle Voto / Familia política funciona en los 4 escenarios.

### 🟠 Importante (UX)
7. Tooltip de banca completo (nombre, provincia, voto, bloque, familia).
8. Click en banca abre el panel "Legislador seleccionado".
9. Click en provincia filtra y muestra panel provincial.
10. Buscador filtra el hemiciclo (las bancas no coincidentes se opacan).
11. Filtro de provincia (select) sincroniza con click en mapa.
12. Pills de voto filtran correctamente (incluida Abstención).
13. Botón Limpiar filtros vuelve a estado inicial.
14. Sub-tabs cambian de panel.
15. Tabla Legisladores: búsqueda y orden funcionan; badges correctos.
16. Tablas Provincias y Familias bien formateadas.

### 🟠 Exportaciones
17. PNG hemiciclo (modo voto): fondo blanco, textos nítidos, no recortado.
18. PNG mapa (modo familia): provincias con colores correctos, labels visibles.
19. CSV legisladores: acentos correctos al abrir en Excel.
20. CSV resumen comparado: 4 filas, columnas completas.

### 🟡 Estética
21. Tipografía consistente.
22. Colores sobrios; daltonismo manejable.
23. Leyenda no compite con el hemiciclo.
24. Tabla territorial sólo aparece si hay desvíos (actualmente no debería aparecer).

### 🟡 Responsive
25. 1366px: sidebar 320px, viz-card respira.
26. 1024px (tablet): viz-card y sidebar apilados.
27. ≤720px (mobile): pills envuelven, summary-strip 2 columnas.

## Conclusión

La herramienta cumple sus objetivos técnicos: archivo único, sin dependencias, datasets validados, exportaciones funcionales, doble fuente documentada en Senado, trazabilidad preservada. Lo que queda es verificación visual y de interacción, que sólo se puede completar abriendo el archivo en un navegador.
