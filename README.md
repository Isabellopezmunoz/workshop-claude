# Workshop Claude — Landing didáctica sobre Claude Code

Proyecto de práctica para aprender a usar **Claude Code**. El resultado es una landing page que explica las piezas principales del CLI (skills, hooks, slash commands, MCP) y sirve a la vez como ejercicio para entender el flujo de trabajo con Claude.

## Stack

- **[Astro](https://astro.build)** — sitio estático, componentes `.astro`.
- **[Tailwind CSS v4](https://tailwindcss.com)** — utilidades directamente en el markup.
- **[Vercel](https://vercel.com)** — despliegue gratuito desde el repo.

## Estructura del proyecto

```
workshop-claude/
├── public/                  ← favicon y assets estáticos
├── src/
│   ├── components/          ← un componente por sección
│   │   ├── Hero.astro
│   │   ├── Skills.astro
│   │   ├── Capabilities.astro
│   │   ├── Resources.astro
│   │   └── Footer.astro
│   ├── layouts/
│   │   └── Layout.astro     ← <head>, meta, body base
│   ├── pages/
│   │   └── index.astro      ← compone las secciones
│   └── styles/
│       └── global.css       ← @import "tailwindcss"
├── .claude/
│   └── skills/              ← skills propias del proyecto
├── astro.config.mjs
└── package.json
```

## Secciones de la landing

1. **Hero** — presentación y CTAs.
2. **Skills** — qué son, tipos (project / personal / plugin) y cómo se invocan.
3. **Capabilities** — hooks, slash commands y MCP servers.
4. **Resources** — enlaces a documentación oficial y recursos útiles.
5. **Footer**.

## Comandos

| Comando            | Acción                                          |
| :----------------- | :---------------------------------------------- |
| `npm install`      | Instala dependencias                            |
| `npm run dev`      | Arranca el dev server en `localhost:4321`       |
| `npm run build`    | Construye el sitio en `./dist/`                 |
| `npm run preview`  | Previsualiza el build local antes de desplegar  |

## Cómo trabajar en el proyecto con Claude Code

1. Arranca el dev server: `npm run dev`.
2. Pide cambios concretos a Claude: *"añade una sección de FAQ"*, *"cambia la paleta a tonos verdes"*, etc.
3. Si tienes skills definidas en `.claude/skills/`, invócalas explícitamente con `/nombre-skill` o deja que se activen solas según su `description`.

## Despliegue en Vercel

1. Entra en [vercel.com](https://vercel.com) y conecta el repo de GitHub.
2. Vercel detecta Astro automáticamente — no hace falta configurar build ni output.
3. Cada `git push` a `main` dispara un nuevo deploy.

## Notas de aprendizaje

- Las **skills** viven en `.claude/skills/` y describen *cuándo* y *cómo* Claude debe actuar en un dominio concreto del proyecto.
- Este repo es deliberadamente pequeño: el foco está en el flujo de Claude Code, no en la complejidad del código.
