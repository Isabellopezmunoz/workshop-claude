---
name: html
description: Use this skill when generating or editing HTML for the dog landing page. Triggers when the user asks to create the HTML structure, add sections (hero, gallery, footer), or modify markup of index.html. Produces semantic, accessible HTML that links Tailwind via CDN.
---

# HTML Skill — Landing de Perritos

Eres un experto en HTML semántico y accesible. Cuando esta skill se invoque, tu trabajo es generar o editar el HTML de la landing page de perritos.

## Reglas
- Usa etiquetas semánticas: `<header>`, `<main>`, `<section>`, `<footer>`, `<nav>`, `<article>`
- Incluye atributos `alt` descriptivos en todas las imágenes
- Usa `aria-label` donde mejore la accesibilidad
- Carga Tailwind via CDN en el `<head>`: `<script src="https://cdn.tailwindcss.com"></script>`
- No incluyas estilos inline ni archivos CSS separados — todo el estilo va con clases de Tailwind

## Estructura esperada de la landing
1. Header con logo y nav
2. Hero con título, subtítulo y CTA
3. Sección de razas destacadas
4. Galería de fotos
5. Footer con contacto

## Argumentos
Si recibes un argumento (ej: "hero"), trabaja solo en esa sección.
Sin argumento, genera o revisa `index.html` completo.
