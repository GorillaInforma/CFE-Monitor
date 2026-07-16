# CFE Monitor — versión React (Vite)

Esta es la app original (un solo `index.html` con HTML + CSS + JS) empaquetada
como proyecto React con [Vite](https://vitejs.dev), lista para desplegar en
Vercel.

## Cómo se organizó

No se reescribió la lógica desde cero (son ~2400 líneas de motor de cálculo,
OCR, onboarding, etc.). En vez de eso, se hizo un "port" fiel:

```
public/
  legacy/
    engine.js       ← motor principal (tasas, proyecciones, historial)
    onboarding.js   ← wizard de alta, OCR del recibo (Tesseract/Groq), y
                      quien decide si arrancar el motor o mostrar el wizard
    pwa.js          ← registro del Service Worker + botón "Instalar app"
  manifest.json     ← manifest de la PWA (regenerado; ver "Pendientes")
  sw.js             ← Service Worker básico (ver "Pendientes")
  icon-192.png
  icon-512.png

src/
  legacy/
    body.html       ← todo el markup original (header, medidor, onboarding,
                      historial, etc.), tal cual, montado vía
                      dangerouslySetInnerHTML
  App.jsx           ← monta body.html y carga los 3 scripts de /legacy en
                      orden (igual que en el <script> original)
  main.jsx          ← punto de entrada de React
  index.css         ← todo el CSS original, extraído del <style>
```

`App.jsx` no reescribe el comportamiento: monta el HTML original y luego
inyecta `engine.js`, `onboarding.js` y `pwa.js` como `<script>` clásicos, en
el mismo orden en que corrían en el archivo original. Por eso siguen
funcionando `localStorage`, el OCR con Tesseract.js, y todo el resto sin
tocarles una línea de lógica.

## Pendientes / cosas a revisar

- **Íconos**: `icon-192.png` e `icon-512.png` son placeholders generados
  (fondo verde CFE + texto "CFE"). Cámbialos por el logo real si tienes uno.
- **Service Worker**: el `index.html` original ya registraba `./sw.js`, pero
  ese archivo no estaba entre lo que subiste, así que se creó uno básico
  (cache-first del shell). Ajústalo si tenías uno con otra lógica.
- **Clave de Groq (OCR con IA)**: si usas la extracción del recibo con Groq
  Vision, la clave se maneja igual que en el original (la sigues
  ingresando tú desde la UI); no se movió a variables de entorno.

## Desarrollo local

```bash
npm install
npm run dev
```

Abre lo que indique la terminal (normalmente http://localhost:5173).

## Build de producción

```bash
npm run build
npm run preview   # para probar el build localmente
```

## Desplegar en Vercel

**Opción A — con la CLI:**
```bash
npm i -g vercel
vercel        # sigue las instrucciones (preview)
vercel --prod # despliegue a producción
```

**Opción B — importando el repo en vercel.com:**
1. Sube esta carpeta a un repositorio (GitHub/GitLab/Bitbucket).
2. En [vercel.com](https://vercel.com) → "Add New… → Project" → importa el repo.
3. Vercel detecta Vite automáticamente (build: `npm run build`, output: `dist`).
   Esto ya viene preconfigurado en `vercel.json`.
4. Deploy.
