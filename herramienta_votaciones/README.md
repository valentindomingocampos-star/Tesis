# Herramienta interactiva de visualización legislativa

> **Versión:** estable para tesis · diseño final con IBM Plex Sans embebida (autocontenida y offline).

Plataforma autocontenida (HTML + CSS + JS) para visualizar las **votaciones nominales** asociadas a la reestatización de empresas estratégicas argentinas: **Ley 26.466** (Aerolíneas Argentinas, 2008) y **Ley 26.741** (YPF, 2012), en sus tratamientos en Diputados y en el Senado de la Nación.

Forma parte del trabajo de tesis de grado *"El péndulo del poder: disputas de propiedad, soberanía y desarrollo en la República Argentina"* (Ciencia Política y Relaciones Internacionales).

## Estado actual

- **Los 4 escenarios están validados** y pasan los chequeos internos con `críticos: 0` e `importantes: 0`. Datasets intactos desde 2026-05-14: 257 / 72 / 257 / 72.
- **Diputados** (Aerolíneas y YPF) validado/reconstruido desde las **actas nominales oficiales** de la Honorable Cámara de Diputados de la Nación. Los cuatro campos del registro (`name`, `block`, `province`, `vote`) provienen del acta.
- **Senado** (Aerolíneas y YPF) validado con **doble fuente**: voto desde el acta nominal del HSN; distrito/provincia reconstruido desde el archivo canónico `SEN AMPLIADOS XLSX` de la Secretaría Parlamentaria del HSN, porque las actas nominales del Senado no incluyen la columna provincia.
- **Diseño editorial de datos (estilo Datawrapper)**: tipografía **IBM Plex Sans embebida en el HTML** (WOFF2 base64, pesos 400/500/600/700 — sin fuentes ni librerías externas), tinta sobria sobre fondo claro, colores de voto institucionales (verde hunter para afirmativo, burgundy para negativo, ocre mostaza para abstención, piedra cálida para ausente, tinta para presidencia), familias políticas con 10 tonos profundos desaturados.
- **Modo familia limpio**: las bancas codifican un único plano (regla metodológica: voto y partido nunca compiten en la misma marca). El cruce se consulta en tooltip, panel individual, filtros y tablas.
- **Tablas editoriales**: 4 tarjetas distintas (tabla nominal, resumen territorial, resumen por familia, ficha institucional) con tipografía afinada, mini-barras de composición, badges/chips sobrios y notas metodológicas al pie.
- **Exportaciones académicas**: 4 PNG con contexto académico (hemiciclo + mapa, modo voto + modo familia), 4 PNG de tablas editoriales, 5 CSV con columnas en español formal y trazabilidad por fila.

## Archivo maestro

```
herramienta_votaciones/index.html
```

Es el único archivo que se ejecuta. Está pensado como una **sola página HTML autocontenida**: no carga fuentes ni librerías externas, no consume datos en red, y abre en cualquier navegador moderno sin instalar nada.

## Cómo abrirla

**Opción A — doble clic.** Abrir directamente `index.html` con el navegador. Las exportaciones a PNG funcionan, pero algunos navegadores aplican restricciones de `file://` que pueden afectar cómo se descargan las imágenes.

**Opción B — servidor local (recomendado).** Desde la raíz del proyecto:

```bash
cd /Users/vdc78/Desktop/Tesis
python3 -m http.server 8000
```

Y luego visitar `http://localhost:8000/herramienta_votaciones/`.

Trabajar bajo `http://` evita restricciones del esquema `file://` para exportar PNG y para algunos comportamientos de `canvas`.

## Qué contiene

Las cuatro votaciones nominales:

1. **Aerolíneas Argentinas · Cámara de Diputados** (03/12/2008, 257 bancas).
2. **Aerolíneas Argentinas · Senado de la Nación** (17/12/2008, 72 bancas).
3. **YPF · Cámara de Diputados** (03/05/2012, 257 bancas).
4. **YPF · Senado de la Nación** (26/04/2012, 72 bancas).

La herramienta organiza el análisis en **cuatro pestañas principales**. Un **selector de caso/cámara** (dentro de las pestañas Análisis del caso y Datos) elige cuál de las cuatro votaciones se está analizando.

### 1 · Análisis del caso

Lectura en profundidad de la votación seleccionada:

