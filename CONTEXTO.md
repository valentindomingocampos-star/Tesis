# Contexto del proyecto — "El péndulo del poder"

> Briefing para arrancar una sesión nueva de Claude Code (o cualquier asistente) con todo el contexto del proyecto: la tesis de grado y su herramienta de visualización de votaciones. Verificado contra el código al 2026-06-04.

## 0. Cómo trabajar conmigo

- Soy estudiante de grado de Ciencia Política y RR.II. (UBA). Tesis de economía política comparada.
- Escribime en **español argentino**, registro académico, denso, analítico y conciso. Vocabulario técnico de economía política (coaliciones, autonomía estatal, valorización financiera, disciplina de bloque, territorialización del voto, fragmentación legislativa).
- **Nada de lecturas normativas** (privatizar = malo / reestatizar = bueno). El enfoque es actores, ideas, instituciones y coaliciones. No tratar "el Estado" como bloque homogéneo: distinguir Ejecutivo, Legislativo, bloques, sindicatos, provincias, empresas, organismos internacionales.

## 1. La tesis

- **Título:** *"El péndulo del poder: disputas de propiedad, soberanía y desarrollo en la República Argentina"*.
- **Pregunta:** cómo y por qué el Estado argentino redefinió su vínculo con empresas estratégicas en dos momentos opuestos (privatización de los 90 / reestatización de los 2000), y qué papel jugaron las coaliciones políticas, la autonomía estatal y los proyectos de desarrollo.
- **Hipótesis:** privatizaciones y reestatizaciones no fueron decisiones administrativas aisladas sino expresiones de cambios en las coaliciones de poder y en la concepción del rol del Estado. Los 90 = coalición reformista pro-mercado (Consenso de Washington, valorización financiera, Estado regulador). Los 2000 = recomposición del rol estatal (crítica al neoliberalismo, recuperación de capacidades, soberanía sectorial).
- **Casos comparados:**
  - **YPF** — Ley 26.741 (Diputados 03/05/2012, Senado 26/04/2012, BO 07/05/2012).
  - **Aerolíneas Argentinas** — Ley 26.466 (Diputados 03/12/2008, Senado 17/12/2008, BO 24/12/2008).
- **Autores teóricos a movilizar funcionalmente** (no decorativos): Evans (autonomía enraizada), Skocpol, Polanyi (doble movimiento), O'Donnell, Aldo Ferrer (densidad nacional), Cavarozzi (matriz estado-céntrica), Rapoport, Basualdo (valorización financiera), Azpiazu y Schorr, Gerchunoff y Torre, Castellani (ámbitos privilegiados de acumulación), Kulfas, Heredia, Etchemendy y Collier, Murillo, Schamis, Weyland, Harvey, Jessop, Hall.
- **Fuentes primarias** (en `herramienta_votaciones/fuentes/`): diarios de sesiones y actas de votación nominal de ambas cámaras y casos (PDF/XLSX).

## 2. La herramienta (foco del trabajo de código)

- **Archivo único:** `herramienta_votaciones/index.html` — una sola página HTML/CSS/JS, autocontenida y **offline, sin dependencias externas** (~916 KB, ~13.000 líneas). Todo va embebido: datos, estilos, lógica y la tipografía **IBM Plex Sans** (WOFF2 base64, pesos 400/500/600/700). Se abre directo en el navegador, sin servidor.
- **Función:** complemento metodológico de la tesis. Visualiza las 4 votaciones nominales con que el Congreso trató ambas leyes. No reemplaza el análisis.

**Escenarios (IDs internos):** `aerolineas_diputados` · `aerolineas_senado` · `ypf_diputados` · `ypf_senado`.

**Metadatos por escenario** (objeto `SCENARIOS`): `id, caseName, chamber, chamberShort, date, law, lawBO, session, expediente, orden, voteType, totalSeats, quorum, present, absent, yes, no, abstain, result, president, source, voteSource, districtSource, territorialValidation, description, status, legislators`.
Ej. Aerolíneas Diputados: 257 bancas, quórum 129, presentes 238, ausentes 19, 153 a favor / 84 en contra / 0 abstención, AFIRMATIVO, pres. Eduardo Fellner, Exp. 6538-D-08, OD 1342.

