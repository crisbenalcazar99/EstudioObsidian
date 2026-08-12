---
tags: [indice]
---

# Índice de temas

Mapa de contenidos (MOC) del vault. Cada tema vive en `Temas/<NombreTema>/` con:

- `<NombreTema>.md` — nota índice del tema.
- `notas/` — notas de conocimiento individuales, escritas con [[Plantillas/Plantilla - Nota de Conocimiento|la plantilla estándar]].
- `recursos/` — archivos adjuntos (HTML, imágenes, PDFs, etc.).

## Temas

- [[Temas/SQLAlchemy/SQLAlchemy|SQLAlchemy]]

## Cómo agregar un nuevo tema

1. Crear `Temas/<NombreTema>/` con subcarpetas `notas/` y `recursos/`.
2. Crear la nota índice `Temas/<NombreTema>/<NombreTema>.md` (usar la de SQLAlchemy como referencia).
3. Enlazar el nuevo tema en este índice.

## Cómo agregar una nueva nota de conocimiento

1. Copiar [[Plantillas/Plantilla - Nota de Conocimiento]] dentro de `Temas/<NombreTema>/notas/`.
2. Renombrarla y completar las secciones.
3. Enlazarla desde la nota índice del tema (sección "Notas").