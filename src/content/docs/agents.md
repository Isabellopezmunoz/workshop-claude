---
title: Agentes de IA
description: Qué son los agentes de IA, cómo se construyen y cómo se diferencian de un chatbot.
order: 5
icon: lucide:bot
---

## ¿Qué es un agente de IA?

Un **agente de IA** es un sistema en el que un modelo de lenguaje (LLM) **toma decisiones de forma autónoma** sobre qué hacer a continuación para cumplir un objetivo. En lugar de generar una única respuesta y terminar, el agente puede **usar herramientas, leer resultados, replanificar y volver a actuar** tantas veces como haga falta.

> Un chatbot responde. Un agente actúa.

La diferencia con un chatbot tradicional no está en el modelo en sí, sino en lo que le rodea: las **tools** que puede usar, el **loop** que le permite iterar y la **autonomía** para decidir cuándo ha terminado.

## Chatbot vs agente

|                 | Chatbot                              | Agente                                                    |
| --------------- | ------------------------------------ | --------------------------------------------------------- |
| **Flujo**       | Pregunta → respuesta → fin           | Objetivo → razonar → actuar → observar → repetir          |
| **Tools**       | No usa (solo genera texto)           | Usa varias (leer archivos, ejecutar código, llamar APIs…) |
| **Iteraciones** | Una por mensaje del usuario          | Tantas como necesite hasta cumplir el objetivo            |
| **Estado**      | Solo el historial de la conversación | Mantiene contexto de lo que ha hecho y observado          |
| **Decisiones**  | Qué decir                            | Qué decir + qué hacer + cuándo parar                      |

Claude Code, por ejemplo, **es un agente**: cuando le pides "arregla este bug", no te contesta con una sugerencia y ya está — lee archivos, ejecuta tests, edita código, vuelve a ejecutar, y solo termina cuando el bug está arreglado.

## Las piezas de un agente

Todo agente, por simple que sea, tiene cuatro componentes:

### 1. El modelo

El cerebro. Un LLM como Claude, GPT, Gemini, etc. Recibe el contexto y decide qué hacer.

### 2. Las tools

Funciones que el modelo puede llamar para **interactuar con el mundo real**: leer archivos, ejecutar SQL, llamar a una API, mandar un email, abrir el navegador… Cada tool tiene un nombre, una descripción y un schema de parámetros.

### 3. El loop agentico

El bucle que ejecuta la lógica:

```
mientras no haya terminado:
    1. enviar contexto al modelo
    2. recibir su respuesta
    3. ¿pide usar una tool?
        sí → ejecutar tool, añadir resultado al contexto, repetir
        no → terminar
```

Sin este loop, no hay agente — sería solo una llamada normal al modelo.

### 4. El prompt del sistema

Las instrucciones iniciales que definen **qué es el agente, qué tools tiene, cómo debe comportarse, cuándo terminar**. Es lo que diferencia a un agente "code reviewer" de un "data analyst" usando el mismo modelo y el mismo loop.

## El loop agentico paso a paso

Es el corazón de todo agente. Veámoslo con un ejemplo concreto:

> **Objetivo del usuario:** "¿Cuántos usuarios se registraron ayer?"

**Iteración 1**

- El modelo recibe el prompt del sistema + la pregunta.
- Decide: _"Necesito consultar la base de datos."_
- Pide usar la tool `query_db` con `SELECT count(*) FROM users WHERE created_at::date = '2026-05-24'`.

**El harness ejecuta la tool**

- Resultado: `{ count: 247 }`.

**Iteración 2**

- El modelo recibe todo el contexto anterior + el resultado de la tool.
- Decide: _"Ya tengo la información, puedo responder."_
- Responde: _"Ayer se registraron 247 usuarios."_
- Termina el loop.

Lo importante: **el modelo no sabe SQL de memoria del usuario**. Lo decide en cada iteración basándose en el contexto. Si la primera query falla, el modelo lo verá en el resultado, lo corregirá y lo intentará de nuevo.

## Tipos de agentes según su autonomía

No todos los agentes tienen el mismo nivel de libertad. De menos a más:

### Workflow guiado

El humano define los pasos por adelantado y el modelo solo decide algunos detalles dentro de cada paso. Ejemplo: una pipeline donde primero se extraen datos, luego se resumen, luego se mandan. El modelo solo elige el contenido del resumen.

### Agente con tools

El humano define las tools disponibles y el objetivo. El modelo decide qué tools usar y en qué orden. La mayoría de agentes útiles caen aquí (Claude Code, agentes de soporte, asistentes de búsqueda…).

### Agente multi-paso autónomo

El modelo planifica su propio plan, lo ejecuta y se autoevalúa. Más potente pero más impredecible — necesita más salvaguardas (timeouts, límites de iteraciones, revisión humana).

### Multi-agente

Varios agentes cooperan, cada uno especializado en un dominio. Un "orquestador" coordina, y los "subagentes" ejecutan tareas concretas. Es lo que hacen plataformas como AutoGPT o el sistema de subagents de Claude Code.

## Construir tu primer agente con el SDK de Anthropic

Vamos a construir el agente más simple posible: uno que sabe consultar la hora actual. Solo necesitas el SDK de Anthropic.

