# 🚀 Sync-in Docker - Plataforma Soberana de Almacenamiento y Colaboración

[![GitHub](https://img.shields.io/badge/GitHub-syncin%2Fserver-blue?logo=github)](https://github.com/syncin/server)
[![Docker](https://img.shields.io/badge/Docker-syncin%2Fserver-blue?logo=docker)](https://hub.docker.com/r/syncin/server)
[![License](https://img.shields.io/badge/License-AGPL--3.0-orange)](https://www.gnu.org/licenses/agpl-3.0.html)

## 📋 Descripción general

**Sync-in** es una plataforma soberana de almacenamiento de archivos y colaboración diseñada para equipos y usuarios que no quieren renunciar al control de su infraestructura ni de sus datos. A diferencia de los servicios de nube tradicionales, Sync-in mantiene los archivos legibles en tu propio sistema de archivos mientras añade capas potentes de colaboración.

**Ventaja clave:** Privacidad por diseño. No hay telemetría ni dependencia de terceros. Permite gestionar espacios de trabajo compartidos con permisos granulares, asegurando que la información sensible esté protegida pero accesible para quien deba estarlo.

## ✨ Características principales

- 🌐 **Interfaz web moderna** con drag & drop y vista de galería
- 🔐 **Autenticación avanzada** (OIDC, LDAP, MFA)
- 📁 **Gestión de Espacios (Spaces)** con roles y permisos detallados
- 🔗 **Enlaces públicos protegidos** con contraseñas y fechas de expiración
- 📝 **Edición de documentos en tiempo real** (integración con OnlyOffice y Collabora)
- 🔍 **Indexación profunda de contenido** para búsqueda full-text
- 🌐 **Soporte WebDAV** para montar la nube como unidad de red
- 💻 **Clientes nativos** para Windows, macOS y Linux
- ⚡ **Sincronización multi-dispositivo** eficiente y segura
- 🏠 **Self-hosted** - Control total de infraestructura

## 📋 Requisitos del sistema

- ✅ Docker
- ✅ Docker Compose
- 💾 **RAM:** Recomendado 2GB+ (especialmente si usas OnlyOffice/Collabora)
- 💿 **Espacio en disco:** Según el volumen de datos a almacenar
- 🗄️ **Base de Datos:** MariaDB/MySQL (incluida en el despliegue)
- 🔌 **Puertos:** 80/443 configurables para acceso web
- 🌐 Navegador moderno y cliente de escritorio Sync-in (opcional)

> **Sugerencia:** Para una experiencia completa de colaboración, despliega el contenedor de OnlyOffice junto al servidor de Sync-in.

## 🐳 Instalación

### Paso 1: Crear docker-compose.yml

```bash
cat > docker-compose.yml << 'EOF'
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
EOF
```

### Paso 2: Iniciar los servicios

```bash
docker compose up -d

# Verifica que todos los contenedores estén corriendo
docker compose ps
```

### Acceder

🌐 **http://localhost:8324** - Dashboard de Sync-in

**Primer login:**
- **Usuario:** `admin`
- **Contraseña:** La definida en `INIT_ADMIN_PASSWORD`

## ⚙️ Configuración

1. **Variables de entorno críticas** - Modifica en `docker-compose.yml`:
   - `INIT_ADMIN_PASSWORD`: Contraseña segura para el administrador inicial
   - `MYSQL_ROOT_PASSWORD`: Contraseña root de MariaDB
   - `PUID`/`PGID`: IDs de usuario/grupo para permisos de archivos (ajusta a tu NAS/servidor)

2. **Rutas de volúmenes** - Adapta las rutas `/volume1/docker/syncin/...` a tu estructura de directorios

3. **Puertos** - Cambia `"8324:8080"` y `"8467:80"` si hay conflictos

4. **Archivo environment.yaml** - Configuración avanzada opcional en `/volume1/docker/syncin/environment.yaml`

## 🚀 Primeros pasos

1. **Configuración del Administrador**
   - Accede al panel con las credenciales iniciales
   - Ve a **Settings → Profile** y cambia la contraseña por defecto
   - Configura la **URL pública del servidor** para que los enlaces funcionen correctamente

2. **Crear el primer "Espacio" (Space)**
   - En el menú lateral, selecciona **"Spaces" → "New Space"**
   - Asigna un nombre (ej. "Proyectos 2026") y una descripción
   - Define si es un espacio privado o compartido con el equipo

3. **Añadir Colaboradores**
   - Ve a **Settings → Users → New User**
   - Crea cuentas para tu equipo con roles específicos (Admin, User, Guest)
   - Asigna a los usuarios a los espacios creados anteriormente

4. **Subir y Sincronizar Archivos**
   - Arrastra archivos directamente a la interfaz web
   - Instala el **cliente de escritorio** en Windows/Linux/macOS
   - Conecta el cliente usando la URL de tu servidor y tus credenciales

5. **Activar Edición Colaborativa**
   - Ve a **Settings → Integrations → Document Server**
   - Ingresa la URL del contenedor de OnlyOffice (ej. `http://localhost:8467`)
   - Crea un documento nuevo y comienza a editar en tiempo real con tu equipo

## 💡 Casos de uso

- 🏠 **Sustituto de Dropbox/OneDrive:** Almacenamiento total bajo tu control, sin cuotas mensuales
- 👥 **Trabajo en Equipo:** Espacios compartidos para proyectos con control de versiones y comentarios
- 📄 **Gestión de Documentos:** Edición simultánea de hojas de cálculo y textos sin salir del servidor
- 🔗 **Intercambio de Archivos:** Envío de archivos pesados a clientes mediante enlaces cifrados y temporales
- 🏢 **Infraestructuras Privadas:** Ideal para entornos corporativos que requieren cumplimiento de privacidad estricto

## 🔒 Acceso remoto seguro

### HTTPS con Caddy (producción)

**Caddyfile:**
```caddyfile
syncin.tudominio.com {
    reverse_proxy localhost:8324
}
```

🌐 **https://syncin.tudominio.com** con certificados SSL automáticos gestionados por Caddy.

## 🛠️ Gestión y mantenimiento

```bash
# Ver logs del servidor
docker compose logs -f sync_in

# Backup de la Base de Datos
docker compose exec mariadb mysqldump -u root -p sync_in > syncin-backup-$(date +%Y%m%d).sql

# Backup de archivos físicos
tar -cvzf syncin-data-backup.tar.gz /volume1/docker/syncin/data

# Actualizar la plataforma
docker compose pull
docker compose up -d

# Monitorear recursos
docker stats sync_in mariadb onlyoffice
```

## 📝 Licencia

Este proyecto está licenciado bajo **AGPL-3.0** - ver el archivo [LICENSE](https://www.gnu.org/licenses/agpl-3.0.html) para más detalles.

---

> 📖 **Guía completa:** [Cómo instalar Sync-in en Docker - Plataforma Soberana de Almacenamiento y Colaboración](https://genbyte.blogspot.com/2026/07/como-instalar-sync-in-en-docker.html)

**Referencias oficiales:**
- [GitHub Repository - Sync-in Server](https://github.com/syncin/server)
- [Official Website - Sync-in](https://syncin.app)
- [Documentation Hub](https://docs.syncin.app)
- [Docker Hub - syncin/server](https://hub.docker.com/r/syncin/server)
- [Join Community on Discord](https://discord.gg/syncin)