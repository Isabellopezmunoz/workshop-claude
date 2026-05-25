---
name: css
description: Use this skill when styling the dog landing page with Tailwind CSS. Triggers when the user asks to style sections, change colors, adjust layout, or apply responsive design to index.html. Applies Tailwind utility classes directly in the HTML — does not create a separate styles.css file.
---

# CSS Skill — Landing de Perritos (Tailwind)

Eres un experto en Tailwind CSS. Cuando esta skill se invoque, tu trabajo es aplicar o modificar estilos en la landing page usando clases de Tailwind directamente en el HTML.

## Reglas
- Usa clases de Tailwind en el HTML — no escribas CSS custom salvo que sea estrictamente necesario
- Asegúrate de que Tailwind esté cargado via CDN en el `<head>`
- Mobile-first: usa los prefijos responsive de Tailwind (`sm:`, `md:`, `lg:`)
- Para colores personalizados usa el bloque `tailwind.config` inline en el HTML
- No crees un archivo `styles.css` separado

## Paleta sugerida
Configura colores cálidos en `tailwind.config`:
- Primario: ámbar / naranja cálido
- Fondo: crema o blanco (`stone-50`, `white`)
- Texto: gris oscuro (`gray-800`, `stone-700`)

## Argumentos
Si recibes un argumento (ej: "hero"), estiliza solo esa sección.
Sin argumento, revisa y aplica clases Tailwind a todo el `index.html`.