### 1. Instala el SDK

```bash
npm install @anthropic-ai/sdk
```

### 2. Define una tool

Una tool es una función con un nombre, descripción y schema:

```ts
const tools = [
  {
    name: 'get_current_time',
    description: 'Returns the current date and time in ISO format',
    input_schema: {
      type: 'object',
      properties: {},
    },
  },
]
```

### 3. Implementa la tool en código

```ts
function executeTool(name, input) {
  if (name === 'get_current_time') {
    return new Date().toISOString()
  }
  throw new Error(`Unknown tool: ${name}`)
}
```

### 4. Escribe el loop agentico

```ts
import Anthropic from '@anthropic-ai/sdk'

const client = new Anthropic()

async function runAgent(userMessage) {
  const messages = [{ role: 'user', content: userMessage }]

  while (true) {
    const response = await client.messages.create({
      model: 'claude-sonnet-4-6',
      max_tokens: 1024,
      tools,
      messages,
    })

    messages.push({ role: 'assistant', content: response.content })

    if (response.stop_reason === 'end_turn') {
      const finalText = response.content.find((block) => block.type === 'text')
      return finalText?.text
    }

    if (response.stop_reason === 'tool_use') {
      const toolResults = response.content
        .filter((block) => block.type === 'tool_use')
        .map((toolUse) => ({
          type: 'tool_result',
          tool_use_id: toolUse.id,
          content: JSON.stringify(executeTool(toolUse.name, toolUse.input)),
        }))

      messages.push({ role: 'user', content: toolResults })
    }
  }
}

const answer = await runAgent('¿Qué hora es?')
console.log(answer)
```

### 5. Ejecuta

El agente:

1. Recibe `¿Qué hora es?`.
2. Decide usar `get_current_time`.
3. El loop ejecuta la tool y devuelve el resultado.
4. El modelo formatea la respuesta: _"Son las 14:32 del 25 de mayo de 2026"_.
5. Termina.

Ese es **el patrón base de cualquier agente**. Añadir más tools (leer archivos, ejecutar SQL, llamar APIs) es solo extender este mismo loop.

## Patrones comunes

### Tool routing

Con muchas tools, el modelo elige la correcta según la intención del usuario. Mejor tener **pocas tools bien descritas** que muchas ambiguas — si dos tools podrían servir, el modelo elegirá mal.

### Multi-step planning

Para tareas complejas, pide al modelo que **planifique primero** (en texto) antes de empezar a ejecutar. Esto reduce errores y te permite auditar el plan antes de que el agente actúe.

### Verificación y feedback

Después de cada acción importante, hacer que el modelo **observe el resultado** y decida si hay que corregir. Es el patrón "act → observe → reflect" que usan los agentes más fiables.

### Subagentes

Cuando una tarea es demasiado grande para una sola conversación, divídela en sub-tareas y lanza **subagentes especializados** para cada una. Cada subagente trabaja en un contexto aislado y devuelve un resumen. Claude Code permite esto con la tool `Agent`.

## Buenas prácticas

- **Empieza simple.** Un agente con dos tools que funciona es mejor que uno con veinte que se confunde. Añade tools solo cuando notes que faltan.
- **Descripciones claras en las tools.** El modelo elige basándose en la `description`. Si es ambigua, elegirá mal.
- **Limita el número de iteraciones.** Ponle un `max_iterations` al loop para evitar que un agente se quede atascado en bucle infinito.
- **Loggea todo.** Cada iteración debería dejar rastro: qué decidió el modelo, qué tool usó, qué resultado obtuvo. Sin logs, debuggear un agente es imposible.
- **Permisos restrictivos.** No le des al agente capacidades destructivas (borrar archivos, ejecutar pagos) sin un humano en el loop confirmando.
- **Prompts cortos y precisos.** El system prompt del agente define su personalidad y límites. Cuanto más claro, más predecible será su comportamiento.
- **Evalúa en producción.** Los agentes fallan en formas raras. Ten un sistema para revisar conversaciones reales y detectar patrones de fallo.

## Cuándo NO usar un agente

No todo problema necesita un agente. Si tu tarea cabe en una sola llamada al modelo, **úsala así** — un agente añade latencia, coste y complejidad.

Casos donde un agente está sobre-diseñado:

- Clasificación o extracción simple ("¿este email es spam?").
- Resúmenes o traducciones.
- Generación de texto sin tools.
- Cualquier tarea que no necesite ejecutar acciones en el mundo.

Usa un agente solo cuando **el modelo necesite tomar decisiones iterativas que dependan de información que aún no tiene**.

## Recursos

- [Documentación oficial de Tool Use](https://docs.claude.com/en/docs/agents-and-tools/tool-use/overview)
- [Claude Agent SDK](https://docs.claude.com/en/api/agent-sdk/overview) — la misma base que usa Claude Code, para construir tus propios agentes.
- [Anthropic Cookbook — Agents](https://github.com/anthropics/anthropic-cookbook) — ejemplos de código listos para correr.
- [Building Effective Agents (Anthropic Engineering)](https://www.anthropic.com/research/building-effective-agents) — guía sobre patrones de diseño de agentes.
