# Ruta Segura Tunja — PWA en JavaScript puro

Proyecto académico que demuestra tres estrategias de caché con Service Workers, cada una aplicada al tipo de recurso donde tiene más sentido:

1. **Cache Only:** los recursos propios del App Shell (CSS, JavaScript, ícono) se precargan durante `install` y se sirven siempre desde caché.
2. **Network with Cache Fallback:** las peticiones de navegación (cargar o recargar `index.html`) intentan primero la red para mostrar siempre la versión más reciente; si no hay conexión, se sirve la copia guardada en caché o, en su defecto, `offline.html`.
3. **Cache with Network Fallback:** las imágenes remotas no incluidas en el App Shell (fotos de los lugares alojadas en Unsplash) se buscan primero en caché para responder rápido; si no existen, se descargan de la red, se guardan en caché dinámico y se entregan al navegador.

## Requisitos

- Un navegador compatible con Service Workers, como Chrome, Edge o Firefox.
- Un servidor local. No abra `index.html` directamente con `file://`, porque el Service Worker requiere `localhost` o HTTPS.

## Ejecución con Visual Studio Code

1. Abra la carpeta del proyecto en VS Code.
2. Instale la extensión **Live Server**, si no la tiene.
3. Haga clic derecho sobre `index.html` y seleccione **Open with Live Server**.
4. Abra DevTools con `F12` y vaya a **Application > Service Workers**.

## Pruebas requeridas

### Prueba A: Cache Only

1. Abra la app conectado a Internet.
2. En DevTools, confirme que existe `ruta-segura-static-v1` en **Application > Cache Storage**.
3. En **Network**, active la opción **Offline**.
4. Recargue la página.
5. La interfaz, los filtros y los datos locales deben seguir disponibles.

### Prueba B: Cache with Network Fallback

1. Desactive Offline y recargue la aplicación.
2. Espere a que se carguen algunas imágenes de los lugares.
3. Revise `ruta-segura-dynamic-v3` en **Cache Storage**: deben aparecer recursos externos (dominio de Unsplash).
4. Active Offline y recargue.
5. Las imágenes ya consultadas deben mostrarse desde caché.
6. Las imágenes no visitadas mostrarán el ícono local como respaldo.

### Prueba C: Network with Cache Fallback

1. Con conexión activa, cargue la página y confirme que `index.html` queda guardado en `ruta-segura-static-v3` dentro de **Cache Storage**.
2. Modifique algo visible en `index.html` (por ejemplo, un texto) y recargue con conexión: debe verse el cambio de inmediato, porque la estrategia siempre intenta la red primero.
3. Active **Offline** en la pestaña **Network** y recargue la página.
4. Debe verse la última versión de `index.html` que quedó guardada en caché (no `offline.html`), porque la petición de navegación sí tenía una copia disponible.
5. Para comprobar el último respaldo, borre manualmente la entrada de `index.html` del caché estático, recargue sin conexión y verifique que ahora sí aparece `offline.html`.

## Actualización de recursos

Si modifica el App Shell, cambie `ruta-segura-static-v1` por una nueva versión, por ejemplo `ruta-segura-static-v2`, dentro de `sw.js`. Luego recargue la página. El evento `activate` elimina automáticamente las cachés de versiones anteriores.

## Estructura

```text
ruta-segura-tunja-pwa/
├── index.html
├── offline.html
├── manifest.json
├── sw.js
├── css/styles.css
├── js/app.js
├── js/datos.js
├── js/ui.js
├── img/icon.svg
└── README.md
```
