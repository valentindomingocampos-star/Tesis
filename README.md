# El péndulo del poder — YPF y Aerolíneas Argentinas

Repositorio de la tesis de grado en **Ciencia Política y Relaciones Internacionales (UBA)** *"El péndulo del poder: disputas de propiedad, soberanía y desarrollo en la República Argentina"*, junto con su complemento metodológico: una herramienta interactiva de visualización de las votaciones legislativas de reestatización.

## Sobre la tesis

La investigación analiza cómo y por qué el Estado argentino redefinió su vínculo con dos empresas estratégicas en dos momentos opuestos —la privatización de los años 90 y la reestatización de los 2000— y qué papel jugaron las coaliciones políticas, la autonomía estatal y los proyectos de desarrollo.

**Casos comparados:**

- **YPF** — Ley 26.741 (BO 07/05/2012)
- **Aerolíneas Argentinas** — Ley 26.466 (BO 24/12/2008)

La hipótesis sostiene que privatizaciones y reestatizaciones no fueron decisiones administrativas aisladas, sino expresiones de cambios en las coaliciones de poder y en la concepción del rol del Estado sobre sectores estratégicos. El enfoque es de **economía política comparada**, centrado en actores, ideas e instituciones, y evita deliberadamente la lectura normativa (privatizar = malo / reestatizar = bueno).

## La herramienta de visualización

`herramienta_votaciones/` contiene una aplicación **HTML/CSS/JS de una sola página, autocontenida y sin dependencias externas** (`index.html`), que reconstruye las **cuatro votaciones nominales** con las que el Congreso trató ambas leyes:

| Caso | Cámara |
|------|--------|
| Aerolíneas Argentinas | Diputados · Senado |
| YPF | Diputados · Senado |

Permite leer la composición del voto por **banca, bloque político, familia partidaria y provincia**, en dos vistas complementarias: **hemiciclo** y **mapa provincial**.

**Principio de diseño central:** voto y partido nunca se codifican en la misma marca visual. El color representa el voto *o* la familia política (modo seleccionable); la otra dimensión se consulta vía tooltip, panel individual, filtros y tablas. Esta separación es lo que le da rigor metodológico a la lectura.

**Incluye:** filtros por voto, provincia y búsqueda; navegación por teclado del hemiciclo (accesibilidad); contador de resultados en vivo; indicadores politológicos (cohesión de Rice, fragmentación de Rae, ausentismo territorial, polarización); y exportación de figuras y tablas a PNG con contexto académico y CSV, listas para anexo de tesis.

## Estructura del repositorio

```
.
├── README.md                  ← este archivo
└── herramienta_votaciones/
    ├── index.html             ← la herramienta (página única autocontenida)
    ├── README.md              ← detalle técnico de la herramienta
    ├── CHANGELOG.md           ← historial de cambios sustantivos
    ├── docs/                  ← metodología, validación de datos, guía de uso, checklists
    ├── fuentes/               ← fuentes primarias: diarios de sesiones y actas de votación nominal (PDF/XLSX)
    ├── trazabilidad/          ← CSV de trazabilidad voto a voto
    └── auditorias/            ← reportes de auditoría de datos por escenario
```

## Fuentes

Los datos provienen de **fuentes primarias oficiales**: los diarios de sesiones de ambas cámaras y las actas de votación nominal de cada ley (`herramienta_votaciones/fuentes/`). Los conteos de la herramienta se derivan de esos documentos y están validados; el detalle metodológico está en `herramienta_votaciones/docs/metodologia_fuentes.md` y `validacion_datos.md`.

## Uso

La herramienta no requiere instalación ni servidor: basta con abrir `herramienta_votaciones/index.html` en cualquier navegador moderno.

## Estado

Herramienta en versión estable, apta para tesis, defensa oral y anexo. El desarrollo continúa en etapas documentadas en el `CHANGELOG.md`.
