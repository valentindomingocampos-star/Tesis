# El péndulo del poder — Manual de sistema visual
Base: sistema Modernist, extendido con la identidad del péndulo. Aplicado a la herramienta de votaciones.

## 1. Concepto
- El péndulo es la identidad: línea de 2px con pivote fijo arriba y disco lleno abajo, oscilando alrededor de la vertical con easing sinusoidal. (En la herramienta se usa solo en transiciones, no como ornamento en vistas.)
- Tinta sobre tiza con un solo acento rojo, grilla modular visible, cero decoración. La atención se dirige con escala tipográfica y reglas de 2px, nunca con sombras, gradientes ni color extra.

## 2. Color (exactos, no aproximar)
```css
:root {
  --color-bg: #f3f2f2;        /* tiza — campo principal */
  --color-surface: #eae9e9;   /* paneles / celdas destacadas */
  --color-text: #201e1d;      /* tinta — todo el contenido estructural */
  --color-accent: #ec3013;    /* rojo — ÚNICO acento */
  --color-accent-400: #ff9783;/* acento sobre fondos oscuros */
  --color-accent-700: #ae1800;/* acento como texto sobre tiza (párrafos) */
  --color-neutral-600: #7d7979; /* etiquetas secundarias */
  --color-neutral-700: #605d5d; /* texto de apoyo */
  --color-neutral-800: #444141; /* texto secundario fuerte */
  --color-neutral-200: #eae7e7; /* pistas de barras vacías */
  --color-divider: color-mix(in srgb, #201e1d 40%, transparent);
}
```
Rojo: datos afirmativos, kickers, un highlight por vista. Nunca dos acentos, nunca rojo como fondo salvo pósters/portadas. Negativo/rechazo = tinta (`--color-neutral-800`). 

## 3. Tipografía
- Una sola familia: Archivo (Google Fonts), pesos 400 y 800. Nada de itálicas.
- Display/cifras hero: 64–84px · 800 · lh 1 · ls -0.02em (en app ~0,5×)
- Título de vista: 32–40px · 800 · -0.015em
- Kicker/etiquetas: 12–13px · 800 · MAYÚSCULAS · ls 0.12–0.14em
- Cuerpo: 15–16px · 400 · lh 1.45–1.55
- Todo alineado a la izquierda. Números con tabular-nums; miles con punto, decimales con coma.

## 4. Grilla y estructura
- Radio 0px en todo. Sin sombras (elevación imprescindible: 0 3px 10px rgba(45,43,43,.16)).
- Reglas de 2px entre secciones mayores; 1px para filas internas.
- Grillas de celdas de igual ancho con bordes visibles — la estructura SE MUESTRA.
- Anatomía: cinta superior (kicker izq + contexto der, regla 2px) → título → contenido → banda de síntesis.

## 5. Visualización de datos
- Afirmativo = `#ec3013` · Negativo = `#444141` · Abstención = `#a8a4a4` · Ausente = `#cfcccc` · Presidencia = `#201e1d`. Codificación FIJA.
- Familias (paleta P1, excepción de datos): FPV-PJ #33607f · UCR #8f4e2a · PRO #8f7430 · Coalición Cívica #2f6b64 · Socialista #8f3550 · Peronismo disidente #7a4633 · Centroizquierda #4a4a85 · Concertación/CF #33687d · Provinciales/otros #6b4d8f · Otros #6e6862. Regla intacta: voto y partido nunca en la misma marca.
- Barras planas, valor dentro o al final; pista vacía #eae7e7. Hemiciclos: discos llenos sin contorno; destacado = anillo 2px tinta. Mapas: pasos discretos, sin degradés. Etiquetas directas, sin leyendas flotantes ni gridlines (solo línea base 2px).
- Cada gráfico lleva frase de síntesis en 800 con highlight en rojo.

## 6. Motion
Easing entradas: cubic-bezier(0.16, 0.84, 0.28, 1) · escalonado 80ms (.08/.16/.24/.32/.4/.48s).
- fu: translateY(32px)→0, 0.65s · fl: translateX(-48px)→0, 0.7s · gx: scaleX(0→1), 0.85s, origin left · ri: reveal enmascarado 0.8s.
- Péndulo: sway con cubic-bezier(0.37, 0, 0.63, 1), 3.4s infinite alternate; amplitud ±10°→±2.5°.
- Contadores: 0→valor en 1.6s, ease-out cúbico, formato es-AR.
- Ken Burns fotos: scale(1.02)→scale(1.14), 18s, ease-in-out infinite alternate.
- Prohibido: bounce, elastic, spins, parallax, partículas, glitch. Respetar prefers-reduced-motion.

## 7. Fotografía
- Siempre B&N documental: grayscale(1) contrast(1.18–1.22) brightness(0.96–1.03).
- Fondo editorial: full-bleed + velo linear-gradient(180deg, rgba(32,30,29,.62), rgba(32,30,29,.26) 45%, rgba(32,30,29,.74)); texto en tiza.

## 8. Interacción
- Hover: paso siguiente de la rampa; superficies tinte 7% tinta. Focus: outline 2px #ec3013, offset 2px.
- Botones con etiqueta alineada a la izquierda; primario rojo lleno texto tiza.
- Selección de texto: rojo 30%. Tablas: header MAYÚSCULAS 11–12px + regla 2px; filas 1px; hover 4% tinta.

## 9. No hacer
Esquinas redondeadas · sombras SaaS · gradientes de color · segundo acento · emojis · íconos decorativos · texto centrado · leyendas/gridlines innecesarias · animar todo a la vez · tipografías ajenas a Archivo.
