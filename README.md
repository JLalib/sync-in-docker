# Sync-in: Plataforma soberana de almacenamiento, sincronización y colaboración en equipo

![Docker](https://img.shields.io/badge/Docker-Sync--in-blue)
![License](https://img.shields.io/badge/License-AGPL--3.0-blue)

> **Sync-in** es una plataforma soberana de almacenamiento de archivos y colaboración diseñada para equipos y usuarios que no quieren renunciar al control de su infraestructura ni de sus datos. A diferencia de los servicios de nube tradicionales, Sync-in mantiene los archivos legibles en tu propio sistema de archivos mientras añade capas potentes de colaboración.

## Características principales

- **Interfaz web moderna** con drag & drop y vista de galería
- **Autenticación avanzada** (OIDC, LDAP, MFA)
- **Gestión de Espacios (Spaces)** con roles y permisos detallados
- **Enlaces públicos protegidos** con contraseñas y fechas de expiración
- **Edición de documentos en tiempo real** (integración con OnlyOffice y Collabora)
- **Indexación profunda de contenido** para búsqueda full‑text
- **Soporte WebDAV** para montar la nube como unidad de red
- **Clientes nativos** para Windows, macOS y Linux
- **Cliente de escritorio y CLI** para flujos de sincronización eficientes

### Para equipos y profesionales
- Elimina la dependencia de Google Drive o Dropbox
- Sincronización rápida, segura y bajo tu propio dominio

## Requisitos del sistema

- **Docker & Docker Compose**
- **RAM:** 2 GB+ (especialmente si usas OnlyOffice/Collabora)
- **Espacio en disco:** Según el volumen de datos a almacenar
- **Base de datos:** MariaDB/MySQL (incluida en el despliegue)
- **Puerto:** 80/443 configurables para acceso web
- **Navegador moderno** y cliente de escritorio Sync‑in (opcional)

> 💡 **Sugerencia:** Para una experiencia completa de colaboración, despliega el contenedor de OnlyOffice junto al servidor de Sync‑in.

## Instalación con Docker Compose

### Paso 1: Crear `docker-compose.yml`

Copia el siguiente contenido en un archivo llamado `docker-compose.yml`:

```yaml
services:
  sync_in:
    image: syncin/server:latest
    container_name: Sync-in
    environment:
      - INIT_ADMIN=true
      - INIT_ADMIN_LOGIN=admin
      - INIT_ADMIN_PASSWORD=tu_password_seguro
      - PUID=1026
      - PGID=100
    ports:
      - "8324:8080"
    volumes:
      - /volume1/docker/syncin/environment.yaml:/app/environment/environment.yaml
      - /volume1/docker/syncin/data:/app/data:rw
      - /volume1/docker/syncin/static:/app/static/releases:ro
    depends_on:
      - mariadb
      - onlyoffice
    restart: on-failure:10

  mariadb:
    image: mariadb:11.8-noble
    container_name: Sync-in-DB
    environment:
      - MYSQL_DATABASE=sync_in
      - MYSQL_ROOT_PASSWORD=root_password
    volumes:
      - /volume1/docker/syncin/db:/var/lib/mysql:rw
    restart: on-failure:5

  onlyoffice:
    container_name: Sync-in-ONLYOFFICE
    image: onlyoffice/documentserver:latest
    ports:
      - "8467:80"
    volumes:
      - /volume1/docker/syncin/onlyoffice/data:/var/www/onlyoffice/Data:rw
    restart: on-failure:5
```

### Paso 2: Editar las contrasejas

Antes de iniciar los contenedores, edita el archivo `docker-compose.yml` para establecer tus contraseñas:

- `INIT_ADMIN_PASSWORD`: contraseña para el administrador de Sync‑in (usuario `admin`)
- `MYSQL_ROOT_PASSWORD`: contraseña para el usuario root de MariaDB

Puedes dejar los volúmenes tal cual o adaptar las rutas a tu entorno.

### Paso 3: Iniciar los servicios

Ejecuta el siguiente comando en el mismo directorio donde está el `docker-compose.yml`:

```bash
docker compose up -d
```

Verifica que todos los contenedores estén corriendo:

```bash
docker compose ps
```

### Paso 4: Acceder a Sync‑in

Abre tu navegador y visita:

```
http://localhost:8324
```

Deberás ver el panel de inicio de sesión de Sync‑in.

### Paso 5: Primer login

- **Usuario:** `admin`
- **Contraseña:** la que definiste en `INIT_ADMIN_PASSWORD`

Tras iniciar sesión, puedes comenzar a crear espacios, subir usuarios y configurar la plataforma según tus necesidades.

## Notas adicionales

- Si deseas cambiar el puerto de acceso, modifica la sección `ports` del servicio `sync_in`.
- Para persistir la configuración de OnlyOffice, asegúrate de que el volumen `/volume1/docker/syncin/onlyoffice/data` exista o ajusta la ruta según tu sistema.
- Recuerda realizar copias de seguridad periódicas de los volúmenes de datos (`/volume1/docker/syncin/data` y `/volume1/docker/syncin/db`).

## Licencia

Este proyecto está licenciado bajo los términos de la **AGPL‑3.0**. Consulta el archivo [LICENSE](LICENSE) para más detalles.

---

> ✨ **Nota**: Este repositorio contiene la configuración Docker y documentación extraída del tutorial de Genbyte: [Cómo instalar Sync‑in en Docker](https://genbyte.blogspot.com/2026/07/como-instalar-sync-in-en-docker.html)
