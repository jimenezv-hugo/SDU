# Manual de despliegue

Este manual cubre dos opciones: ejecutar todos los servicios localmente con Docker Compose, o publicar el frontend en GitHub Pages y la API y base de datos en Render.

## Requisitos

- Git y acceso al repositorio.
- Docker Desktop con Docker Compose, para el despliegue local.
- Una cuenta de GitHub con permisos de administrador del repositorio y una cuenta de Render, para el despliegue web.

## Crear un fork del repositorio

Antes de desplegar tu propia instancia, crea una copia del proyecto en tu cuenta de GitHub:

1. Inicia sesión en GitHub y abre el repositorio original: <https://github.com/jimenezv-hugo/SDU>.
2. Selecciona **Fork → Create a new fork**, elige tu cuenta como propietario y crea el fork. Conserva el nombre `SDU` para que coincida con la ruta `/SDU/` configurada para GitHub Pages.
3. Clona tu fork (reemplaza `TU_USUARIO` por tu nombre de usuario de GitHub):

   ```powershell
   git clone https://github.com/TU_USUARIO/SDU.git
   cd SDU
   ```

4. Si vas a publicar una versión con cambios, trabaja y súbelos a la rama `main` de tu fork. En **Actions**, habilita los workflows del repositorio si GitHub los dejó pausados al crear el fork.
5. Al crear los servicios web, conecta tu fork y no el repositorio original. Configura tus propias credenciales, base de datos, bot y servicio de Render; no reutilices secretos de otra instalación.

Si le das al fork un nombre distinto de `SDU`, cambia `/SDU/` por `/<NOMBRE_DEL_REPOSITORIO>/` en el paso de compilación del workflow `.github/workflows/deploy-pages.yml`. La URL de GitHub Pages también usará ese nombre.

## Despliegue local con Docker Compose

1. Abre una terminal en la carpeta raíz del repositorio original o de tu fork.
2. Crea el archivo local de variables y cambia las contraseñas de ejemplo por valores propios:

   ```powershell
   Copy-Item .env.example .env
   ```

   Edita `.env`. `DATABASE_PASSWORD` debe coincidir con la contraseña que Compose entrega a PostgreSQL y a la API. El bot de Telegram es opcional; déjalo vacío si no se va a usar.

3. Construye las imágenes e inicia la aplicación:

   ```powershell
   docker compose up -d --build
   ```

   En el primer inicio, PostgreSQL ejecuta automáticamente los scripts de `Database-SDU/migrations/` y crea el volumen persistente `postgres-data`.

4. Comprueba los servicios:

   | Servicio | Dirección |
   |---|---|
   | Aplicación web | <http://localhost:4200> |
   | API | <http://localhost:8080> |
   | PostgreSQL | `localhost:5432` |

   La API está disponible para las llamadas que hace la aplicación; no tiene una página de inicio pública. Para revisar el estado de los contenedores y sus registros:

   ```powershell
   docker compose ps
   docker compose logs -f backend
   ```

5. Para detener los servicios sin borrar la base de datos:

   ```powershell
   docker compose down
   ```

   Para volver a iniciarlos, ejecuta `docker compose up -d`. Conserva `.env` en privado y no compartas sus claves.

## Despliegue web con GitHub Pages y Render

La arquitectura pública usa GitHub Pages para la aplicación Angular, Render Web Service para la API Spring Boot y Render PostgreSQL para los datos. El frontend debe apuntar a la URL HTTPS del backend en Render.

### 1. Crear la base de datos PostgreSQL en Render

