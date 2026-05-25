# Workshop Claude — instrucciones para Claude Code

Landing didáctica sobre Claude Code, construida con Astro + Tailwind v4. Para arranque, deploy y onboarding humano, consulta el [README.md](./README.md).

Este archivo recoge las convenciones del proyecto. Síguelas en cualquier cambio que hagas aquí.

## Stack

- **Astro 6** con plantilla `minimal` (sitio estático).
- **Tailwind CSS v4** vía `@tailwindcss/vite` — los tokens de diseño se declaran con `@theme` en `src/styles/global.css`.
- **astro-icon** con `@iconify-json/lucide` para los iconos.
- **Astro Content Collections** para las páginas de documentación.
- **Prettier** sin punto y coma, con comillas simples.

## Estructura

```
src/
├── components/          ← un componente por sección de la landing
├── content/docs/        ← markdown con el contenido de las pages /docs/*
├── content.config.ts    ← schema de las Content Collections (Zod)
├── layouts/
│   ├── Layout.astro     ← layout base (head, fuente, body)
│   └── DocLayout.astro  ← layout de las pages de docs (con TOC lateral)
├── pages/
│   ├── index.astro      ← landing
│   └── docs/[slug].astro ← page dinámica que renderiza cualquier doc
└── styles/global.css    ← @import tailwindcss + tokens @theme
```

## Convenciones de código

### Estilos

- **Nunca hardcodees colores ni fuentes.** Usa los tokens definidos en `src/styles/global.css` (clases como `bg-bg-base`, `text-text-primary`, `border-border-base`, `text-accent`, etc.).
- Si necesitas un color o variable que no existe, añádelo primero al bloque `@theme` y luego úsalo.
- La fuente del proyecto es **JetBrains Mono** (estética terminal). No introduzcas sans-serif.
- El acento es **verde lima** (`--color-accent: #a3e635`). Si en el futuro se cambia, basta tocar esa variable.

### Iconos

- **Nunca uses emojis** en componentes ni en docs visibles.
- Para iconos importa el componente de `astro-icon`:
  ```astro
  import { Icon } from 'astro-icon/components'
  <Icon name="lucide:nombre-del-icono" class="size-4 text-accent" />
  ```
- El set disponible es **Lucide** (`lucide:*`). Para ver iconos disponibles → [lucide.dev/icons](https://lucide.dev/icons).

### Componentes

- Un componente `.astro` por sección de la landing. Nada de mega-componentes.
- Los datos repetitivos (listas de tarjetas, links, etc.) viven en el frontmatter del componente como array, no hardcodeados en el JSX.
- En `Astro.props` y arrays internos usa **nombres descriptivos** (no `c`, `t`, `i`).

### Formato

- Sin punto y coma, comillas simples — Prettier lo gestiona.
- Antes de commitear: `npm run format` (o configurar VS Code para formatear al guardar).
- La config vive en `.prettierrc`.

## Añadir contenido nuevo

### Una sección a la landing

1. Crea `src/components/MiSeccion.astro` siguiendo el patrón de las existentes.
2. Importa y úsalo en `src/pages/index.astro`.
3. Si quieres link desde el header, añádelo al array `links` en `src/components/Header.astro`.

### Una doc nueva

1. Crea `src/content/docs/mi-doc.md` con este frontmatter:
   ```yaml
   ---
   title: Mi doc
   description: Una frase corta que aparece bajo el título.
   order: 5  # posición en el menú (los existentes van 1-4)
   icon: lucide:nombre-icono
   ---
   ```
2. La URL será automáticamente `/docs/mi-doc`.
3. Para mostrarla en el header, añade un link en `src/components/Header.astro`.

## Cosas que NO hacer

- **No edites `.astro/`, `dist/`, `node_modules/`** — son generadas.
- **No commitees** sin que la usuaria lo pida explícitamente.
- **No introduzcas dependencias nuevas** sin pedir confirmación.
- **No uses gradientes lila/morados** — la paleta es negros + acento verde.
- **No añadas comentarios obvios al código.** Si el código se explica solo, no comentes.

## Verificación visual

Para cambios de UI: arranca `npm run dev` y comprueba en `http://localhost:4321/` antes de dar el cambio por hecho. Los tests/typecheck no detectan que el header se haya descolocado.