**Shape de cada legislador** (array `*_LEGISLATORS`):
```js
{ name, block, family, province, vote }
// vote ∈ AFIRMATIVO | NEGATIVO | ABSTENCION | AUSENTE | PRESIDENCIA
```

**Arquitectura:** 4 pestañas principales — **Análisis del caso · Análisis comparado · Datos · Metodología** (la pestaña activa fija una clase `mode-*` en `<body>` que acota qué controles se muestran). Un selector de caso/cámara (dentro de Caso y Datos) elige la votación analizada. **Vistas del caso:** hemiciclo (bancas SVG `<circle class="seat">`) y mapa provincial (SVG con heatmap por provincia, inset Malvinas). **Diseño:** editorial de datos estilo Datawrapper, tipografía IBM Plex Sans.

**Modos de color:** `'vote'` o `'family'` (no ambos a la vez — ver regla central).

**Paleta voto** (`VOTE_COLORS`): AFIRMATIVO `#3f6e44` (verde hunter) · NEGATIVO `#a32a2a` (burgundy) · AUSENTE `#a09b8c` (piedra) · ABSTENCION `#a87523` (ocre) · PRESIDENCIA `#14181f` (tinta).

**Familias** (orden espectro izq→der): Socialista, Centroizquierda, FPV-PJ, Concertación/CF, Provinciales/otros, Otros, Peronismo disidente, UCR, Coalición Cívica, PRO (cada una con su color en `FAMILY_COLORS`).

**Indicadores politológicos:** cohesión de Rice (familia y bloque), fragmentación de Rae, ausentismo territorial, polarización, % afirmativo, comparadores entre los 4 escenarios, síntesis interpretativa automática (describe magnitudes vs umbrales explícitos; nunca causalidad).

**Exportación:** figuras PNG (hemiciclo/mapa con contexto académico embebido: eyebrow, título, bajada, metadatos, leyenda, nota de lectura, fuentes, crédito), tablas como PNG editorial, CSV (legisladores, provincias, familias, resumen, comparativo 4 escenarios + indicadores granulares) e Imprimir/PDF (anexo de ~54 páginas). ~40 botones de export. Los PNG rasterizan con la IBM Plex Sans embebida → idénticos online u offline.

**Estado** (objeto `state`, valores iniciales):
```js
scenarioId:'aerolineas_diputados', view:'hemicycle', colorMode:'vote',
voteFilter:'TODOS', provinceFilter:'', search:'', selectedIndex:null,
subtab:'case', tableSearch:'', sortCol:null, sortDir:1,
famHeatMetric:'pctYesPresent'
```

**Pipeline de render (funciones clave):**
- `render()` → `renderTabs`, `renderMetaBar`, `renderVizCard`.
- `renderVizCard()` arma las 4 pestañas (modo) con sus subpaneles y monta el selector de caso/cámara; `activateSubtab(name)` alterna el panel activo y fija la clase `mode-*` en `<body>`. Llama, entre otras, a: `populateProvinceSelect`, `renderValidationPanel`, `renderFigureBlocks`, `renderLegend`, `renderStage`, `renderSummaryStrip`, `renderTables`, `renderStatsTab`, `bind*Events`, `updateSeatCounter`.
- `renderStage()` → `renderHemicycle()` | `renderMap()`.
- Filtrado: `legMatchesFilters(leg, q)` (predicado único) ← usado por `applyFilters(svg)` (atenúa bancas con `.is-dim`) y `updateSeatCounter()`.
- Selección: `selectLegislator(idx)`. Pre-proceso de bancas: `assignSeats()`.
- Accesibilidad hemiciclo: `initSeatNavigation`, `getSeatNodes`, `navigableSeats`, `focusSeat`, `showSeatTooltipForNode`, `buildSeatAriaLabel`.

## 3. Regla de diseño central (no romper)