1. En el panel de Render, crea una base de datos PostgreSQL y espera a que esté disponible.
2. Guarda el host, nombre de la base, usuario y contraseña de la conexión interna; se usarán en la configuración del backend.
3. Las migraciones de este repositorio no se ejecutan automáticamente en Render (`spring.flyway.enabled` está desactivado; en Compose las ejecuta la imagen de PostgreSQL). Desde la raíz del repositorio, ejecuta cada migración una sola vez, en orden, usando la URL **externa** de la base de datos que muestra Render:

   ```powershell
   psql "postgresql://USUARIO:CONTRASENA@HOST_EXTERNO:PUERTO/NOMBRE_BASE" -v ON_ERROR_STOP=1 -f Database-SDU/migrations/V1__crear_esquema.sql
   psql "postgresql://USUARIO:CONTRASENA@HOST_EXTERNO:PUERTO/NOMBRE_BASE" -v ON_ERROR_STOP=1 -f Database-SDU/migrations/V2__sedes_iniciales.sql
   psql "postgresql://USUARIO:CONTRASENA@HOST_EXTERNO:PUERTO/NOMBRE_BASE" -v ON_ERROR_STOP=1 -f Database-SDU/migrations/V3__seguimiento_y_evidencias.sql
   ```

   Reemplaza la URL de ejemplo con la URL externa de Render y asegúrate de tener instalado el cliente `psql`. Si la contraseña contiene caracteres reservados para una URL, codifícala antes de usarla. No ejecutes estas migraciones de nuevo sobre una base ya inicializada.

### 2. Crear el servicio de backend en Render

1. Crea un **Web Service** conectado al repositorio de GitHub y selecciona el runtime **Docker**.
2. Configura el directorio raíz como `Backend-SDU` y el Dockerfile como `Dockerfile`.
3. Añade estas variables de entorno al servicio:

   | Variable | Valor |
   |---|---|
   | `DATABASE_URL` | `jdbc:postgresql://HOST_INTERNO:5432/NOMBRE_BASE` |
   | `DATABASE_USERNAME` | Usuario PostgreSQL de Render |
   | `DATABASE_PASSWORD` | Contraseña PostgreSQL de Render |
   | `SDU_ADMIN_USERNAME` | Usuario nuevo para el panel administrativo |
   | `SDU_ADMIN_PASSWORD` | Contraseña segura nueva para el panel |
   | `APP_CORS_ALLOWED_ORIGINS` | `https://TU_USUARIO.github.io` |
   | `TELEGRAM_BOT_TOKEN` | Token del bot, opcional |

   Usa el host interno y los datos de conexión de la base de Render. Si Render presenta una URL PostgreSQL del formato `postgresql://usuario:clave@host:5432/base`, configura `DATABASE_URL` con el formato JDBC `jdbc:postgresql://host:5432/base` y coloca usuario y contraseña en sus variables respectivas. Render asigna automáticamente `PORT`; la aplicación lo utiliza para escuchar las solicitudes.

4. Despliega el servicio. Su URL pública será similar a `https://nombre-del-servicio.onrender.com`; consérvala para el paso de GitHub Pages. Comprueba el estado en `https://nombre-del-servicio.onrender.com/actuator/health`; debe responder con estado `UP`.

### 3. Publicar el frontend en GitHub Pages

1. En el repositorio, abre **Settings → Secrets and variables → Actions → Variables** y crea la variable de repositorio:

   | Nombre | Valor |
   |---|---|
   | `SDU_API_URL` | URL HTTPS pública del Web Service de Render, sin `/` al final |

   Es una variable de configuración, no un token. El workflow falla si falta o no es HTTPS.

2. Abre **Settings → Pages** y selecciona **GitHub Actions** como fuente de publicación.
3. Ejecuta el workflow `Deploy Angular to GitHub Pages` desde **Actions → Run workflow**, o publica un cambio en la rama `main`. El workflow compila el frontend con la URL de Render, configura la ruta `/SDU/` y publica el resultado.
4. Abre `https://TU_USUARIO.github.io/SDU/`. Para cambiar el backend, actualiza `SDU_API_URL` y vuelve a ejecutar el workflow.

Si la página carga pero no puede consultar o enviar denuncias, verifica que `SDU_API_URL` coincida exactamente con la URL del backend y que `APP_CORS_ALLOWED_ORIGINS` en Render sea `https://TU_USUARIO.github.io` (sin `/SDU/`). Revisa también los registros del servicio y que la base de datos tenga las tres migraciones aplicadas.

## Consideraciones

- El servicio web depende de PostgreSQL y debe iniciarse después de que las migraciones hayan terminado.
- Configura credenciales propias y conserva los tokens y contraseñas exclusivamente en `.env` local o en la configuración privada de Render.
- Sin `TELEGRAM_BOT_TOKEN`, la aplicación web y las denuncias siguen funcionando, pero no se sincronizan contactos ni se envían notificaciones de Telegram.
