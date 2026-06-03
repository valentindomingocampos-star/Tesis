# Plan de rediseño: Bifurcación Análisis de Caso / Análisis Comparado

## Contexto del proyecto

**Archivo:** `herramienta_votaciones/index.html` (~11.300 líneas, vanilla JS, single-file HTML autocontenido)
**Propósito:** Visualización legislativa de las votaciones nominales de Aerolíneas (Ley 26.466, 2008) e YPF (Ley 26.741, 2012) en ambas cámaras del Congreso argentino.
**Arquitectura:** Sin frameworks, sin dependencias. SVG para hemiciclo y mapa. CSS custom properties. Estado global (`state`). Todo embebido.
**Restricción:** Debe mantenerse como HTML autocontenido (single-file).

---

## Auditoría del estado actual

### Lo que funciona bien (no tocar salvo mejoras puntuales)

- **Hemiciclo:** `buildHemicycleLayout()` (líneas ~3944–4084) + `renderHemicycle()` (~4631–4840). Radial layout con accesibilidad completa (keyboard nav, aria-labels).
- **Mapa:** `renderMap()` (~4841–5026). SVG choropleth con 24 provincias + CABA + Malvinas.
- **Filtros:** Búsqueda + provincia + voto combinables. Estado en `state.search`, `state.provinceFilter`, `state.voteFilter`.
- **Sidebar:** Detalle legislador, detalle provincia, resumen nacional por familia.
- **Indicadores (División A):** 7 bloques politológicos en `renderScenarioIndicators()` (~5311–8170): resultado, ausentismo, cohesión familia, cohesión bloque, territorialización, Rice, Rae.
- **Export PNG:** `exportSvgAsPng()` (~6008+) con wrapper académico. Canvas 2.5× scaling.
- **Export CSV:** 7 tipos de CSV con BOM UTF-8.

### Lo que tiene el Comparado actual (División B)

Función `renderComparisonPanel()` (líneas ~8546–8855):
- B.1: Tabla comparativa agregada (4 columnas = 4 escenarios)
- B.2: Deltas entre pares (YPF−Aero por cámara, Senado−Dip por caso)
- B.3: Rankings cualitativos (mayor apoyo, rechazo, ausentismo, margen)
- B.4: Heatmap familias × escenarios (3 métricas: %Afi, Rice, %Aus)
- B.5: Heatmap provincias × escenarios (voto dominante + patrón cross-escenario)
- B.6: Rae comparado (fragmentación)

**Diagnóstico:** Todo es tabular/numérico. No hay ninguna visualización gráfica comparativa (hemiciclos lado a lado, mapas lado a lado, flujos de trayectoria). Esto es lo que hay que construir.

---

## Especificación de features nuevas

### FEATURE 1: Selector de par de escenarios

**Ubicación:** Inicio de la sub-tab "Análisis comparado" (`<div class="subpanel" data-subtab="compare">`)

**Comportamiento:**
- Agregar al `state` una propiedad `comparePair`: array de 2 scenario IDs.
- Default: `['aerolineas_diputados', 'ypf_diputados']` (misma cámara, distintos casos).
- UI: Grupo de botones preset:
  - "Diputados: Aero vs YPF" → `['aerolineas_diputados', 'ypf_diputados']`
  - "Senado: Aero vs YPF" → `['aerolineas_senado', 'ypf_senado']`
  - "Aerolíneas: Dip vs Sen" → `['aerolineas_diputados', 'aerolineas_senado']`
  - "YPF: Dip vs Sen" → `['ypf_diputados', 'ypf_senado']`
- Opcional: selector libre con 2 dropdowns para elegir cualquier par.
- Cambiar el par dispara re-render de todas las visualizaciones comparativas.

**State change:**
```javascript
// Agregar a state:
comparePair: ['aerolineas_diputados', 'ypf_diputados'],
```

---

### FEATURE 2: Hemiciclos lado a lado

**Qué:** Renderizar 2 hemiciclos SVG uno al lado del otro, correspondientes al par seleccionado.

**Implementación:**
- Crear función `renderDualHemicycle(scenarioIdA, scenarioIdB)`.
- Reutilizar `buildHemicycleLayout()` para cada escenario.
- Contenedor flex/grid con 2 SVGs. Cada uno con su etiqueta de escenario arriba (caso · cámara · fecha · resultado).
- Ambos comparten la misma leyenda de colores debajo.
- El `colorMode` (voto/familia) del comparado puede ser independiente o sincronizado con el del caso. Recomiendo independiente con su propio toggle.
- Agregar al `state`:
```javascript
compareColorMode: 'vote', // 'vote' | 'family'
```

