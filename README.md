# Hero · Izipay Developers

Hero de la home de Izipay Developers: una MacBook con un IDE y la pasarela de pago real corriendo el flujo oficial de tarjeta, sobre una foto de fondo. En móvil, la pasarela va dentro de un iPhone.

**Demo:** https://izidev.vercel.app

Es HTML, CSS y JS vanilla en un solo archivo (`izipay-hero-v4.html`), sin build ni dependencias.

## Correr en local

Los assets se cargan con rutas relativas, así que hace falta un servidor (con `file://` no cargan las máscaras):

```bash
npx http-server -p 8777 -c-1
# http://localhost:8777/izipay-hero-v4.html
```

## Integrarlo en el sitio

El bloque a copiar está marcado en el HTML entre `HERO: copiar desde aquí` y `HERO: hasta aquí`. Con él van el `<style>` del `<head>` y la carpeta `assets/`.

- Todas las clases llevan el prefijo `izp-` (escritorio) o `izm-` (móvil), para no chocar con los estilos del sitio.
- La pasarela va encapsulada bajo `.izp-gw`.
- Fuentes: Roboto, JetBrains Mono, Nunito Sans y Open Sans, desde Google Fonts. La pasarela pide primero Museo Sans, la fuente de marca; si el sitio ya la carga, la usa.

## Cómo está armado

| Ancho | Vista |
|---|---|
| ≥ 1280 px | Escritorio: texto a la izquierda, escena a la derecha, escalada al alto de la sección |
| 640 – 1279 px | Tablet: texto arriba y escena recortada debajo |
| < 640 px | Móvil: iPhone con la pasarela a tamaño real. El tamaño del teléfono sale del alto visible, para que texto y teléfono entren sin scroll |

- **Escena:** es un escenario fijo de 1200×760 que `fit()` escala por JS. Los rects de la pantalla de la MacBook y del iPhone son las medidas exactas de los smart objects de los PSD, en porcentaje.
- **Tamaño del conjunto:** la variable CSS `--props` (por defecto `.8`) escala la laptop y las tarjetas sin tocar el fondo.
- **Una sola pasarela:** hay un único nodo `.izp-gw-wrap`. `place()` lo mueve entre la ventana del navegador de la laptop y la pantalla del iPhone al cruzar los 640 px, así que escritorio y móvil comparten estado.
- **Flujo animado (`run()`):** formulario vacío → se escriben los datos → botón habilitado → *Estamos procesando tu compra* → *Pago realizado con éxito* con cuenta regresiva → vuelve a empezar. Se pausa cuando el hero sale de pantalla. Con `prefers-reduced-motion` se muestra fijo el formulario lleno.
- **API de depuración:** `window.izipayHero.replay()` reinicia el flujo.
- **Solo tarjeta:** los recuadros de QR, Yape y Plin y el botón de Apple Pay se muestran como en el diseño, pero no son interactivos.

## Assets

| Archivo | Origen |
|---|---|
| `assets/pasarela/*.svg` | Figma *1. MD \| Pasarela de pago – desktop (prototipo)*, frames `tarjeta_01` a `tarjeta_03` (nombres = ids del export) |
| `assets/hero/gw-loading.svg`, `gw-success.svg`, `gw-clock.svg` | Mismo Figma, frames `tarjeta_06` (procesando) y `tarjeta_07` (pago exitoso) |
| `assets/hero/macbook.webp`, `macbook-glass.webp`, `macbook-mask.png` | Render de `Macbook_Air_M2_Mockup_3.psd`: equipo, capa *Highlights* y máscara de la pantalla |
| `assets/hero/iphone.webp`, `iphone-mask.png` | Render de `Free_iPhone_16_Mockup_5.psd`: equipo y máscara de la pantalla |
| `assets/hero/bg.webp` | Foto de fondo (pared con arco y mesa blanca), generada con IA |

Los PSD no están en el repo por su peso (~80 MB); pídelos si necesitas reexportar. Las máscaras usan el canal **alfa**, así que deben ser PNG RGBA.

## Placeholders a reemplazar

- **Snippet del IDE:** es ilustrativo (`@izipay/sdk`, `createPayment`, `pk_test_••••`). Hay que cambiarlo por el del SDK oficial, en las variables `head`, `tail` y `CARD` del script.
- **Datos de la tarjeta:** `4111 1111 1111 1111`, `06/23`, Lucho Torres, `luchotorres@izipay.pe`. Son los del Figma y están en `FORM` (atributos `data-v`).
- **Montos y números:** S/ 129.90, número de pedido y número de transacción.
- **Links de los CTA:** `#empezar` y `#documentacion`.

## Deploy

El repo está conectado a Vercel: cada push a `main` se publica en producción. `vercel.json` sirve el hero en la raíz, y `.gitignore` / `.vercelignore` funcionan como lista blanca, así que solo se suben el HTML, `assets/` y la config.
