# La Ganga – Multi-Stream Dashboard para Kick

Un dashboard de código abierto para ver múltiples streams de Kick al mismo tiempo, con chat integrado y seguimiento de estado en vivo.

## ¿Qué hace?

- 🎥 **Multi-stream**: visualiza hasta 6 streams de Kick simultáneamente en un grid adaptable
- 💬 **Chat integrado**: panel lateral de chat de Kick (modo lectura, sin campo de texto)
- 🟢 **Estado en vivo**: muestra si cada streamer está en directo, con número de espectadores, categoría y tiempo al aire
- 🎬 **Modo cine**: expande uno o varios streams a pantalla completa con chat opcional
- 🔔 **Notificaciones Discord**: webhook opcional para recibir alertas cuando un streamer se conecta
- ⚙️ **Personalizable**: agrega, quita y reordena streamers; importa/exporta configuración en JSON
- 💾 **Persistencia local**: la configuración se guarda en `localStorage` (sin backend)

## Tecnologías

- **React 18** (vía CDN con UMD) + **Babel Standalone** para JSX en el navegador
- **API pública v1 de Kick**: `https://kick.com/api/v1/channels/<slug>`
- Sin frameworks CSS, sin bundler — todo en un único `index.html`

## Estructura del proyecto

```
la-ganga.github.io/
├── index.html        ← toda la aplicación (React + estilos inline)
├── README.md         ← esta documentación
└── .github/
    └── workflows/
        └── deploy.yml  ← workflow de GitHub Actions para despliegue en Pages
```

## Cómo funciona

### Estado en tiempo real

El estado en vivo se consulta usando la API pública oficial de Kick v1.  
Se actualiza automáticamente **cada 45 segundos**.

```
GET https://kick.com/api/v1/channels/<slug>
```

La respuesta incluye `livestream.viewer_count`, `followers_count`, categorías y `created_at`.

### Chat

El chat se incrusta como iframe apuntando a `https://kick.com/<slug>/chatroom`.  
El campo de texto de Kick se oculta mediante CSS para mostrar solo la lectura del chat.

### Persistencia

Toda la configuración (streamers, tema, webhooks, etc.) se guarda en `localStorage` bajo la clave `laganga_v6`.  
No hay base de datos ni backend.

---

## 🚀 Cómo clonar y modificar

### 1. Hacer fork / clonar

```bash
git clone https://github.com/Rickr18/la-ganga.github.io.git
cd la-ganga.github.io
```

### 2. Editar a tu gusto

Todo el código está en **`index.html`**. Puedes abrirlo directamente en un navegador o con un servidor local:

```bash
# Con Python (sin instalación extra)
python3 -m http.server 8080
# Luego visita http://localhost:8080
```

### Cambiar los streamers por defecto

Busca la constante `DEFAULT_STREAMERS` cerca del inicio del `<script>`:

```js
const DEFAULT_STREAMERS = [
  { id:'westcol',    name:'Westcol',  color:'#ec4899', avatar:'🔴' },
  { id:'willito',    name:'Willito',  color:'#facc15', avatar:'🟡' },
  // Agrega o quita streamers aquí
];
```

- **`id`**: slug del canal en Kick (lo que aparece en `kick.com/<id>`)
- **`name`**: nombre que se muestra en la UI
- **`color`**: color identificativo (hex)
- **`avatar`**: emoji o texto corto

### Cambiar el título y el acento de color

- **Título**: busca `pageTitle` en `defaultState` o edítalo desde el panel de administración
- **Color neón**: cambia `--neon: #53fc18` en el bloque `:root` de los estilos

### Agregar etiquetas personalizadas

Edita el objeto `GANG_TAGS`:

```js
const GANG_TAGS = {
  'westcol': 'W Reals',
  'micanal': 'MI TAG',   // ← agrega el tuyo
};
```

---

## 🌐 Despliegue en GitHub Pages

### Opción A: sin secretos (solo chat y streams públicos)

1. Haz un fork del repositorio
2. Ve a **Settings → Pages** y elige **GitHub Actions** como fuente
3. Haz `push` a `main` — el workflow lo desplegará automáticamente en `https://<tu-usuario>.github.io/<nombre-del-repo>/`

### Opción B: con integración OAuth de Kick

Si quieres habilitar el flujo OAuth2 (PKCE) para autenticación con Kick:

1. Ve a **Settings → Secrets and variables → Actions** en tu repositorio
2. Crea un secreto llamado `KICK_CLIENT_SECRET` con el valor de tu client secret de Kick
3. El workflow de deploy inyectará el secreto en el HTML antes de publicarlo
4. Registra esta URI de redirección en el portal de Kick:
   ```
   https://<tu-usuario>.github.io/<nombre-del-repo>/
   ```

> **Importante:** el secreto nunca queda en el código fuente ni en el historial de git.

---

## Licencia

Este proyecto es **open source**. Puedes usarlo, modificarlo y redistribuirlo libremente.