**Layout CSS:**
```css
.dual-viz {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: var(--space-4);
}
/* Responsive: stack en <1100px */
@media (max-width: 1100px) {
  .dual-viz { grid-template-columns: 1fr; }
}
```

**Cada hemiciclo debe:**
- Ser más compacto que el del análisis de caso (viewBox reducido, sin centro tan grande).
- Mantener hover/tooltip pero NO click-to-select (no hay sidebar en comparado).
- Tener un header editorial: `<div class="dual-viz-label">Aerolíneas · Diputados · 3/12/2008 · AFIRMATIVO</div>`.

**Export PNG:**
- Función `exportDualHemicyclePng()` que renderiza ambos hemiciclos en un solo canvas.
- Con wrapper académico: título "Hemiciclos comparados", leyenda compartida, metadata de ambos escenarios, fuentes.
- Canvas width: 2400px (1200 cada hemiciclo) para calidad print.

---

### FEATURE 3: Mapas lado a lado

**Qué:** 2 mapas SVG del mismo par, mostrando voto dominante por provincia.

**Implementación:**
- Función `renderDualMap(scenarioIdA, scenarioIdB)`.
- Reutilizar la lógica de `renderMap()` pero parametrizada por scenario.
- Misma estructura grid que los hemiciclos.
- **Feature diferencial:** En el mapa del segundo escenario (o en un tercer mapa "diff"), resaltar con borde grueso (stroke-width: 3, stroke: var(--accent-gold)) las provincias que cambiaron de voto dominante entre un escenario y el otro.

**Variante "mapa de diferencias":**
- Opcional: tercer mapa centrado debajo que muestre SOLO las diferencias.
- Provincias que cambiaron: coloreadas (verde→rojo o viceversa con flecha).
- Provincias que no cambiaron: gris neutro.
- Esto es muy potente para la tesis.

**Export PNG:**
- `exportDualMapPng()` — ambos mapas + wrapper académico.
- Si se implementa el mapa diff, incluirlo como tercer panel.

---

### FEATURE 4: Trayectoria de legisladores repetidos (★ feature estrella)

**Qué:** Identificar legisladores que aparecen en ambos escenarios del par seleccionado y analizar cómo cambiaron.

**Lógica de match:**
- Match por `name` (string exact match o fuzzy si hay variaciones menores).
- Solo tiene sentido entre escenarios de la **misma cámara** (Dip Aero → Dip YPF, Sen Aero → Sen YPF), porque un legislador no puede estar en ambas cámaras simultáneamente. Pero sí podría haber pasado de Diputados a Senado entre 2008 y 2012 — eso es un match cross-cámara interesante pero secundario.
- Implementar primero same-chamber, opcionalmente cross-chamber después.

**Función principal:**
```javascript
function computeTrajectory(scenarioIdA, scenarioIdB) {
  const legsA = getLegsForScenario(scenarioIdA);
  const legsB = getLegsForScenario(scenarioIdB);
  
  // Normalizar nombre para match (trim, lowercase, quitar acentos)
  const normalize = (name) => name.trim().toLowerCase()
    .normalize('NFD').replace(/[̀-ͯ]/g, '');
  
  const mapB = new Map();
  legsB.forEach(leg => mapB.set(normalize(leg.name), leg));
  
  const matched = [];
  const onlyA = [];
  
  legsA.forEach(legA => {
    const key = normalize(legA.name);
    if (mapB.has(key)) {
      const legB = mapB.get(key);
      matched.push({
        name: legA.name,
        provinceA: legA.province, provinceB: legB.province,
        voteA: legA.vote, voteB: legB.vote,
        familyA: legA.family, familyB: legB.family,
        blockA: legA.block, blockB: legB.block,
        sameVote: legA.vote === legB.vote,
        sameFamily: legA.family === legB.family,
        sameBlock: legA.block === legB.block,
      });
      mapB.delete(key);
    } else {
      onlyA.push(legA);
    }
  });
  
  const onlyB = Array.from(mapB.values());
  
  // Métricas
  const totalA = legsA.length;
  const totalB = legsB.length;
  const nMatched = matched.length;
  const pctPermanencia = (nMatched / totalA * 100);
  const nSameVote = matched.filter(m => m.sameVote).length;
  const pctConsistencia = nMatched > 0 ? (nSameVote / nMatched * 100) : 0;
  
  // Flujos de migración de voto
  const flows = {};
  matched.forEach(m => {
    const key = `${m.voteA}→${m.voteB}`;
    flows[key] = (flows[key] || 0) + 1;
  });
  
  return {
    matched, onlyA, onlyB,
    metrics: { totalA, totalB, nMatched, pctPermanencia, nSameVote, pctConsistencia },
    flows
  };
}
```