Voto y partido **nunca** se codifican en la misma marca visual. El color de la banca es el voto **o** la familia política (modo seleccionable); la otra dimensión va en tooltip, panel individual, filtros y tablas. Esta separación es lo que da rigor metodológico. Cualquier cambio de paleta/leyenda debe respetarla; si un pedido la rompe, **señalarlo antes de hacerlo**.

## 4. Reglas de trabajo sobre código/datos

1. No inventar datos. Si falta un dato, decirlo explícitamente.
2. No borrar info importante del HTML/JS ni de los datasets. No romper datasets.
3. No simplificar de más. No hardcodear innecesariamente; funciones reutilizables.
4. Leer y **entender la estructura antes de modificar**.
5. Validar conteos (totales de voto/ausentes/abstención por cámara) tras cambios.
6. Probar filtros y exportaciones (PNG y CSV) antes de declarar algo terminado.
7. Cuidar responsive y vista de impresión (Cmd+P).
8. Mantener **una sola página HTML autocontenida**.
9. Señalar inconsistencias en vez de taparlas.
10. Documentar decisiones no triviales en el código y registrar cambios sustantivos en `herramienta_votaciones/CHANGELOG.md`.
11. **Confirmar alcance antes de editar.** No avanzar de etapa sin que yo lo pida.
12. Hay tests con jsdom posibles para smoke-testear el render (conteos, teclado, contador) — usarlos para validar antes de cerrar una etapa.

## 5. Estado actual (al 2026-06-04)

Desarrollo funcional **cerrado**. La herramienta está estable, autocontenida y lista para tesis/anexo. Todas las etapas están hechas y commiteadas (detalle por commit en `herramienta_votaciones/CHANGELOG.md`):

- **Accesibilidad** — hemiciclo navegable por teclado (`role="button"` + `aria-label`, roving tabindex, flechas/Home/End, Enter/Espacio), contador con `aria-live`, foco visible. Predicado de filtrado unificado en `legMatchesFilters`.
- **Rediseño "Atlas" + Fase 5** — selector físico de caso/cámara dentro de Caso/Datos; trayectoria entre cámaras (B.6b) separada de la intra; diferencia territorial textual en B.5 (dos mapas planos, sin highlight, con contador + lista + nota); limpieza responsive desktop-first; optimización de código (CSS muerto removido).
- **Reskin tipográfico a IBM Plex Sans** (estilo Datawrapper), con la fuente **embebida en WOFF2 base64** (offline, portable) tanto en pantalla como en los PNG exportados.
- 4 escenarios validados; datasets/cálculos intactos; print de 54 páginas; exports PNG/CSV operativos.

## 6. Git / GitHub

- Repo **público**: <https://github.com/valentindomingocampos-star/Tesis> (rama `main`).
- Autenticado con `gh` CLI (cuenta `valentindomingocampos-star`), `gh auth setup-git` ya hecho.
- El versionado git reemplaza los backups manuales: los snapshots HTML de `backups/` se eliminaron por redundantes con el historial de commits.
- Para guardar cambios: `git add -A && git commit -m "..." && git push` (o pedírmelo: "subí los cambios").

## 7. Estructura del repo

```
.
├── README.md                     (raíz: describe tesis + herramienta)
├── CONTEXTO.md                   (este archivo)
├── .gitignore
└── herramienta_votaciones/
    ├── index.html                ← LA HERRAMIENTA (página única)
    ├── README.md                 (detalle técnico de la herramienta)
    ├── CHANGELOG.md              (historial de cambios)
    ├── docs/                     metodologia_fuentes.md, validacion_datos.md,
    │                             uso_herramienta.md, checklist_v1.md,
    │                             checklist_revision_final.md
    ├── fuentes/                  PDFs/XLSX de diarios de sesión y actas nominales
    ├── trazabilidad/             CSV de trazabilidad voto a voto (senado)
    └── auditorias/               reportes de auditoría de datos por escenario
```

**Primer paso sugerido en sesión nueva:** leer `herramienta_votaciones/index.html` y `herramienta_votaciones/docs/` antes de tocar nada, y confirmar el alcance conmigo.
