Sync-in | File Storage | Collaboration

 Control total de infraestructura. Sincronización multi-dispositivo. Edición de documentos online. Búsqueda full-text indexada. WebDAV. AGPL-3.0 open source. Self-hosted.

 ¿Qué es Sync-in?
 Sync-in es una plataforma soberana de almacenamiento de archivos y colaboración diseñada para equipos y usuarios que no quieren renunciar al control de su infraestructura ni de sus datos. A diferencia de los servicios de nube tradicionales, Sync-in mantiene los archivos legibles en tu propio sistema de archivos mientras añade capas potentes de colaboración.

 Ventaja clave: Privacidad por diseño. No hay telemetría ni dependencia de terceros. Permite gestionar espacios de trabajo compartidos con permisos granulares, asegurando que la información sensible esté protegida pero accesible para quien deba estarlo.

 Características principales: Interfaz web moderna con drag & drop y vista de galería. Autenticación avanzada (OIDC, LDAP, MFA). Gestión de Espacios (Spaces) con roles y permisos detallados. Enlaces públicos protegidos con contraseñas y fechas de expiración. Edición de documentos en tiempo real (integración con OnlyOffice y Collabora). Indexación profunda de contenido para búsqueda full-text. Soporte WebDAV para montar la nube como unidad de red. Clientes nativos para Windows, macOS y Linux.

 Para equipos y profesionales: Elimina la dependencia de Google Drive o Dropbox. Sincronización rápida, segura y bajo tu propio dominio.

 Características principales

 Sincronización Soberana
 Tus archivos, tu servidor. Control total sobre dónde y cómo se almacenan los datos.

 Edición Online
 Integración nativa con OnlyOffice y Collabora para editar documentos en el navegador.

 Búsqueda Avanzada
 Indexación profunda del contenido de los documentos para encontrar archivos al instante.

 Colaboración Segura
 Espacios compartidos, gestión de invitados y permisos granulares por usuario o grupo.

 Enlaces Protegidos
 Comparte archivos con el exterior mediante links con contraseña y fecha de caducidad.

 Soporte WebDAV
 Accede a tus archivos desde cualquier explorador de archivos o app móvil compatible.

 Autenticación Robusta
 Soporte para LDAP, OIDC y MFA para garantizar que solo usuarios autorizados entren.

 Multi-plataforma
 Clientes nativos y CLI para Windows, macOS y Linux con flujos de sincronización eficientes.

 Requisitos del sistema

 Docker & Docker Compose
 RAM: Recomendado 2GB+ (especialmente si usas OnlyOffice/Collabora)
 Espacio en disco: Según el volumen de datos a almacenar de

 Base de Datos: MariaDB/MySQL (incluida en el despliegue)
 Puerto: 80/443 configurables para acceso web
 Navegador moderno y cliente de escritorio Sync-in (opcional)

 Sugerencia: Para una experiencia completa de colaboración, despliega el contenedor de OnlyOffice junto al servidor de Sync-in.

 Instalación con Docker Compose

 Paso 1: Crear docker-compose.yml

 Paso 2: Iniciar los servicios
 docker compose up -d

docker compose ps

 Acceder
 http://localhost:8324 - Dashboard de Sync-in

 Primer login

 Usuario: admin
 Contraseña: La definida en INIT_ADMIN_PASSWORD

 Primeros pasos

 1. Configuración del Administrador

  Accede al panel con las credenciales iniciales.
  Ve a Settings → Profile y cambia la contraseña por defecto.
  Configura la URL pública del servidor para que los enlaces funcionen correctamente.

 2. Crear el primer "Espacio" (Space)

  En el menú lateral, selecciona "Spaces" → "New Space".
  Asigna un nombre (ej. "Proyectos 2026") y una descripción.
  Define si es un espacio privado o compartido con el equipo.

 3. Añadir Colaboradores

  Settings → Users → New User.
  Crea cuentas para tu equipo con roles específicos (Admin, User, Guest).
  Asigna a los usuarios a los espacios creados anteriormente.

 4. Subir y Sincronizar Archivos

  Arrastra archivos directamente a la interfaz web.
  Instala el cliente de escritorio en Windows/Linux/macOS.
  Conecta el cliente usando la URL de tu servidor y tus credenciales.

 5. Activar Edición Colaborativa

  Settings → Integrations → Document Server.
  Ingresa la URL del contenedor de OnlyOffice (ej. http://localhost:8467).
  Crea un documento nuevo y comienza a editar en tiempo real con tu equipo.

 Casos de uso

 Sustituto de Dropbox/OneDrive: Almacenamiento total bajo tu control, sin cuotas mensuales.
 Trabajo en Equipo: Espacios compartidos para proyectos con control de versiones y comentarios.
 Gestión de Documentos: Edición simultánea de hojas de cálculo y textos sin salir del servidor.
 Intercambio de Archivos: Envío de archivos pesados a clientes mediante enlaces cifrados y temporales.
 Infraestructuras Privadas: Ideal para entornos corporativos que requieren cumplimiento de privacidad estricto.

 HTTPS con Caddy (producción)

 Caddyfile
 syncin.tudominio.com {
   reverse_proxy localhost:8324
 }

 Acceso remoto seguro
 https://syncin.tudominio.com con certificados SSL automáticos gestionados por Caddy.

 Gestión y mantenimiento

 Ver logs del servidor
 docker compose logs -f sync_in

 Backup de la Base de Datos
 docker compose exec mariadb mysqldump -u root -p sync_in > syncin-backup-$(date +%Y%m%d).sql

 Backup de archivos físicos
 tar -cvzf syncin-data-backup.tar.gz /volume1/docker/syncin/data

 Actualizar la plataforma
 docker compose pull
docker compose up -d

 Monitorear recursos
 docker stats sync_in mariadb onlyoffice

 Comparativa con alternativas

 vs Nextcloud
 Sync-in gana: Mucho más ligero, enfoque en rendimiento y simplicidad, archivos legibles en FS. Nextcloud gana: Ecosistema de apps gigantesco (Calendario, Contactos, etc).

 vs Google Drive
 Sync-in gana: Privacidad total, sin rastreo, control de infraestructura. Google gana: Facilidad de uso instantánea, integración con ecosistema Google.

 vs Dropbox
 Sync-in gana: Sin límites de espacio (solo tu disco), sin cuotas mensuales. Dropbox gana: Sincronización extremadamente madura y rápida.

 Mejor para: Quienes buscan una alternativa a la nube comercial que sea ligera, rápida y centrada en la soberanía de los datos.