- **Hemiciclo** SVG con cada banca posicionada según orden político y **mapa provincial** SVG con las 24 jurisdicciones (Malvinas como referencia cartográfica); toggle entre ambas vistas.
- **Toggle de modo de color**: por voto o por familia política (regla central: una banca, un plano — el cruce se consulta en tooltip, panel individual, filtros y tablas).
- **Filtros**: buscador libre, por provincia, por voto y botón de limpiar; contador de resultados en vivo. Hemiciclo navegable por teclado.
- **Ficha institucional** colapsable (línea resumen Ley · Cámara · Fecha · Resultado · tally siempre visible; header/trámite/fuentes bajo el toggle) y panel de **validación de datos** (verde "Datos validados" / "con doble fuente" para Senado; amarillo "Bajo revisión territorial").
- **Indicadores politológicos del escenario (A.1–A.6)** con síntesis interpretativa automática (descriptiva, sin causalidad): resultado agregado, participación y ausentismo, cohesión por familia y por bloque, territorialización y polarización, y cohesión dicotómica de **Rice** + fragmentación de **Rae**.
- **Panel lateral** con la ficha del legislador seleccionado y el resumen nacional / provincial.

### 2 · Análisis comparado

Comparación sistemática de las cuatro votaciones (secciones B.1–B.10): tabla comparativa agregada, diferencias entre casos y entre cámaras (Δ en puntos porcentuales, incluida Δ de cohesión promedio por familia), rankings cualitativos, **hemiciclos lado a lado**, **mapas lado a lado** (con resumen territorial de las provincias que cambiaron de voto dominante), **trayectoria de legisladores** intra-cámara y entre cámaras (Diputados 2008 → Senado 2012), familias por % afirmativo, provincias por voto predominante, **Rae comparado**, y una **grilla panorámica 2×2** de los cuatro escenarios.

### 3 · Datos

Base nominal de la votación seleccionada: **ficha institucional** (grid clave/valor en 5 secciones — Identificación · Trámite · Conteos · Fuentes · Validación) y **tabla nominal** completa de legisladores, ordenable y con búsqueda.

### 4 · Metodología

Aparato metodológico en 10 secciones: fuentes y universo empírico, casos y cámaras, clasificación de votos, tratamiento de presidencia y no voto, bloques y familias, agregación territorial, Rice, Rae, criterios de trayectoria, y criterios de lectura/exportación.

**Definición de los índices.** **Rice** = `|Afi − Neg| / (Afi + Neg)` por familia y bloque (excluye ausentes, presidencia y abstenciones; si `Afi + Neg = 0` → "No calculable"). **Rae** = `1 − Σ pᵢ²` sobre afirmativo, negativo y abstención (excluye ausentes y presidencia; cercano a 0 = voto concentrado, más alto = fragmentado). El **alineamiento oficialismo/oposición** no se calcula con el dataset actual (requiere una variable analítica adicional).

## Estado de validación

Al momento del cierre de esta versión, los cuatro escenarios pasan la validación interna con `críticos: 0` e `importantes: 0`:

| Escenario | Estado | Fuente del voto | Fuente del distrito |
|---|:-:|---|---|
| Aerolíneas · Diputados | ✓ validado contra PDF oficial | Acta nominal HCDN | Acta nominal HCDN |
| Aerolíneas · Senado | ✓ validado con doble fuente | Acta nominal HSN | SEN AMPLIADOS XLSX (Secretaría Parlamentaria HSN) |
| YPF · Diputados | ✓ reconstruido desde PDF oficial | Acta nominal HCDN | Acta nominal HCDN |
| YPF · Senado | ✓ validado con doble fuente | Acta nominal HSN | SEN AMPLIADOS XLSX (Secretaría Parlamentaria HSN) |

**Advertencia metodológica importante.** Para los dos escenarios de Senado, la columna *provincia* no aparece en el acta de votación nominal (que sólo lista nombre y voto). La provincia/distrito de cada senador se reconstruyó a partir de los archivos `SEN AMPLIADOS *.xlsx` provistos por la Secretaría Parlamentaria del Honorable Senado de la Nación. El banner verde "Datos validados con doble fuente" deja constancia explícita de esa doble procedencia. Ver `docs/metodologia_fuentes.md` para el detalle.

## Estructura del proyecto

```
herramienta_votaciones/
├── index.html                  ← archivo maestro
├── README.md                   ← este archivo
├── CHANGELOG.md                ← historial de cambios
├── docs/                       ← documentación metodológica y de uso
│   ├── metodologia_fuentes.md
│   ├── validacion_datos.md
│   ├── uso_herramienta.md
│   ├── checklist_v1.md         ← checklist de entrega v1.0 (histórico)
│   └── checklist_revision_final.md
├── fuentes/                    ← archivos originales (PDFs y XLSX por escenario)
│   ├── aerolineas_diputados/
│   ├── aerolineas_senado/
│   ├── ypf_diputados/
│   └── ypf_senado/
├── trazabilidad/               ← cruces nombre × distrito × fuente (Senado)
└── auditorias/                 ← reportes por escenario y auditoría final
    ├── auditoria_final.md
    ├── reporte_aerolineas_diputados.md
    ├── reporte_ypf_diputados.md
    └── reporte_senado.md
```

