---
title: Hooks
description: Automatiza acciones en respuesta a eventos del agente — formato, linters, notificaciones o lo que necesites.
order: 2
icon: lucide:webhook
---

## ¿Qué es un hook?

Un **hook** es un comando de shell que Claude Code ejecuta automáticamente cuando ocurre un evento concreto del agente: antes de usar una tool, después de editar un archivo, al terminar una respuesta, etc.

Los hooks son la forma de **reaccionar a lo que hace Claude sin tener que pedírselo**. Por ejemplo: cada vez que edita un archivo `.ts`, lanza Prettier automáticamente.

> Los hooks los ejecuta el **harness** (la propia CLI), no Claude. Eso significa que son **deterministas**: siempre se disparan cuando se cumple la condición, sin importar lo que decida el modelo.

## Cuándo usar hooks

Los hooks son ideales para:

- **Automatizar formato y linting** — Prettier, ESLint, Black, gofmt al editar archivos.
- **Generar tipos o assets** — recompilar tipos de TypeScript, regenerar tokens de diseño.
- **Notificaciones** — mandarte un mensaje cuando Claude termina una tarea larga.
- **Validaciones** — bloquear commits si los tests fallan, impedir edición de archivos sensibles.
- **Logging** — registrar qué tools usa Claude para auditoría.

**No uses hooks para** cosas que el propio Claude puede hacer (escribir código, decidir qué archivo editar). Los hooks son para **acciones mecánicas y predecibles**.

## Tipos de eventos

Los hooks se enganchan a eventos del agente. Los principales son:

- **`PreToolUse`** — antes de que Claude ejecute una tool. Puedes bloquearla.
- **`PostToolUse`** — después de que la tool termine. Útil para formatear lo que acaba de editar.
- **`UserPromptSubmit`** — cada vez que el usuario manda un mensaje.
- **`Stop`** — cuando Claude termina su respuesta.
- **`Notification`** — cuando el agente pide permisos o atención.

Cada evento puede recibir filtros (`matcher`) para que solo se dispare en condiciones concretas.

## Cómo se configuran

Los hooks viven en `settings.json`, normalmente en `~/.claude/settings.json` (global) o `.claude/settings.json` (proyecto):

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "echo \"Claude acaba de editar un archivo\""
          }
        ]
      }
    ]
  }
}
```

Estructura:

- **Evento** (`PostToolUse`, `PreToolUse`, etc.) como clave principal.
- **`matcher`** — patrón que filtra cuándo aplicar el hook. Puede ser nombre de tool, regex, etc.
- **`hooks`** — lista de comandos a ejecutar.

## Ejemplo 1 — Prettier automático

Cada vez que Claude edita un archivo, formatéalo con Prettier:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "npx prettier --write \"$CLAUDE_TOOL_INPUT_FILE_PATH\""
          }
        ]
      }
    ]
  }
}
```

El hook recibe variables de entorno con info de la tool que se acaba de ejecutar — en este caso `$CLAUDE_TOOL_INPUT_FILE_PATH` con la ruta del archivo editado.

## Ejemplo 2 — Notificación cuando termina

Manda una notificación de macOS cuando Claude acaba una respuesta:

```json
{
  "hooks": {
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "osascript -e 'display notification \"Claude ha terminado\" with title \"Claude Code\"'"
          }
        ]
      }
    ]
  }
}
```

Muy útil cuando lanzas tareas largas y quieres saber cuándo volver a la terminal.

## Ejemplo 3 — Bloquear edición de archivos sensibles

Antes de que Claude edite cualquier `.env`, aborta:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "case \"$CLAUDE_TOOL_INPUT_FILE_PATH\" in *.env*) echo 'No edites archivos .env' >&2; exit 1 ;; esac"
          }
        ]
      }
    ]
  }
}
```

Si el hook devuelve `exit 1` con un `PreToolUse`, la tool no se ejecuta.

## Buenas prácticas

- **Hooks rápidos.** Bloquean al agente mientras se ejecutan. Mantenlos por debajo de 1-2 segundos.
- **Idempotentes.** El mismo hook puede dispararse muchas veces — asegúrate de que no rompe nada si se ejecuta de más.
- **Salida mínima.** Lo que escriba el hook a `stdout` se envía a Claude como contexto. No spammees.
- **Errores explícitos.** Si quieres bloquear una acción, escribe el motivo a `stderr` antes del `exit 1`.
- **Empieza global, mueve a proyecto.** Los hooks útiles para todos los proyectos van en `~/.claude/settings.json`. Los específicos, en `.claude/settings.json` del repo.

## Recursos

- [Documentación oficial de Hooks](https://docs.claude.com/en/docs/claude-code/hooks)
