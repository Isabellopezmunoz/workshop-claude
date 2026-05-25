---
name: new-doc
description: Use this skill when the user asks to create a new documentation page under /docs/* in this project. Triggers when the user says "crea una doc", "añade una página de documentación", "new doc", "/new-doc", or mentions adding a new section under /docs. This skill scaffolds the markdown file with valid frontmatter, generates a base content structure following the project conventions, and adds the link to the header navigation.
---

# Crear una nueva doc

Esta skill automatiza la creación de una nueva página de documentación en el proyecto `workshop-claude`. Asegura que el nuevo `.md` cumple el schema de Content Collections y queda enlazado desde el header.

## Contexto del proyecto

- Las docs viven en `src/content/docs/*.md`.
- El schema está en `src/content.config.ts` — los campos obligatorios son `title` (string), `description` (string), `order` (number, default 99) e `icon` (string, default `lucide:book-open`).
- La URL se genera automáticamente como `/docs/{slug}`, donde el slug es el nombre del archivo sin extensión.
- El header con los links está en `src/components/Header.astro`, en el array `links`.
- Los iconos vienen de Lucide (`lucide:nombre`) — catálogo en https://lucide.dev/icons.

## Pasos a seguir cuando se active

### 1. Pregunta los datos mínimos al usuario

Pregunta de una vez (no uno a uno):

- **Slug del archivo** (kebab-case, **siempre en inglés**, será la URL `/docs/{slug}`). Si el usuario propone un slug en español, tradúcelo al inglés y confírmaselo (ej: `agentes` → `agents`, `flujos-de-trabajo` → `workflows`).
- **Título** que aparece en la página.
- **Description** corta (1 frase, sale bajo el título y en el sidebar del DocLayout).
- **Order** — si el usuario no lo da, mira los `order` existentes en `src/content/docs/*.md` y asigna el siguiente número disponible.
- **Icon** — sugiere uno de Lucide acorde al tema, pero deja que el usuario lo cambie.

Si el usuario ya dio alguno de estos datos en el mensaje inicial, no lo vuelvas a preguntar.

### 2. Comprueba que el slug no existe ya

Lista `src/content/docs/` y aborta con un mensaje claro si ya hay un `.md` con ese nombre.

### 3. Crea el archivo `src/content/docs/{slug}.md`

Usa este frontmatter exacto:

```markdown
---
title: {Título}
description: {Description}
order: {Order}
icon: {Icono}
---
```

Y a continuación genera una **estructura base vacía** siguiendo el patrón de las docs existentes (`skills.md`, `hooks.md`, `slash-commands.md`, `mcp.md`):

```markdown
## ¿Qué es {tema}?

> Escribe aquí la definición corta del concepto. Una frase clara, sin rodeos.

## ¿Para qué sirve?

- Caso de uso 1
- Caso de uso 2
- Caso de uso 3

## Cómo se usa

Explicación de los pasos básicos.

## Ejemplo

```ejemplo
contenido del ejemplo
```

Comenta brevemente qué hace el ejemplo.

## Buenas prácticas

- **Práctica 1.** Razón.
- **Práctica 2.** Razón.
- **Práctica 3.** Razón.

## Recursos

- [Enlace a docs oficial](https://...)
```

**Importante:** no inventes contenido sobre el tema. Solo deja el armazón con headings y placeholders. El usuario rellenará el contenido real después (o te lo pedirá explícitamente).

### 4. Añade el link al header

Edita `src/components/Header.astro` y añade una entrada al array `links` respetando el orden por `order` (insertarla en la posición correcta, no siempre al final):

```ts
{ label: '{Título}', href: '/docs/{slug}' },
```

### 5. Confirma al usuario

Reporta de forma concisa:

- Ruta del archivo creado.
- URL accesible (`/docs/{slug}`).
- Que el link se ha añadido al header.
- Sugiere que arranque `npm run dev` si no está corriendo, o que recargue el navegador si sí lo está.

No relances el dev server tú — el usuario lo decide.

## Reglas de la skill

- **No inventes contenido temático.** El armazón es genérico; el contenido lo aporta el usuario.
- **No commitees** ningún cambio (regla global del proyecto, ver `CLAUDE.md`).
- **No introduzcas dependencias nuevas.**
- **Si el icono Lucide propuesto no existe**, busca uno alternativo en https://lucide.dev/icons. Nunca uses emojis.
- **Si el usuario quiere que rellenes el contenido real** después de crear el armazón, hazlo como tarea separada (no es parte de esta skill).