## Cómo exportar

Los botones de exportación viven en cada pestaña, junto al contenido que exportan (no hay carpetas versionadas: los archivos se descargan desde la herramienta). Además, **Imprimir / PDF** genera el anexo completo paginado (~54 páginas). Las figuras PNG rasterizan con la **misma IBM Plex Sans embebida**, de modo que se ven igual en cualquier máquina, online u offline.

- **Figuras PNG con contexto académico (recomendado).** Hemiciclo y mapa rasterizados a 2,5× sobre fondo blanco `#ffffff`, con eyebrow institucional, título en IBM Plex Sans, bajada, franja de metadatos (Fecha · Ley · Sesión · Tipo de votación · Bancas/Quórum · Resultado), leyenda cromática embebida, nota de lectura, fuentes documentales, nota cartográfica de Malvinas (mapa) y crédito al pie. Filename ej.: `aerolineas_diputados_hemiciclo_contexto_voto.png`.
- **Figuras PNG solo gráfico** (opción secundaria). El SVG crudo sin wrapper, para usos editoriales donde el contexto se compone aparte. Filename ej.: `aerolineas_diputados_hemiciclo_voto.png`.
- **Tablas como figura PNG.** Cada tabla se renderiza como figura editorial autosuficiente (eyebrow + título + bajada + meta-chips + tabla con badges/chips/mini-barras + fuentes + credit). Filenames: `<escenario>_tabla_legisladores.png`, `tabla_provincias.png`, `tabla_familias.png`, `resumen_institucional.png`. La tabla nominal exporta las filas visibles tras filtros y búsqueda; si no hay filtros y son > 100 filas, pide confirmación.
- **Tablas CSV.** Codificación UTF-8 con BOM (acentos respetados en Excel y Numbers). Cinco salidas: `<escenario>_tabla_legisladores.csv`, `tabla_provincias.csv`, `tabla_familias.csv`, `resumen_institucional.csv`, y `comparativo_votaciones_resumen.csv` (4 escenarios). Cada CSV incluye contexto institucional (Caso · Cámara · Fecha · Ley), valores de voto en español formal (Afirmativo / Negativo / Abstención / Ausente / Presidencia), porcentajes 1-decimal sobre el denominador apropiado, fuentes documentales y estado de validación.

## Fuentes

| Tipo | Archivo | Ubicación |
|---|---|---|
| Acta nominal Diputados (AAAA) | `VOTO DIPUTADOS AAAA.pdf` | `fuentes/aerolineas_diputados/` |
| Acta nominal Diputados (YPF) | `VOTO DIPUTADOS YPF.pdf` | `fuentes/ypf_diputados/` |
| Acta nominal Senado (AAAA) | `VOTO SENADORES AAAA.pdf` | `fuentes/aerolineas_senado/` |
| Acta nominal Senado (YPF) | `VOTO SENADORES YPF.pdf` | `fuentes/ypf_senado/` |
| Datos ampliados Senado (AAAA) | `SEN AMPLIADOS AAAA.xlsx` | `fuentes/aerolineas_senado/` |
| Datos ampliados Senado (YPF) | `SEN AMPLIADOS YPF.xlsx` | `fuentes/ypf_senado/` |
| Diarios de sesiones | `DIP/SEN *.pdf` | `fuentes/*/` |

## Respaldos

El versionado por git cumple la función de respaldo: cualquier estado anterior del `index.html` maestro es recuperable desde el historial de commits (incluidas las reconstrucciones de dataset y las etapas de pulido editorial).

## Cómo seguir trabajando

- Cualquier modificación de fondo del HTML debería pasar por `node --check` de su script extraído y por `python3` con el auditor de datasets antes de declararla terminada.
- Cualquier cambio de fuentes debe quedar registrado en `CHANGELOG.md` y, si toca datasets, generar un archivo nuevo en `trazabilidad/`.
- Si se actualiza alguna composición legislativa, la convención es: extraer la fuente primaria, generar el dataset reconstruido, cruzar contra el dataset actual, presentar un reporte con confianza alta/media/baja, y aplicar sólo tras autorización explícita.