**Visualización 4a: Tabla de legisladores repetidos**
- Tabla con columnas: Nombre | Provincia | Voto (escenario A) | Voto (escenario B) | Familia A | Familia B | Estado
- "Estado" = badge: "Mantuvo voto" (verde), "Cambió voto" (rojo), "Cambió familia" (ocre)
- Sortable por cualquier columna.
- Searchable.
- Filtrable: "Solo los que cambiaron voto" / "Solo los que mantuvieron" / "Todos".

**Visualización 4b: Diagrama de flujo (Sankey simplificado)**
- SVG que muestra los flujos de voto entre escenarios.
- Lado izquierdo: bloques por voto en escenario A (AFIRMATIVO, NEGATIVO, AUSENTE, ABSTENCIÓN) con tamaño proporcional.
- Lado derecho: bloques por voto en escenario B.
- Bandas curvas (paths con curva bezier) conectando cada flujo, con grosor proporcional al n° de legisladores.
- Colores: verde para los que mantuvieron afirmativo, rojo para los que mantuvieron negativo, dorado/ocre para los que cambiaron.
- Labels en cada banda: "n legisladores (x%)".

**Implementación del Sankey:**
```javascript
function renderTrajectoryFlow(trajectory) {
  // trajectory = output de computeTrajectory()
  const flows = trajectory.flows;
  const matched = trajectory.matched;
  
  // Categorías de voto ordenadas
  const cats = ['AFIRMATIVO', 'NEGATIVO', 'ABSTENCION', 'AUSENTE'];
  
  // Contar por categoría en cada lado
  const leftCounts = {};
  const rightCounts = {};
  cats.forEach(c => { leftCounts[c] = 0; rightCounts[c] = 0; });
  matched.forEach(m => {
    leftCounts[m.voteA] = (leftCounts[m.voteA] || 0) + 1;
    rightCounts[m.voteB] = (rightCounts[m.voteB] || 0) + 1;
  });
  
  // Construir SVG con barras + paths bezier
  // ViewBox: 800 x 400
  // Barras izquierda en x=0..80, barras derecha en x=720..800
  // Paths con control points para curvas suaves
  // ... (implementar el SVG path generator)
}
```

**Visualización 4c: Métricas resumen**
- Card con indicadores clave:
  - "N legisladores repitieron banca" (de X en escenario A)
  - "Tasa de permanencia: X%"
  - "Tasa de consistencia de voto: X%"
  - "N cambiaron de sentido del voto"
  - "N cambiaron de familia política"

**Export PNG:**
- `exportTrajectoryTablePng()` — tabla nominal con badges.
- `exportTrajectoryFlowPng()` — diagrama Sankey con wrapper académico.
- `exportTrajectoryMetricsPng()` — card de métricas resumen.

---

### FEATURE 5: Grilla 2×2 (los 4 escenarios)

**Qué:** Vista panorámica con 4 hemiciclos o 4 mapas en un grid 2×2.

**Layout:**
```
┌──────────────────┬──────────────────┐
│ Aero · Diputados │ YPF · Diputados  │
├──────────────────┼──────────────────┤
│ Aero · Senado    │ YPF · Senado     │
└──────────────────┴──────────────────┘
```

**Implementación:**
- Función `renderQuadView(viewType)` donde `viewType` = 'hemicycle' | 'map'.
- Grid CSS 2×2. Cada celda tiene header editorial + mini SVG.
- Hemiciclos/mapas más pequeños (viewport reducido), sin interactividad (solo lectura).
- Toggle para alternar entre hemiciclos y mapas.
- Toggle para colorMode (voto/familia) compartido por los 4.

**Export PNG:**
- `exportQuadPng()` — los 4 en un canvas de 2400×1600 con wrapper académico completo.
- Título: "Las cuatro votaciones nominales" + leyenda + metadata + fuentes.

---

### FEATURE 6: Tabla de migración de voto por familia política

**Qué:** Tabla que muestra, para cada familia, cómo votó en el escenario A vs el escenario B (del par seleccionado), calculada solo sobre los legisladores que repitieron.

**Columnas:**
- Familia | #Repetidos | %Afi en A | %Afi en B | Δ%Afi | Cohesión A | Cohesión B | ΔCohesión

**Implementación:**
- Usar el output de `computeTrajectory()` agrupado por familia.
- Solo familias con ≥2 legisladores repetidos (umbral de relevancia).
- Badges: "↑ más apoyo", "↓ menos apoyo", "= estable".

---

## Orden de implementación sugerido

