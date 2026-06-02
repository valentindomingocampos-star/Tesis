# Checklist de revisión final — post-auditoría (v1.0)

Documento de verificación tras el roadmap completo recomendado por la auditoría post-Etapa 2. Fecha de cierre: **2026-05-19**.

## 1. Etapa A — fixes técnicos prioritarios

- [x] **A1** · `validateScenario` cachea en `scenario._validation`. Resultados idénticos entre llamadas sucesivas.
- [x] **A2** · `renderNationalPanel` incluye abstenciones en barra y tally; usa `computeVoteSummary`. Sin NaN ni segmentos faltantes.
- [x] **A3** · `setTimeout(60)` reemplazado por `afterRender()` (doble `requestAnimationFrame`). Exports hemiciclo/mapa desde otra vista funcionan sin race condition.
- [x] **A4** · Separación visual `.legend` ↔ `.viz-summary` (regla inset suave).
- [x] **A5** · Badges de voto en tablas: `font-size: 11.5px`, `padding: 3px 12px`.
- [x] **A6** · ViewBox del mapa **no modificado** (decisión documentada: riesgo alto, queda para v1.1).

## 2. Etapa B — robustez y accesibilidad

- [x] **B1** · Heatmaps con `role="table"/"row"/"columnheader"/"cell"` + `aria-label` por celda.
- [x] **B2** · `CASE_DESCRIPTIONS` parametrizado, `renderFooter` sin hardcode.
- [x] **B3** · Parseo robusto con regex: `/^stats-.+-csv$/` y `/^stats-.+-png$/`.
- [x] **B4** · Print stylesheet `@media print` aplicado. Ctrl+P → vista limpia.

## 3. Etapa C — indicadores Rice y Rae

### C1 · Rice por familia y bloque
- [x] `computeFamilyRice(scenario)` y `computeBlockRice(scenario)` definidos.
- [x] Fórmula: `|Afi − Neg| / (Afi + Neg)`.
- [x] Excluye ausentes, presidencia y abstenciones.
- [x] Devuelve `"No calculable"` cuando `Afi + Neg = 0`.
- [x] Visible en A.6 (top 5 familia + top 5 bloque).

### C2 · Rae por escenario
- [x] `computeScenarioRae(scenario)` definido.
- [x] Fórmula: `1 − Σ(pᵢ²)` sobre afirmativo, negativo, abstención.
- [x] Excluye ausentes y presidencia.
- [x] Visible en A.6 (KPI) y en B.6 (ranking comparado entre los 4).
- [x] Interpretación textual incluida: "concentrado / moderado / fragmentado / altamente fragmentado".

### C3 · Integración visual
- [x] Sub-sección A.6 con KPI Rae + Rice top 5 por familia y bloque, sin saturar.
- [x] Sub-sección B.6 con ranking comparado de Rae.
- [x] Alineamiento renumera a A.7 (sigue como nota metodológica).
- [x] Nota inline explicando ambas fórmulas.

### C4 · Exportaciones
- [x] `exportStatsCsv` incluye filas Rice familia, Rice bloque y Rae escenario.
- [x] `exportComparatorCsv` incluye columnas Rae y base de cálculo.
- [x] Nuevo CSV/PNG granular `stats-rice-rae-csv/png` (escenario activo).
- [x] Nuevo CSV/PNG granular `stats-comp-rae-csv/png` (comparador).
- [x] `"No calculable"` impreso correctamente cuando corresponde.

## 4. Etapa D — documentación

- [x] `README.md` actualizado con módulo estadístico e índices.
- [x] `CHANGELOG.md` con entrada del 2026-05-19 cubriendo las 4 etapas.
- [x] `docs/uso_herramienta.md` con sub-tab Estadísticas declarada.
- [x] `docs/checklist_v1.md` preservado.
- [x] `docs/checklist_revision_final.md` (este documento).

## 5. Decisiones metodológicas finales

