---
title: Slash commands
description: Atajos que invocas escribiendo "/" — built-in del CLI o creados por ti para automatizar flujos.
order: 3
icon: lucide:slash
---

## ¿Qué es un slash command?

Un **slash command** es un atajo que invocas escribiendo `/` seguido de un nombre. Cuando lo ejecutas, Claude carga un prompt predefinido y lo procesa como si lo hubieras escrito tú.

Son la forma de **reutilizar prompts complejos sin tener que reescribirlos**. En vez de escribir cada vez _"haz un code review de los cambios pendientes mirando seguridad, performance y estilo…"_, escribes `/review` y listo.

## Tipos de slash commands

### Built-in del CLI

Vienen con Claude Code. Algunos ejemplos:

- **`/help`** — muestra la ayuda.
- **`/clear`** — limpia la conversación actual.
- **`/config`** — abre la configuración.
- **`/init`** — crea un `CLAUDE.md` analizando el proyecto.
- **`/compact`** — comprime la conversación cuando se hace muy larga.

### Custom (creados por ti)

Los defines tú en archivos markdown. Pueden vivir a nivel de proyecto o global.

### De skills

Algunas skills exponen slash commands. Por ejemplo, la skill `verify` se invoca con `/verify`.

### De plugins

Los plugins también añaden slash commands con prefijo: `/figma:generate-design`, `/figma:use-figma`, etc.

## Crear un slash command custom

Los custom commands viven en:

- **Proyecto**: `.claude/commands/<nombre>.md`
- **Global**: `~/.claude/commands/<nombre>.md`

Cada archivo `.md` es un comando. El nombre del archivo es el nombre del comando.

### Ejemplo 1 — Comando simple

Crea `.claude/commands/changelog.md`:

```markdown
Mira los commits desde la última tag y genera un changelog en español
agrupando los cambios por tipo (features, fixes, otros).

Formato:

## [versión-siguiente]

### Features

- ...

### Fixes

- ...
```

Ahora puedes escribir `/changelog` y Claude ejecutará ese prompt completo.

### Ejemplo 2 — Comando con argumentos

Los comandos pueden recibir argumentos con `$ARGUMENTS`:

```markdown
Crea un componente React llamado $ARGUMENTS en src/components/.

Reglas:

- Componente funcional con TypeScript
- Props tipadas con interface
- Export default al final
- Test básico en el mismo directorio
```

Lo invocas así: `/new-component UserCard` — y `$ARGUMENTS` se sustituye por `UserCard`.

### Ejemplo 3 — Comando con frontmatter

Puedes añadir frontmatter para personalizar el comportamiento:

```markdown
---
description: Audita la seguridad del código pendiente de commitear
allowed-tools: Bash, Read, Grep
---

Revisa los cambios pendientes (git diff) y busca:

- Secretos hardcodeados (API keys, passwords, tokens)
- Uso inseguro de eval, exec o similares
- Inyecciones SQL o XSS potenciales
- Permisos demasiado abiertos en archivos creados

Reporta cada hallazgo con la línea concreta y la severidad.
```

## Ejemplos típicos

Algunos commands que vale la pena tener siempre a mano:

- **`/review`** — code review de los cambios pendientes.
- **`/test`** — escribe tests para el código que acabas de tocar.
- **`/explain`** — explica qué hace este archivo o función.
- **`/refactor`** — sugiere refactors aplicando un principio concreto.
- **`/commit`** — escribe un commit message para los cambios staged.

## Buenas prácticas

- **Nombres cortos y memorables.** Vas a escribirlos mucho — `/review` mejor que `/run-code-review`.
- **Un comando, un propósito.** Si necesita 3 modos distintos, son 3 comandos.
- **Documenta el `description` en frontmatter.** Aparece en `/help`.
- **No abuses de los argumentos.** Si necesitas más de uno, probablemente es mejor que sea una skill o que Claude te pregunte.
- **Versiona los del proyecto.** Los commands en `.claude/commands/` son parte del repo y del onboarding del equipo.

## Diferencia con skills

|                 | Slash command                  | Skill                                  |
| --------------- | ------------------------------ | -------------------------------------- |
| **Activación**  | Manual (`/nombre`)             | Manual o automática                    |
| **Tamaño**      | Prompt corto                   | Conjunto de instrucciones complejas    |
| **Trigger**     | Cuando lo escribes             | Cuando el contexto encaja              |
| **Cuándo usar** | Tareas concretas y repetitivas | Conocimiento de dominio o convenciones |

Regla rápida: si es _"ejecuta este prompt"_, es un command. Si es _"compórtate de esta manera cuando…"_, es una skill.

## Recursos

- [Documentación oficial de Slash Commands](https://docs.claude.com/en/docs/claude-code/slash-commands)