### Fase 1: Infraestructura (hacer primero)
1. Agregar `comparePair` y `compareColorMode` al `state`.
2. Crear el selector de par con los 4 presets + render en la sub-tab comparado.
3. Crear contenedor genérico `.dual-viz` y `.quad-viz` con CSS responsive.
4. Crear función `getLegsForScenario(id)` centralizada (evitar duplicación).

### Fase 2: Visualizaciones comparativas
5. `renderDualHemicycle()` — hemiciclos lado a lado.
6. `renderDualMap()` — mapas lado a lado (con highlight de provincias que cambiaron).
7. `renderQuadView()` — grilla 2×2.

### Fase 3: Trayectorias
8. `computeTrajectory()` — lógica de match + métricas.
9. Tabla de legisladores repetidos (con filtros y badges).
10. Diagrama de flujo Sankey.
11. Tabla de migración por familia.
12. Card de métricas resumen de trayectoria.

### Fase 4: Exports PNG
13. `exportDualHemicyclePng()` con wrapper académico.
14. `exportDualMapPng()` con wrapper académico.
15. `exportQuadPng()` con wrapper académico.
16. `exportTrajectoryTablePng()`.
17. `exportTrajectoryFlowPng()`.
18. `exportTrajectoryMetricsPng()`.
19. `exportFamilyMigrationPng()`.

### Fase 5: Integración y pulido
20. Reorganizar la sub-tab comparado con secciones claras (B.7 en adelante para las nuevas).
21. Agregar navegación interna (índice de saltos rápidos, como ya tiene División A).
22. Responsive testing.
23. Verificar que no se rompió nada del análisis de caso.

---

## Consideraciones técnicas

### Rendimiento
- Los 4 escenarios suman 638 legisladores (257+72+257+72). No hay problema de performance.
- El Sankey opera sobre el subset de matched (~50-100 legisladores estimados). Trivial.

### Tamaño del archivo
- El HTML actual tiene ~600KB. Las features nuevas agregarán ~200-300 líneas de CSS y ~1500-2000 líneas de JS. Total estimado: ~700KB. Sigue siendo manejable como single-file.

### Principio de diseño vigente
- **Voto y partido nunca compiten en la misma marca.** Respetar en todas las visualizaciones nuevas. Cada hemiciclo/mapa muestra voto O familia, nunca ambos.

### Accesibilidad
- Mantener `aria-labels` en todas las visualizaciones nuevas.
- Los hemiciclos del comparado no necesitan keyboard nav completa (son de lectura), pero sí tooltips y aria.

### Convención de nombres de export
- Seguir el patrón existente: `{caso}_{camara}_{tipo}_{contexto}_{modo}.png`
- Para comparativos: `comparativo_{casoA}_vs_{casoB}_{tipo}_{contexto}.png`
- Ejemplo: `comparativo_aerolineas_dip_vs_ypf_dip_hemiciclo_contexto_voto.png`

---

## Estructura de la sub-tab Análisis Comparado (resultado final)

```
Análisis Comparado
├── Selector de par de escenarios (4 presets + toggle voto/familia)
├── B.7  Hemiciclos comparados (lado a lado)
│        └── Export PNG
├── B.8  Mapas comparados (lado a lado + diff)
│        └── Export PNG
├── B.9  Grilla panorámica 2×2
│        └── Export PNG
├── B.10 Trayectoria de legisladores
│        ├── Métricas resumen (permanencia, consistencia)
│        ├── Diagrama de flujo (Sankey)
│        ├── Tabla nominal con badges
│        ├── Tabla migración por familia
│        └── Exports PNG (flujo, tabla, métricas)
├── B.1  Tabla comparativa agregada [ya existe]
├── B.2  Diferencias entre pares [ya existe]
├── B.3  Rankings [ya existe]
├── B.4  Heatmap familias [ya existe]
├── B.5  Heatmap provincias [ya existe]
└── B.6  Rae comparado [ya existe]
```

---

## Preguntas abiertas (decidir antes de implementar)

1. **Match de nombres:** ¿Los nombres en los 4 datasets están escritos exactamente igual? Si hay variaciones (tildes, orden apellido-nombre), el match necesita fuzzy matching. Verificar comparando los arrays de legisladores.

2. **Cross-cámara:** ¿Incluir match de legisladores que pasaron de Diputados a Senado entre 2008 y 2012? Es políticamente interesante pero secundario.

3. **Mapa diff:** ¿Implementar el tercer mapa de diferencias (solo provincias que cambiaron) o alcanza con resaltar las que cambiaron en el segundo mapa?

4. **Grilla 2×2:** ¿Siempre los 4 escenarios fijos, o permitir seleccionar cuáles van en cada celda?