| Indicador | Denominador | Excluye | Caso "no calculable" |
|---|---|---|---|
| **Cohesión simple** (A.3/A.4) | presentes | ausentes | sin presentes |
| **Rice** | Afi + Neg | ausentes, presidencia, abstenciones | Afi + Neg = 0 |
| **Rae** | Afi + Neg + Abst (votos emitidos) | ausentes, presidencia | sin votos emitidos |
| **Porcentaje de voto** | presentes | — | sin presentes |
| **Tasa de ausencia** | total de bancas del escenario | — | nunca (denom siempre > 0) |
| **% Apoyo sobre cuerpo** | total de bancas | — | nunca |
| **Cohesión territorial** (por provincia) | total representantes provincia | — | provincia con 0 reps |
| **Alineamiento oficialismo/oposición** | — | — | siempre (requiere variable adicional, no incluida) |

## 6. Validación técnica final

- [x] `node --check` OK sobre JS extraído.
- [x] Datasets intactos: 257 / 72 / 257 / 72.
- [x] `validateScenario` OK en los 4 escenarios (verde "Datos validados" en Diputados, verde "doble fuente" en Senado).
- [x] Visualización funciona (hemiciclo + mapa + heatmap territorial).
- [x] Estadísticas funcionan en los 4 escenarios.
- [x] Tablas funcionan (4 tarjetas distintas).
- [x] Exportaciones PNG: hemiciclo · mapa · 4 tablas · 10 bloques estadísticos · 2 índices nuevos (Rice/Rae) · comparador Rae.
- [x] Exportaciones CSV: legisladores · provincias · familias · resumen institucional · comparativo · indicadores · comparador completo (con Rae).
- [x] Filtros funcionan en tabla nominal; descargas filtradas respetan estado visible.
- [x] Sin NaN ni undefined en celdas; fallbacks `"No consignado"` / `"No calculable"` activos.
- [x] HTML autocontenido (sin recursos externos, favicon SVG inline).

## 6 bis. Estadísticas politológicas avanzadas (iteración 2026-05-19)

### Síntesis interpretativa automática
- [x] `buildScenarioInsights(scenario)` genera 4-6 oraciones descriptivas (resultado · participación · cohesión · territorialización · fragmentación · disciplina).
- [x] `buildComparisonInsights()` genera 4-6 oraciones de comparación cross-escenario.
- [x] Bloque `A.0` y `B.0` con `insights-block` editorial al inicio de cada división.
- [x] Las oraciones derivan estrictamente de indicadores calculados; nunca formulan causalidad.

### Δ cohesión promedio por familia
- [x] `computeAvgFamilyCohesionDelta(A, B)` calcula la diferencia entre la cohesión promedio de familias entre dos escenarios.
- [x] Integrado a los 4 diff blocks de B.2.
- [x] Verde si la cohesión sube de A a B, burdeos si baja.

### Selector de métrica en heatmap B.4
- [x] Toggle entre `% Afirmativo (presentes)`, `Cohesión Rice` y `% Ausentismo (total)`.
- [x] Gradiente cromático adaptado a la métrica.
- [x] `state.famHeatMetric` persiste durante la sesión.
- [x] aria-label en celdas refleja la métrica activa.

### Exports granulares nuevos
- [x] `stats-rice-fam-csv/png` — Rice por familia política
- [x] `stats-rice-blk-csv/png` — Rice por bloque
- [x] `stats-rae-scen-csv/png` — detalle Rae escenario
- [x] `stats-comp-rae-csv/png` — Rae comparado (ya existía)
- [x] `stats-fam-comp-wide-csv/png` — wide familias × escenarios
- [x] `stats-prov-comp-wide-csv/png` — wide territorial × escenarios

### Tono académico
- [x] Bajadas y eyebrows con vocabulario de Ciencia Política y RR.II.
- [x] Insights nunca afirman causalidad; describen y comparan contra umbrales.

## 7. Estado de versión

**v1.0 — versión estable para tesis** · `Date.parse('2026-05-19')`.

Próximas etapas potenciales (post-defensa, fuera del scope v1.0):
- v1.1: layout horizontal mapa + sidebar adjunto (resolución del item A6).
- v1.1: incorporar variable `alignment` por legislador para activar el indicador A.7.
- v1.2: incorporación de nuevas votaciones (ej. CEAMSE, YCRT) bajo el mismo esquema.
