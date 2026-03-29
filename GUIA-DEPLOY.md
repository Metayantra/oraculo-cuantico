# GUÍA DE DEPLOY — Oráculo Cuántico para Shopify

## Arquitectura

```
[Shopify Page] → <iframe> → [Vercel App]
                                ├── public/index.html  (Frontend)
                                └── api/oracle.js      (Proxy seguro → Anthropic API)
```

Tu API key NUNCA se expone al público. El proxy serverless la mantiene segura.

---

## PASO 1: Obtener tu API Key de Anthropic

1. Ve a **https://console.anthropic.com**
2. Crea una cuenta (email + verificación por SMS)
3. En el dashboard, ve a **Plans & Billing**
4. Selecciona **Build** (pay-as-you-go)
5. Agrega una tarjeta de crédito y compra créditos iniciales ($5 USD son suficientes para empezar — cada consulta del oráculo cuesta aprox. $0.003 USD)
6. Ve a **API Keys** en el menú lateral
7. Click **+ Create Key**
8. Nómbrala "oraculo-cuantico-metayantra"
9. **COPIA LA KEY INMEDIATAMENTE** — solo se muestra una vez
10. Guárdala en un lugar seguro (no la compartas con nadie)

La key tiene este formato: `sk-ant-api03-xxxxxxxxxxxxx...`

---

## PASO 2: Subir el Proyecto a GitHub

### Opción A: Desde la interfaz web de GitHub

1. Ve a **https://github.com/new**
2. Nombre del repo: `oraculo-cuantico`
3. Déjalo **público** o **privado** (tu elección)
4. Click **Create repository**
5. Sube los archivos:
   - `vercel.json`
   - `package.json`
   - `api/oracle.js`
   - `public/index.html`

### Opción B: Desde terminal (si tienes git instalado)

```bash
cd oraculo-cuantico
git init
git add .
git commit -m "Oráculo Cuántico v1.0"
git remote add origin https://github.com/TU-USUARIO/oraculo-cuantico.git
git push -u origin main
```

---

## PASO 3: Deployar en Vercel (Gratis)

1. Ve a **https://vercel.com** y crea una cuenta (puedes usar tu cuenta de GitHub)
2. Click **"Add New Project"**
3. Selecciona el repo `oraculo-cuantico` de GitHub
4. En la configuración del proyecto:
   - **Framework Preset:** Other
   - **Build Command:** (dejar vacío)
   - **Output Directory:** `public`
5. **IMPORTANTE — Agregar la variable de entorno:**
   - Click **"Environment Variables"**
   - Key: `ANTHROPIC_API_KEY`
   - Value: `sk-ant-api03-xxxxxxxxxxxxx...` (tu API key completa)
   - Click **Add**
6. Click **Deploy**
7. Espera ~30 segundos. Vercel te dará una URL tipo:
   `https://oraculo-cuantico.vercel.app`

### Verificar que funciona

1. Abre la URL que te dio Vercel
2. Navega por las pantallas: Bienvenida → Intención → Ruleta → Lectura
3. Si ves el mensaje del Doble Cuántico, todo está funcionando
4. Si hay error de API, verifica que la variable de entorno esté bien configurada

---

## PASO 4: Embeber en Shopify

### En el Admin de Shopify:

1. Ve a **Online Store → Pages**
2. Click **Add Page**
3. Título: "Oráculo Cuántico" (o como prefieras)
4. En el editor, click el botón **< >** (Show HTML)
5. Pega este código:

```html
<div style="width:100%;max-width:520px;margin:0 auto;">
  <iframe 
    src="https://TU-PROYECTO.vercel.app" 
    width="100%" 
    height="900" 
    frameborder="0" 
    style="border:none;border-radius:16px;overflow:hidden;"
    allow="autoplay"
    loading="lazy"
    title="Oráculo Cuántico Metayantra">
  </iframe>
</div>
```

6. Reemplaza `TU-PROYECTO.vercel.app` con tu URL real de Vercel
7. **Guarda** la página

### Opcional: Agregar al menú de navegación

1. Ve a **Online Store → Navigation**
2. Selecciona tu menú principal
3. **Add menu item:**
   - Name: "Oráculo Cuántico"
   - Link: la página que acabas de crear

---

## PASO 5: Dominio personalizado (opcional)

Si quieres que la app viva en `oraculo.metayantra.com.mx`:

1. En Vercel → Settings → Domains
2. Agrega `oraculo.metayantra.com.mx`
3. Vercel te dará un registro CNAME para configurar en tu DNS
4. Actualiza el iframe en Shopify con la nueva URL

---

## COSTOS ESTIMADOS

| Concepto | Costo |
|----------|-------|
| Vercel hosting | **GRATIS** (plan Hobby, hasta 100GB bandwidth) |
| Anthropic API por consulta | ~$0.003 USD (usando Claude Sonnet) |
| 100 consultas/día | ~$9 USD/mes |
| 1,000 consultas/día | ~$90 USD/mes |

Puedes configurar un **límite de gasto mensual** en console.anthropic.com para controlar costos.

### Si quieres reducir costos a $0:

La app ya incluye un **banco de mensajes pre-escritos** que se activa automáticamente si:
- La API key no está configurada
- Se agotan los créditos
- Hay cualquier error de conexión

Los usuarios siempre recibirán un mensaje, con o sin API activa.

---

## ESTRUCTURA DE ARCHIVOS

```
oraculo-cuantico/
├── vercel.json          ← Config de Vercel (permite iframe + routing)
├── package.json         ← Metadatos del proyecto
├── api/
│   └── oracle.js        ← Proxy serverless (protege tu API key)
└── public/
    └── index.html       ← La app completa (HTML/CSS/JS puro)
```

---

## PERSONALIZACIÓN

### Cambiar intenciones
En `public/index.html`, busca el array `INTENTIONS` y modifica/agrega las que quieras.

### Cambiar arquetipos de la ruleta
Busca el array `SEGMENTS` y personaliza los 12 arquetipos.

### Cambiar el prompt del Doble Cuántico
En `api/oracle.js`, modifica el contenido del mensaje para ajustar el tono, incluir referencias a productos de Metayantra, o cambiar el estilo.

### Agregar mensajes fallback
En `public/index.html`, busca `FALLBACK_MESSAGES` y agrega más variantes por intención.

### Cambiar colores/estética
Las variables CSS están al inicio del `<style>` en `index.html`.

---

## TROUBLESHOOTING

**"La conexión cuántica se ha interrumpido"**
→ Verifica tu API key en Vercel: Settings → Environment Variables

**La ruleta no gira**
→ Asegúrate de haber seleccionado una intención primero

**El iframe no carga en Shopify**
→ Verifica que `vercel.json` tenga los headers de `X-Frame-Options: ALLOWALL`

**Error 429 (rate limit)**
→ Estás haciendo muchas consultas. Espera 1 minuto o aumenta tu tier en Anthropic.

---

## SOPORTE

- Anthropic API docs: https://docs.anthropic.com
- Vercel docs: https://vercel.com/docs
- Shopify custom pages: https://help.shopify.com/en/manual/online-store/pages
