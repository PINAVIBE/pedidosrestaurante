# Comedor Universitario – Sistema de pedidos

Plataforma web (Node.js + Express + SQLite) para gestionar pedidos, productos, inventario, usuarios, ventas y reportes.

## Ejecutar
```
npm install
npm start
```
Abre http://localhost:3000 (ya NO se abre index.html directo: ahora hay servidor).
Requiere Node.js 18 o superior (se recomienda 20). La base de datos se crea sola en `data/comedor.db`.
Para reiniciarla desde cero, detén el servidor y borra la carpeta `data/`.

Los estudiantes pueden crear su cuenta desde la pantalla de login (siempre con rol `cliente`).
Solo el administrador crea usuarios de cocina o admin.

## Variables de entorno
Las contraseñas **no** están en el código; se leen del entorno al crear la base de datos por primera vez.

| Variable | Para qué sirve |
|---|---|
| `ADMIN_EMAIL` | Correo del administrador inicial (por defecto `admin@comedor.local`). |
| `ADMIN_PASSWORD` | Contraseña del administrador inicial. Si no se define, se genera una temporal y se muestra en el log. |
| `DEMO_PASSWORD` | Opcional. Si se define, crea `cocina@comedor.local` y `cliente@comedor.local` con esa contraseña. |
| `RESET_ADMIN_PASSWORD` | Rescate: si el admin olvidó su clave, define esta variable (con `ADMIN_EMAIL`), reinicia y luego bórrala. |
| `NODE_ENV` | Usa `production` al desplegar (activa la cookie `secure`). |
| `PORT` | Puerto del servidor (por defecto 3000; la plataforma de hosting lo define sola). |

Ejemplo local (Linux/macOS): `ADMIN_PASSWORD=MiClaveSegura npm start`
En Windows (PowerShell): `$env:ADMIN_PASSWORD="MiClaveSegura"; npm start`

## Despliegue (Render)
GitHub Pages **no** sirve para esta app (solo muestra archivos estáticos); se necesita un host que ejecute Node.
1. En Render: *New → Web Service* y conecta este repositorio.
2. Build Command: `npm install` · Start Command: `npm start`.
3. Agrega las variables de entorno de la tabla anterior.
4. Cada `git push` redespliega la app automáticamente.

> **Importante:** en el plan gratuito de Render el disco no es persistente. La base de datos SQLite se reinicia
> con cada redespliegue o reinicio, y se pierden usuarios y pedidos. Es válido para demostraciones.
> Para uso real, usa un disco persistente (montado en `data/`) o una base de datos externa.
> El servicio gratuito también se "duerme" tras un rato sin visitas, y la primera carga tarda.

## Contraseñas olvidadas
No hay recuperación por correo. El administrador puede cambiar la contraseña de cualquier usuario
desde **Usuarios → Cambiar contraseña**. Si el admin olvida la suya, usa `RESET_ADMIN_PASSWORD` (ver arriba).

## Inventario
En **Inventario** el administrador ajusta el stock con −5 / +10 o con **Otra cantidad** (número positivo suma, negativo resta).

## Base de datos (SQLite)
`usuarios` · `productos` · `pedidos` · `pedido_items` · `sesiones`

## Seguridad implementada
- Contraseñas guardadas con bcrypt (nunca en texto plano).
- Sesión con cookie `httpOnly`; en la BD solo se guarda el hash del token.
- Permisos por rol validados en el servidor (no solo ocultando botones).
- El total y los precios de un pedido los calcula el servidor; el stock se descuenta en una transacción.
- Bloqueo temporal tras 8 intentos de login fallidos.
- Texto de usuarios escapado en pantalla (evita XSS).

## Imágenes de los platos
Están en `public/img/` (SVG). `app.js` elige la imagen según el nombre del producto (`IMG_NOMBRE`);
si no hay coincidencia, usa la de su categoría (`IMG_CAT`). Para un plato nuevo, agrega `public/img/mi-plato.svg`
(o .png/.jpg, cambiando la extensión en `imgDe`) y una línea en `IMG_NOMBRE`.
