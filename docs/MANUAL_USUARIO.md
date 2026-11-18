# Manual de usuario

El Sistema de Denuncias Universitario permite presentar reportes, consultar su estado y, para el personal autorizado, gestionar los casos recibidos.

Abre el sitio publicado o la dirección local que te haya indicado la persona administradora. En la pantalla inicial encontrarás las opciones **Presentar una denuncia**, **Consultar una denuncia** y **Acceso administrativo**.

## 1. Guía para usuarios

### Presentar una denuncia

1. En la página principal, selecciona **Presentar una denuncia**.

   ![Página principal del sistema](imagenes-manual-usuario/Pagina%20Principal.png)

2. Completa los campos obligatorios:
   - **Rol en la universidad:** selecciona tu rol.
   - **Sede donde ocurrió el evento:** selecciona la sede correspondiente.
   - **Fecha del evento:** selecciona la fecha del hecho; no puede ser futura.
   - **Descripción de lo ocurrido:** explica los hechos con claridad. El límite es de 500 caracteres.

   ![Primera parte del formulario de denuncia](imagenes-manual-usuario/Denunciar%201.png)

3. Si quieres recibir avisos por Telegram, inicia una conversación con el bot como se explica en [Vincular Telegram](#vincular-telegram) y escribe tu usuario de Telegram en el formulario. Este dato es opcional.

   ![Campos opcionales de Telegram y evidencias en el formulario](imagenes-manual-usuario/Denunciar%202.png)

4. Si tienes fotos o videos, puedes compartir enlaces a las evidencias. Este paso es opcional:
   1. Abre [LimeWire](https://limewire.com/) y selecciona o arrastra el archivo que quieres compartir.
   2. Espera a que termine la carga.
   3. Abre las opciones para compartir y revisa quién podrá ver y descargar el archivo.
   4. Copia el enlace y pégalo en **Fotos o videos de la evidencia**. Para compartir más de uno, escribe cada enlace en una línea distinta.

   ![Seleccionar un archivo en LimeWire](imagenes-manual-usuario/LimeWire%20subir%20documento%20%281%29.png)

   ![Archivo cargado en LimeWire](imagenes-manual-usuario/LimeWire%20subir%20documento%20%282%29.png)

   ![Opciones para compartir el archivo](imagenes-manual-usuario/LimeWire%20subir%20documento%20%283%29.png)

   ![Copiar el enlace para compartir](imagenes-manual-usuario/LimeWire%20subir%20documento%20%284%29.png)

   > [!IMPORTANT]
   > La evidencia se aloja en un servicio externo y no en el sistema de denuncias. Revisa los permisos y la vigencia del enlace de LimeWire antes de compartirlo. Cualquier persona que tenga acceso al enlace podría ver o descargar el archivo, según los permisos elegidos.

5. Revisa la información y selecciona **Enviar denuncia**. La aplicación confirmará el registro y mostrará un **código de seguimiento**.

   ![Confirmación del registro y código de seguimiento](imagenes-manual-usuario/Denuncia%20Realizada.png)

6. Guarda el código en un lugar seguro. Lo necesitarás para consultar el estado; no se puede recuperar desde el formulario si lo pierdes.

### Consultar el estado de una denuncia

1. Selecciona **Consultar estado** en la navegación o **Consultar una denuncia** en la página principal.
2. Escribe el código de seguimiento recibido al presentar la denuncia y selecciona **Consultar**.
3. Verás el estado actual, la sede, la fecha del reporte y, si fueron proporcionadas, las evidencias enlazadas.

   ![Formulario de consulta del estado](imagenes-manual-usuario/Consultar%20Estado.png)

   ![Resultado de la consulta de estado](imagenes-manual-usuario/Estado%20Consultado.png)

La consulta es pública y no requiere iniciar sesión. Por privacidad, no muestra la descripción completa ni datos personales. Si el sistema no encuentra la denuncia, verifica que el código esté completo y bien escrito.

### Vincular Telegram

1. Abre el bot indicado en el formulario: [@denuncia_requestUSB_bot](https://t.me/denuncia_requestUSB_bot).
2. Envía el comando `/start` para iniciar la conversación.
3. Al presentar la denuncia, escribe tu usuario de Telegram en el campo opcional **Usuario de Telegram**.

Cuando la cuenta esté vinculada, el bot puede enviarte una confirmación y avisos cuando el equipo administrativo actualice el estado de la denuncia.

![Ejemplo de mensajes del bot de Telegram](imagenes-manual-usuario/Bot%20Telegram.png)

## 2. Guía para administradores

El acceso administrativo está reservado al personal autorizado. Las credenciales son entregadas por quien administra el sistema; no existe un registro público de administradores.

### Iniciar sesión

1. Selecciona **Acceso administrativo** en la navegación.
2. Introduce el usuario y la contraseña asignados.
3. Selecciona **Iniciar sesión**. Si las credenciales son válidas, se abrirá el panel de denuncias.

   ![Formulario de acceso administrativo](imagenes-manual-usuario/Login%20Administrativo.png)

Si aparece un error de acceso, comprueba las credenciales e inténtalo de nuevo. Si no puedes iniciar sesión, contacta a la persona responsable de configurar el sistema.

### Revisar el panel

El panel presenta un resumen de denuncias por sede y una tabla con las denuncias recibidas. Puedes seleccionar **Actualizar datos** para volver a cargar la información.

![Resumen administrativo por sede](imagenes-manual-usuario/Resumen%20de%20Denuncias%20-%20Admin%20.png)

La tabla muestra el identificador y código de seguimiento, la fecha, la sede, el rol, la descripción, los enlaces de evidencia, el usuario de Telegram y el estado. Usa el campo **Buscar** para filtrar los resultados visibles por sede, descripción o estado; utiliza **Anterior** y **Siguiente** para recorrer las páginas.

![Listado y acciones disponibles para las denuncias](imagenes-manual-usuario/Listado%20de%20denuncias%20-%20Admin.png)

### Actualizar el estado y notificar al denunciante

1. En la fila de la denuncia, abre el selector de estado y elige el nuevo estado.
2. Si deseas agregar contexto para quien reportó el caso, escribe un mensaje en **Mensaje opcional para Telegram**.
3. Selecciona **Guardar** y espera a que se complete la actualización.

Los estados de trabajo son **REGISTRADA**, **ADMITIDA**, **PROCESANDO** y **PROCESADA**. Avanza el caso según corresponda; no se permiten retrocesos. También puedes marcar como **RECHAZADA** una denuncia activa. **PROCESADA** y **RECHAZADA** son estados finales y no admiten cambios posteriores. El mensaje personalizado es opcional y se envía por Telegram cuando la denuncia tiene un contacto vinculado.

Si la actualización falla, el panel muestra un mensaje de error. Revisa que el nuevo estado sea válido y vuelve a intentarlo; si persiste, informa a la persona encargada del sistema.

### Cerrar sesión

Cuando termines, selecciona **Salir** en la barra de navegación, especialmente si estás usando un equipo compartido.
