# Contexto del repo

Página personal de Diego Díaz López como **Anthropic Education Ambassador** en
Madrid. Es un único rol (no es "Community Lead"). Sitio estático de un solo fichero
(`index.html`) publicado con GitHub Pages.

No confundir con **claudemadrid.com**, que es la página de la comunidad. Esta es
la página *personal* de Diego: recursos que recomienda, contacto y sus charlas.
Cuando haya que hablar de eventos, se enlaza a claudemadrid.com, no se duplican
aquí.

## Cómo está hecho

- Un único `index.html`. Todo el CSS y el JS van dentro. Sin build, sin
  dependencias, sin framework.
- Bilingüe ES/EN: cada texto existe dos veces, en elementos hermanos con
  `data-l="es"` y `data-l="en"`. El CSS oculta el idioma que no toca y el
  selector del nav cambia `data-lang` en `<html>`. **Al añadir cualquier texto
  visible hay que añadir las dos versiones**, o ese texto desaparecerá en uno de
  los dos idiomas.
- Los `<option>` del formulario llevan también `data-l`; el JS los desactiva
  en lugar de ocultarlos (los navegadores no ocultan `<option>` de forma fiable).
- Dependencias externas: la fuente Instrument Serif de Google Fonts y el script
  de GoatCounter (`diegodl-ambassador`, analítica anónima sin cookies, compartida
  con `diego-thebridge.github.io`). El footer dice que se cuentan visitas así; si
  se cambia de herramienta, cambiar también ese texto.
- Los PDFs de las charlas **no** viven aquí: se enlazan a
  `https://diego-thebridge.github.io/`. Si se mueven, actualizar esos enlaces.

## Portadas de las charlas

`covers/<slug>.jpg` es la **primera página** de cada PDF, a 600 px de ancho. Se
regeneran con poppler:

```bash
pdftoppm -f 1 -l 1 -r 60 -jpeg -jpegopt quality=82 -singlefile charla.pdf covers/slug
```

El `slug` del JPG tiene que coincidir con el nombre del PDF en
`diego-thebridge.github.io`. Al pinchar, un `<dialog>` abre el PDF en un iframe;
en móvil y en pantallas por debajo de 760 px se abre en una pestaña, porque los
navegadores móviles no dibujan PDFs dentro de un iframe.

Los dos sitios están en el mismo dominio (`diego-thebridge.github.io`), así que
el iframe no tiene problema de origen cruzado. Si los PDFs se mueven a otro
dominio, comprobar que ese dominio no mande `X-Frame-Options`.

## Radar (noticias.js)

La sección "Radar" se pinta desde `noticias.js`, que define `window.RADAR` y se
carga con una etiqueta `<script>`. Para añadir una entrada, basta con meter un
objeto al principio del array `entradas` y actualizar `actualizado`. No se toca
el HTML.

```json
{
  "fecha": "2026-09-30",
  "titulo": "Titular corto, en español",
  "fuente": "Quién lo publica",
  "url": "https://...",
  "es": "Tu comentario: por qué le importa a quien lee. Una o dos frases.",
  "en": "The same comment in English."
}
```

Reglas de la sección, que son lo que la hace útil:

- **El comentario es el producto.** Si no hay una frase propia que aporte algo,
  la entrada no entra. Resumir el titular no vale.
- Orden cronológico inverso. Entre cinco y ocho entradas visibles; al añadir,
  quitar las de abajo.
- `fecha` es la de la **fuente**, no la del día en que se añade.
- Encuadre personal: "esto me ha parecido interesante", nunca "Anthropic
  anuncia" en tono de canal oficial. La página lleva el título de Ambassador y
  no puede parecer un boletín de Anthropic.
- **Nada que venga de los canales internos del programa** antes de ser público.
  Está cubierto por el NDA.
- Verificar el enlace y la fecha antes de publicar. Si la fuente es secundaria
  y no se localiza el estudio o el anuncio original, no se publica.

Se carga con `<script src="noticias.js">` y no con `fetch` a propósito: así la
sección se ve igual publicada y abriendo el `index.html` en local con doble
clic. Si `noticias.js` falta o tiene un error de sintaxis, el Radar no aparece
y el resto de la página sigue funcionando.

Hay una tarea programada que cada dos semanas propone tres entradas candidatas
ya redactadas. Diego aprueba o descarta; quien publica es él.

## Formulario

Va a Formspree por `fetch`. El endpoint está en el `action` del `<form>` y es
público por diseño — no es un secreto. Es el mismo formulario que usa
`diego-thebridge.github.io`; el campo oculto `pagina=ambassador` distingue los
envíos de esta página. Si aparece el marcador
`PON_AQUI_TU_ID_FORMSPREE`, el formulario no está conectado todavía y el botón
muestra un aviso en lugar de fallar en silencio.

**Los datos del formulario no entran en ningún CRM ni herramienta de marketing.**
Solo email de respuesta. Esto está escrito en la propia página y es una promesa
al visitante; no cambiar sin cambiar también el texto.

## Reglas de marca — leer antes de tocar textos o imágenes

Esto es una página de un Ambassador voluntario, no de Anthropic. El Ambassador
Handbook de Claude Community es la fuente; lo que sigue es el resumen aplicable
a este repo:

1. **Nada de marca Anthropic.** Ni el nombre como titular, ni el logo, ni el
   logo o wordmark de Claude en solitario, ni la tagline "Keep Thinking".
2. **El lockup "Claude Community [Ciudad]" es para assets de la comunidad**, no
   para esta página. Aquí no se pone el wordmark en arco. Si algún día se pone,
   deja de ser una página personal y pasa a necesitar visto bueno de
   community@anthropic.com.
3. **Cero logos de terceros**: ni The Bridge, ni patrocinadores, ni sedes. Un
   logo ajeno junto a la marca Claude se lee como una alianza oficial.
4. **Decir siempre que el rol es voluntario** y que las opiniones son propias.
   Ya está en el hero y en el footer; que no se caiga.
5. **Nada de acceso preferente.** Esta página no puede ofrecer plazas
   reservadas, inscripción anticipada ni ninguna ventaja de entrada a los
   eventos de la comunidad por escribir aquí o por ser cliente de nada. Es una
   de las cuatro causas de expulsión del programa.
6. **Nada de promoción comercial** de The Bridge ni de sus productos apoyándose
   en el rol de Ambassador. Se puede decir dónde trabaja; no vender desde aquí.
7. **Un asset gráfico compuesto** (banner, portada, ilustración) hecho fuera del
   kit oficial necesita visto bueno de community@anthropic.com **antes** de
   publicarse. Por eso ahora mismo la página es puramente tipográfica.

Si el plugin `claude-community-events` está instalado, pasar la skill
`brand-check` antes de publicar cambios de copy o cualquier imagen nueva.

## Publicar

`main` se despliega solo con GitHub Pages (Settings → Pages → Deploy from a
branch → `main` / root). Para comprobar en local:

```bash
python3 -m http.server 8000
# http://localhost:8000
```

Revisar siempre en los dos idiomas y a ~390 px de ancho antes de hacer push.
