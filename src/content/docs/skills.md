---
title: Skills
description: Instrucciones especializadas que le dicen a Claude cuándo y cómo trabajar en un dominio concreto.
order: 1
icon: lucide:sparkles
---

## ¿Qué es una skill?

Una **skill** es un conjunto de instrucciones que Claude carga cuando detecta que el contexto encaja. Son la forma de **enseñarle a Claude convenciones, flujos o conocimiento de un dominio** sin tener que repetirlas en cada conversación.

Una skill bien escrita responde a tres preguntas:

- **¿Cuándo activarse?** — el campo `description` le dice a Claude en qué situaciones la skill es relevante.
- **¿Cómo trabajar?** — el cuerpo de la skill (el archivo `SKILL.md`) contiene las instrucciones detalladas.
- **¿Qué archivos usar?** — opcionalmente, la skill puede incluir templates, scripts o referencias auxiliares.

> Piensa en una skill como un "modo experto" que se enciende solo. En vez de tener que decirle a Claude _"recuerda que en este proyecto usamos PascalCase para componentes y kebab-case para archivos"_ en cada conversación, escribes una skill `react-conventions` y la activa cuando detecta que estás trabajando con React.

## Anatomía de una skill

Una skill es una carpeta con un archivo `SKILL.md` dentro. El frontmatter contiene los metadatos y el cuerpo las instrucciones:

```markdown
---
name: my-skill
description: Use this skill when the user wants to do X. Triggers when…
---

# Instrucciones para Claude

Cuando se active esta skill, sigue estas reglas:

1. Primero haz X.
2. Después haz Y.
3. Nunca hagas Z.
```

Dos campos son obligatorios:

- **`name`** — identificador único, en kebab-case. Es lo que escribes para invocarla manualmente (`/my-skill`).
- **`description`** — la línea más importante de la skill. Claude la lee para decidir si activar la skill. Sé específico sobre **cuándo** debe activarse, no solo qué hace.

### El campo `description` es crítico

Esto es lo que diferencia una skill útil de una que nunca se dispara:

❌ **Mal**: `description: Skill for React`
✅ **Bien**: `description: Use this skill when the user asks to create, edit, or refactor React components. Triggers when files end in .tsx/.jsx or when the user mentions hooks, props, or JSX.`

Cuanto más explícito seas sobre los triggers, mejor decidirá Claude cuándo activarla.

## Tipos de skills

### Project skills

Viven en `.claude/skills/` dentro del repo. Se versionan con git y las comparte todo el equipo.

```
mi-proyecto/
└── .claude/
    └── skills/
        └── react-conventions/
            └── SKILL.md
```

**Cuándo usarlas:** convenciones del proyecto, arquitectura, patrones de testing, reglas de estilo específicas del repo.

### Personal skills

Viven en `~/.claude/skills/` (tu home). Te siguen entre proyectos y son privadas.

```
~/.claude/
└── skills/
    └── mi-flujo-de-commits/
        └── SKILL.md
```

**Cuándo usarlas:** tus preferencias personales, atajos que usas siempre, formatos de commit, estilo de escritura propio.

### Plugin skills

Vienen empaquetadas dentro de plugins de Claude Code. Se invocan con el prefijo del plugin: `/figma:use-figma`, `/figma:generate-design`, etc.

**Cuándo usarlas:** cuando instalas un plugin que añade integraciones (Figma, MCP servers concretos, herramientas de un proveedor…).

## Cómo se invocan

Hay tres formas de activar una skill:

1. **Invocación explícita** — escribes `/nombre-skill` en el chat. Claude la carga inmediatamente.
2. **Activación automática** — Claude lee el `description` de todas las skills disponibles y activa las que encajan con el contexto actual.
3. **Composición** — varias skills pueden estar activas al mismo tiempo. Por ejemplo, `/figma-use` + `/figma-generate-design` se combinan cuando trabajas con Figma.

## Crea tu primera skill paso a paso

Vamos a crear una skill que enseñe a Claude a escribir mensajes de commit en español siguiendo el formato Conventional Commits.

### 1. Crea la carpeta

Dentro de tu repo (o en `~/.claude/skills/` si la quieres global):

```bash
mkdir -p .claude/skills/commits-es
```

### 2. Crea el archivo `SKILL.md`

```markdown
---
name: commits-es
description: Use this skill when the user asks to write, draft, or improve a git commit message. Always write commit messages in Spanish following Conventional Commits format.
---

# Mensajes de commit en español

Cuando el usuario pida un mensaje de commit, sigue estas reglas:

## Formato

`tipo(scope): descripción corta en imperativo`

## Tipos permitidos

- `feat`: nueva funcionalidad
- `fix`: corrección de bug
- `docs`: cambios en documentación
- `style`: formato (sin cambios de lógica)
- `refactor`: cambio de código que no añade ni arregla
- `test`: añadir o corregir tests
- `chore`: tareas de mantenimiento

## Reglas

- La descripción va en **español** y en **imperativo** ("añade", no "añadido" ni "añadiendo").
- Máximo 72 caracteres en la primera línea.
- Si hace falta más contexto, deja una línea en blanco y añade un cuerpo explicativo.
- Nunca incluyas referencias a issues o PRs salvo que el usuario lo pida.
```

### 3. Pruébala

En tu próxima conversación con Claude, escribe `/commits-es` o simplemente pídele un commit message — debería activarse sola por el `description`.

## Buenas prácticas

- **Una skill, un propósito.** Si una skill hace demasiado, divídela. Es más fácil que Claude active dos skills pequeñas que una mega-skill ambigua.
- **Empieza por el `description`.** Antes de escribir el cuerpo, define exactamente cuándo debe activarse. Si no puedes describirlo claramente, la skill probablemente está mal planteada.
- **Versiona las project skills.** Las skills del proyecto deben commitearse al repo — son parte de la documentación viva del código.
- **Itera.** Si Claude no activa tu skill cuando debería, mejora el `description`. Si la activa cuando no toca, hazlo más restrictivo.
- **No dupliques CLAUDE.md.** Si una regla aplica a todo el repo, va en `CLAUDE.md`. Si aplica solo a un dominio concreto, va en una skill.

## Recursos

- [Documentación oficial de Skills](https://docs.claude.com/en/docs/claude-code/skills)
- [Marketplace de skills de la comunidad](https://claudecodemarketplaces.com)
