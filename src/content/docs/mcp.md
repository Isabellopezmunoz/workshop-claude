---
title: MCP servers
description: Model Context Protocol — conecta Claude con herramientas externas, APIs y fuentes de datos.
order: 4
icon: lucide:server
---

## ¿Qué es MCP?

**MCP** es Model Context Protocol — un estándar abierto que permite a Claude (y a otros modelos) conectarse con **herramientas y datos externos** a través de servidores especializados.

En lugar de que cada modelo tenga que reinventar cómo hablar con Figma, GitHub o tu base de datos, MCP define un protocolo común. **Cualquier MCP server funciona con cualquier cliente MCP-compatible.**

> Piensa en MCP como USB para modelos de IA: enchufas un servidor y Claude obtiene nuevas capacidades sin tener que actualizarlo.

## ¿Para qué sirve?

Con MCP, Claude puede:

- **Leer y escribir en herramientas que no conoce de base** — Figma, Notion, Linear, Slack.
- **Acceder a tus datos** — bases de datos, APIs internas, ficheros remotos.
- **Ejecutar acciones en tu navegador** — Chrome DevTools, automatización web.
- **Integrarse con servicios cloud** — Vercel, AWS, Google Drive, Gmail.

Cada MCP server expone:

- **Tools** — acciones que Claude puede ejecutar (`create_file`, `search_design`, `get_pr`).
- **Resources** — datos que Claude puede leer (archivos, registros de DB, contenido remoto).
- **Prompts** — plantillas de prompt que el server provee.

## MCP servers populares

Algunos servidores que vale la pena explorar:

- **Chrome DevTools MCP** — controla Chrome desde Claude, toma screenshots, ejecuta JS, audita performance.
- **Figma MCP** — lee y escribe diseños, crea componentes, sincroniza tokens.
- **GitHub MCP** — gestiona issues, PRs, releases.
- **Filesystem MCP** — acceso controlado a directorios específicos.
- **Postgres MCP** — consulta bases de datos.
- **Slack / Gmail / Google Drive** — integraciones con productividad.

La lista completa y oficial está en el [registro de MCP servers](https://github.com/modelcontextprotocol/servers).

## Cómo conectar un MCP server

Los MCP servers se configuran en `~/.claude/settings.json` (global) o `.claude/settings.json` (proyecto), bajo la clave `mcpServers`:

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/Users/tu-usuario/Documents"]
    }
  }
}
```

Estructura:

- **Clave** (`filesystem`) — nombre con el que se identifica el server en Claude.
- **`command`** y **`args`** — cómo arrancar el server (puede ser un binario, `npx`, `docker`, etc.).
- **`env`** — variables de entorno opcionales (API keys, etc.).

Después de añadir un MCP server, reinicia Claude Code para que lo cargue.

## Ejemplo — conectar Chrome DevTools MCP

Útil cuando trabajas en frontend y quieres que Claude pueda ver el resultado en el navegador:

```json
{
  "mcpServers": {
    "chrome-devtools": {
      "command": "npx",
      "args": ["-y", "chrome-devtools-mcp"]
    }
  }
}
```

Ahora puedes pedirle cosas como _"abre localhost:3000, haz screenshot y dime si el header está bien alineado"_ — Claude usa las tools del MCP para abrir Chrome, navegar y tomar la captura.

## Usar las tools del MCP

Una vez configurado, las tools del server aparecen con el prefijo del nombre del server. Ejemplo con Chrome DevTools:

- `mcp__chrome-devtools__new_page`
- `mcp__chrome-devtools__take_screenshot`
- `mcp__chrome-devtools__list_console_messages`

Claude decide cuándo usarlas según lo que le pidas. No necesitas invocarlas manualmente — solo describe lo que quieres hacer.

## Permisos

Por defecto, Claude te pide permiso cada vez que usa una tool de MCP. Puedes auto-aprobarlas añadiendo permisos en `settings.json`:

```json
{
  "permissions": {
    "allow": [
      "mcp__chrome-devtools__take_screenshot",
      "mcp__chrome-devtools__list_console_messages"
    ]
  }
}
```

Concede permisos solo a tools que sean **read-only o reversibles**. Para acciones destructivas (eliminar archivos, enviar mensajes…), deja que te pida confirmación cada vez.

## Crear tu propio MCP server

Si no existe un MCP para lo que necesitas, puedes escribirlo tú. Hay SDKs oficiales en:

- **TypeScript** — `@modelcontextprotocol/sdk`
- **Python** — `mcp` (paquete oficial)

Un MCP mínimo en TypeScript:

```ts
import { Server } from '@modelcontextprotocol/sdk/server/index.js'
import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio.js'

const server = new Server({ name: 'mi-mcp', version: '0.1.0' }, { capabilities: { tools: {} } })

server.setRequestHandler('tools/list', async () => ({
  tools: [
    {
      name: 'hola',
      description: 'Saluda al usuario',
      inputSchema: { type: 'object', properties: { nombre: { type: 'string' } } },
    },
  ],
}))

server.setRequestHandler('tools/call', async (request) => {
  if (request.params.name === 'hola') {
    return { content: [{ type: 'text', text: `Hola ${request.params.arguments.nombre}` }] }
  }
})

const transport = new StdioServerTransport()
await server.connect(transport)
```

## Buenas prácticas

- **Permisos restrictivos por defecto.** Solo auto-aprueba tools read-only.
- **Un MCP server, un dominio.** Mejor varios servers especializados que uno monstruoso.
- **Variables de entorno para secretos.** Nunca hardcodees API keys en `settings.json` — usa `env`.
- **Empieza con los oficiales.** Antes de escribir el tuyo, mira si ya existe uno en el [registro](https://github.com/modelcontextprotocol/servers).
- **Versiona la configuración del proyecto.** Si todo el equipo necesita el mismo MCP, va en `.claude/settings.json` del repo.

## Recursos

- [Especificación de MCP](https://modelcontextprotocol.io)
- [Registro de MCP servers](https://github.com/modelcontextprotocol/servers)
- [Documentación de MCP en Claude Code](https://docs.claude.com/en/docs/claude-code/mcp)
