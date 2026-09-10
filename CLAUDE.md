# Científica Movi — sitio de la droguería

Migración de un mockup hecho en v0 (Next.js/React) a Astro estático, para producción real en Hostinger.

## Stack y por qué

- Astro puro, sin framework de UI (React/Vue/Svelte). El sitio es una sola página informativa: sin backend, sin formularios, sin CMS — el contacto es un link a WhatsApp y números de teléfono (`tel:`), no hay mail de contacto.
- El único JS del sitio es un `<script>` vanilla al final de `src/pages/index.astro` que abre/cierra el menú móvil. No agregues un framework de UI salvo que aparezca una necesidad real de interactividad compleja — no la había al migrar y probablemente siga sin haberla.
- CSS plano en `src/styles/global.css`, sin Tailwind ni utility classes — todo con clases propias (`.hero`, `.site-header`, etc.) y variables CSS (`--navy`, `--cyan`, `--muted`, etc.) definidas en `:root`. Seguí ese estilo al agregar CSS nuevo.

## Estructura

- `src/pages/index.astro`: única página del sitio. El contenido (líneas de productos, hitos históricos, métricas, infraestructura) vive en arrays dentro del frontmatter del archivo — es donde se edita texto.
- `src/components/`: piezas reutilizables (`WhatsAppIcon.astro`, `ArrowIcon.astro`, `SectionIntro.astro`, `InfoBlock.astro`, `ContactRow.astro`, `SocialLinks.astro` + `IconInstagram.astro`/`IconLinkedIn.astro`/`IconFacebook.astro`), reflejo de los sub-componentes que tenía el `.tsx` original en v0. `InfoBlock` acepta `index` (numérito fantasma), `title` y `tags` (chips) además del texto del slot — se usa para las cards "Mayorista"/"Minorista" de la sección Cobertura. `SocialLinks` acepta `light` para la variante sobre fondo oscuro (footer) y trae hardcodeadas las tres URLs de redes.
- `public/images/`: `movi-logo.png` y `movi-logo-white.png` (header y footer), y la foto del hero como `movi-building.webp` — solo webp, sin fallback a PNG (decisión deliberada, no reintroducir un `.png`). Favicons (`favicon.ico`, PNGs en varios tamaños, `apple-touch-icon.png`) viven sueltos en `public/`, no en `public/images/`.
- El mapa de la sección Cobertura (`#cobertura`) es un SVG con los límites reales de Corrientes, Chaco, Formosa, Misiones y el norte de Santa Fe, generado a partir del geojson de [alvarezgarcia/provincias-argentinas-geojson](https://github.com/alvarezgarcia/provincias-argentinas-geojson) (proyección simple + simplificación Douglas-Peucker para look bajo-poligonal, Santa Fe recortada a la franja norte). Si hay que tocar los límites, regenerar desde el geojson en vez de editar las coordenadas del `d` a mano.

## Herramientas

- Plugin de Claude Code `frontend-design@claude-plugins-official` instalado (scope user). Es un skill que se activa solo en tareas de frontend para empujar decisiones de diseño más distintivas (tipografía, color, detalle visual) — no agrega dependencias ni frameworks al proyecto, sigue aplicando todo lo de "Stack y por qué" de arriba.

## Referencia de diseño

El mockup original de v0 (Next.js/React, con exactamente el mismo diseño) vive en el repo hermano `../movivercel` — solo local, sin remoto. Es la referencia visual si hay dudas de cómo se veía algo originalmente. No se despliega ni se modifica.

## Deploy — cómo funciona, importante

- Repo en GitHub: `https://github.com/DiegoSuarez09/movi-astro`, rama `master`.
- Cada `git push` a `master` dispara un build + deploy automático en Hostinger vía su **Node.js App Deployer** ("Deploy Now"): detecta Astro solo, corre `npm run build`, sirve el contenido de `dist/` en `public_html`.
- **No existe una rama `deploy` separada.** Se probó ese patrón (rama con solo el output compilado + git worktree) pensando que hacía falta, y se descartó al confirmar que Hostinger compila él mismo desde el código fuente en `master`. No reintroducir ese patrón salvo que cambie el mecanismo de hosting.
- El sitio en `master` con push exitoso queda en producción inmediatamente — no hay ambiente de staging.
- URL temporal en producción: `https://lightblue-eland-599476.hostingersite.com/`. Dominio propio: aún no conectado.

## Workflow con Diego

- Prueba cambios en local con `npm run dev` antes de pushear.
- Para cambios grandes o riesgosos, conviene trabajar en una rama aparte y mergear a `master` recién cuando esté validado visualmente (dado que `master` deploya automático a producción).
- Prefiere correr los comandos de terminal él mismo para aprender el flujo — dale el comando exacto con una breve explicación de qué hace, en vez de ejecutarlo directamente, salvo que pida lo contrario. Escribir/editar los archivos de código sí es tarea de Claude.

## Development

When starting the dev server, use background mode:

```
astro dev --background
```

Manage the background server with `astro dev stop`, `astro dev status`, and `astro dev logs`.

## Documentation

Full documentation: https://docs.astro.build

Consult these guides before working on related tasks:

- [Adding pages, dynamic routes, or middleware](https://docs.astro.build/en/guides/routing/)
- [Working with Astro components](https://docs.astro.build/en/basics/astro-components/)
- [Using React, Vue, Svelte, or other framework components](https://docs.astro.build/en/guides/framework-components/)
- [Adding or managing content](https://docs.astro.build/en/guides/content-collections/)
- [Adding styles or using Tailwind](https://docs.astro.build/en/guides/styling/)
- [Supporting multiple languages](https://docs.astro.build/en/guides/internationalization/)
