# TransFoam accordion

A dependency-free, desktop-first accordion intended for a single Tilda HTML block.

## Preview locally

Open `index.html` directly in a browser. No build step or local server is required.

## Add a card

Copy one complete `<article class="tf-accordion__item" data-accordion-item>` block. Change the visible number, titles, copy, image path, ARIA labels, the expanded panel `id`, its matching `aria-controls`, and the benefits heading `id`. No JavaScript changes are needed.

## Replace icons

Replace files in `assets/icons/`, or update the relevant `<img src="...">` attributes. Keep decorative image `alt` attributes empty. The bundled SVGs are placeholders and can be replaced without changing the layout.

## Local fonts

Local preview fonts are loaded from `Anta,Inter,Outfit/` through the `@font-face` declarations at the top of `index.html`. Inter and Outfit use their variable font files; Anta uses its regular font file. Tilda may continue using its own configured copies of these font families in production.

## Copy into Tilda

Copy the `<style>`, the complete `<section class="tf-accordion" data-accordion>`, and the `<script>` from `index.html` into one Tilda HTML block. Upload the icon files to a public location and replace each relative `./assets/icons/...` path with its hosted URL. Do not split the cards across multiple HTML blocks.
