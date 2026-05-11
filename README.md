# Analizador de Defectos en Paneles Solares

Aplicación web estática (HTML + JS puro, sin backend) que envía una imagen de paneles solares a un modelo de **Roboflow** para detectar defectos y muestra el resultado con las regiones marcadas y los porcentajes de confianza.

> **Proyecto TFM — Diagnóstico de fallos en paneles fotovoltaicos · UNIR 2026**

---

## ¿Qué hace?

- Subes una imagen (RGB, infrarroja o térmica, depende del modelo elegido).
- La app envía la imagen al **Hosted Inference API** de Roboflow.
- Devuelve una lista de objetos detectados (clase + confianza %) y dibuja recuadros sobre la imagen.
- Un panel de administrador permite **añadir varios modelos** de Roboflow para que el usuario final elija con cuál inferir.

---

## Requisitos previos (todo gratis)

| # | Recurso | Para qué | Coste |
|---|---------|----------|-------|
| 1 | Cuenta en **Roboflow** ([roboflow.com](https://roboflow.com)) | Obtener la API Key y acceder a modelos | Gratis |
| 2 | Cuenta en **GitHub** ([github.com](https://github.com)) | Alojar y publicar la web | Gratis |
| 3 | Modelo(s) Roboflow Universe | Inferencia | Gratis (con créditos) |
| 4 | Navegador moderno | Probar la app | — |

---

## Datos que tienes que recopilar de cada modelo Roboflow

Abre la URL del modelo en tu navegador, p. ej.:
`https://universe.roboflow.com/solar-panel-iewzf/solar-panel-w3f7s/model/7`

Anota:

- **Workspace** → `solar-panel-iewzf` *(la primera parte después de `/`)*
- **Project ID (slug)** → `solar-panel-w3f7s` *(la segunda parte)*
- **Versión** → `7` *(el número final)*

Estos tres valores son lo único que necesita la web para llamar al modelo. (El workspace es informativo: la API solo usa project + version + tu API key.)

---

## Cómo obtener tu API Key de Roboflow

1. Entra a `roboflow.com` y haz login.
2. Arriba a la derecha → **Settings** → pestaña **API Keys**.
3. Copia tu **Private API Key**.

> ⚠️ Esta key queda guardada en `localStorage` del navegador. Para un PoC académico es aceptable. No subas la key al repositorio de GitHub.

---

## Despliegue — Camino recomendado: GitHub Pages

### Paso 1 — Crear el repositorio

1. Entra a [github.com](https://github.com) → botón **New** (verde) para crear un repositorio.
2. Nombre: `analizador-paneles-solares` (o el que prefieras).
3. **Public** (necesario para que GitHub Pages funcione en cuenta gratis).
4. Marca **Add a README file**.
5. Crea el repositorio.

### Paso 2 — Subir el fichero `index.html`

1. En la página del repositorio recién creado, pulsa **Add file → Upload files**.
2. Arrastra el `index.html` desde tu ordenador.
3. Mensaje de commit (cualquier cosa, p. ej. `add app`) y pulsa **Commit changes**.

### Paso 3 — Activar GitHub Pages

1. En el repositorio, pestaña **Settings**.
2. Menú lateral izquierdo → **Pages**.
3. En **Source**, elige **Deploy from a branch**.
4. En **Branch**, selecciona `main` (carpeta `/ (root)`) y pulsa **Save**.
5. Espera ~1-2 minutos. Al recargar la página verás un recuadro con la URL pública del tipo:
   `https://<tu-usuario>.github.io/analizador-paneles-solares/`

### Paso 4 — Configurar la app

1. Abre la URL pública.
2. Despliega **⚙️ Configuración (administrador)**.
3. Pega tu **API Key** y pulsa *Guardar API Key*.
4. Rellena los datos del primer modelo y pulsa *Añadir modelo*. Repite para cuantos modelos quieras.
5. Sube una imagen → selecciona el modelo → pulsa *Analizar imagen*.

¡Listo!

---

## Camino alternativo aún más simple: Netlify Drop

Si te resulta más fácil que GitHub:

1. Ve a [app.netlify.com/drop](https://app.netlify.com/drop) (te pedirá registro gratuito la primera vez).
2. Arrastra el fichero `index.html` (o la carpeta que lo contiene).
3. Netlify te da una URL pública en segundos del tipo `https://random-name-12345.netlify.app`.

Las dos opciones son gratuitas y suficientes para un PoC.

---

## Ejemplo de descripciones por clase

Cuando añades un modelo, puedes pegar (opcional) un JSON con descripciones que se mostrarán junto a cada detección. Ejemplo:

```json
{
  "hotspot": "Punto caliente — posible cortocircuito en una celda. Inspección térmica recomendada.",
  "soiling": "Suciedad acumulada sobre el panel. Programar limpieza.",
  "crack": "Fisura visible. Riesgo de degradación acelerada.",
  "bird-dropping": "Excremento de ave. Limpieza puntual."
}
```

Las claves del JSON deben coincidir **exactamente** con los nombres de clase que devuelve el modelo Roboflow.

---

## Estructura del fichero

Todo vive en un único `index.html` con:

- HTML (estructura)
- CSS embebido (estilos en `<style>`)
- JavaScript embebido (lógica en `<script>` al final)

No hay dependencias externas, no hay build, no hay Node, no hay Python. Solo abrir el fichero en un navegador (o servirlo desde GitHub Pages) funciona.

---

## Plantilla para documentar tus modelos en el TFM

Como no he podido extraer los datos de las URLs que me diste (Roboflow Universe bloquea el acceso automatizado), abre tú las páginas y copia estos campos para cada modelo. Sirven también como referencia para la web:

```
Modelo: __________________________________________
URL dataset: _____________________________________
URL modelo: ______________________________________

Tipo de imagen (RGB / IR / térmica): _____________
Nº total de imágenes: ____________________________
División train/valid/test: _______________________
Clases (lista completa con conteos): _____________
   - clase_1 (n imágenes)
   - clase_2 (n imágenes)
   - ...
Tipo de tarea (object detection / classification / segmentation): __

== Métricas del modelo (pestaña "Versions" → "Model" en Roboflow) ==
mAP @50: ____  
Precisión: ____  
Recall: ____  
F1 (si aparece): ____  
Arquitectura (YOLOv8, YOLOv11, etc.): ____  
Tamaño de entrada: ____  

== Para la web ==
Workspace: _______________________________________
Project ID (slug): _______________________________
Versión: _________________________________________
```

---

## Límites y consideraciones

- **Cuota gratuita de Roboflow**: la capa gratis incluye créditos para inferencia hosted. Para pruebas durante el TFM va sobrado, pero conviene mirar el contador en `roboflow.com → Usage`.
- **Tamaño de imagen**: la API hosted suele tener un límite por petición. Si recibes errores con imágenes muy grandes, redimensiónalas antes (p. ej. a 1280 px en el lado largo).
- **CORS al pegar URL de imagen**: si la URL apunta a un servidor que no permite CORS, la inferencia funciona igualmente (Roboflow descarga la imagen por su cuenta), pero el dibujo en el lienzo del navegador puede fallar. En ese caso usa la opción de subir el fichero.
- **API Key visible**: como la página es estática, la key viaja en el código del navegador. Para producción real conviene poner un pequeño backend que proxy las llamadas y guarde la key en variables de entorno. Para el PoC del TFM no es necesario.
