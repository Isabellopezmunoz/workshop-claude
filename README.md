# Workshop Claude — Landing de Perritos 🐶

Proyecto de práctica para aprender a usar **Claude Code** y, en concreto, el sistema de **skills** personalizadas.

## Objetivo

Construir una landing page sencilla sobre perritos para probar el flujo completo de trabajo con Claude:

- Invocar skills personalizadas (`/html`, `/css`).
- Iterar sobre el diseño y el contenido con prompts.
- Aprender cómo Claude Code edita ficheros, mantiene contexto y usa herramientas.

## Skills usadas en este proyecto

- **`html`** — genera y edita el HTML semántico de `index.html`. Enlaza Tailwind vía CDN.
- **`css`** — aplica estilos con utilidades de Tailwind directamente en el HTML (sin `styles.css` aparte).

## Estructura prevista

```
workshop-claude/
├── README.md      ← este archivo
└── index.html     ← landing (HTML + Tailwind CDN)
```

## Secciones de la landing

1. **Hero** — título grande, subtítulo y CTA ("Adopta un perrito").
2. **Galería** — grid con fotos de perritos.
3. **Sobre nosotros** — breve texto sobre la "protectora".
4. **Footer** — enlaces de contacto y redes.

## Cómo trabajar en el proyecto

1. Pídele a Claude cambios concretos: *"añade una sección hero con un CTA"*, *"haz la galería responsive"*, etc.
2. Si quieres invocar una skill explícitamente, escribe `/html` o `/css` seguido de tu instrucción.
3. Abre `index.html` en el navegador para ver los cambios — no hace falta build, Tailwind se carga por CDN.

## Notas de aprendizaje

- Las **skills** viven en `.claude/skills/` y describen *cuándo* y *cómo* Claude debe actuar en un dominio concreto.
- Este proyecto es deliberadamente pequeño para centrar la atención en el flujo de Claude, no en la complejidad del código.
