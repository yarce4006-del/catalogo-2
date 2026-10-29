# ENOC — archivos preparados para GitHub + Netlify

Este repositorio contiene el catálogo, el acceso privado, el panel administrativo y el Worker de Cloudflare. Netlify publica los tres HTML; el Worker se despliega por separado en Cloudflare.

## Archivos
- `index.html`: catálogo público.
- `login.html`: inicio de sesión contra la API de Cloudflare.
- `admin.html`: panel que usa la API autenticada.
- `netlify.toml`: configuración de publicación estática.
- `worker/index.js` + `wrangler.toml`: API conectada a D1/R2 de producción.

## Publicación del frontend
1. Sube el contenido de esta carpeta a la raíz del repositorio de GitHub (no subas el ZIP como único archivo).
2. En Netlify, conecta ese repositorio y deja el comando de build vacío y el directorio de publicación como `.`.
3. El origen Netlify debe estar incluido en `ALLOWED_ORIGINS` de `worker/index.js`. El archivo trae `https://enoc365.netlify.app`; si Netlify asigna otro subdominio, añade ese origen antes de publicar el Worker.

## Despliegue de la API (separado del frontend)
Requiere Node.js y Wrangler autenticado con la cuenta Cloudflare correcta:

```powershell
npx wrangler deploy --config wrangler.toml
npx wrangler secret put ADMIN_PASSWORD --config wrangler.toml
npx wrangler secret put ADMIN_JWT_SECRET --config wrangler.toml
```

Usa una contraseña fuerte y genera un valor aleatorio largo para `ADMIN_JWT_SECRET`. No guardes esos secretos en GitHub ni los pongas en HTML.

## Guardarraíles incluidos
- Las escrituras de los registros fiscales están bloqueadas; su lectura permanece disponible.
- Las mutaciones de Cashea y la venta manual sin stock no se habilitan automáticamente.
- No se incluyen respaldos SQL ni exportaciones de datos.
- El Worker usa la base D1 de producción y el bucket R2 de producción configurados en `wrangler.toml`; no ejecutes el despliegue si no estás conectado a la cuenta correcta.

## Importante
Este paquete prepara los archivos del repositorio. La publicación del sitio estático no despliega automáticamente el Worker de Cloudflare, salvo que configures un pipeline específico para hacerlo.
