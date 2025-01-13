# Estructura de Archivos y Directorios

Apache HTTP Server utiliza una estructura de archivos organizada que permite gestionar fácilmente su configuración, módulos, y registros. A continuación, se explica la estructura de directorios y los archivos más importantes.

---

## Directorios Principales

### Ubuntu/Debian

- **`/etc/apache2/`**
  Directorio principal que contiene todos los archivos de configuración de Apache.

- **`/var/www/`**
  Directorio raíz por defecto para alojar los archivos del sitio web.

- **`/var/log/apache2/`**
  Archivos de registro de Apache, como logs de acceso y errores.

### CentOS/RedHat

- **`/etc/httpd/`**
  Directorio principal de configuración de Apache en sistemas basados en RedHat.

- **`/var/www/`**
  Igual que en Ubuntu/Debian, contiene los archivos del sitio web.

- **`/var/log/httpd/`**
  Archivos de registro de Apache.

---

## Archivos de Configuración Principales

### `apache2.conf` o `httpd.conf`
- **Ubicación:**
  - Ubuntu/Debian: `/etc/apache2/apache2.conf`
  - CentOS/RedHat: `/etc/httpd/conf/httpd.conf`
- **Descripción:** Archivo principal de configuración global. Incluye directivas generales como `Timeout`, `KeepAlive`, y `ServerTokens`.

### `envvars`
- **Ubicación:** `/etc/apache2/envvars`
- **Descripción:** Configura las variables de entorno utilizadas por Apache, como el usuario y grupo con los que se ejecuta el servicio (`www-data`).

### `ports.conf`
- **Ubicación:** `/etc/apache2/ports.conf`
- **Descripción:** Especifica los puertos que Apache utiliza para escuchar conexiones (por defecto, `80` para HTTP y `443` para HTTPS).

### `sites-available/` y `sites-enabled/`
- **Ubicación:** `/etc/apache2/`
- **Descripción:**
  - **`sites-available/`**: Contiene los archivos de configuración de los sitios web, organizados por host virtual.
  - **`sites-enabled/`**: Contiene enlaces simbólicos a los sitios activos definidos en `sites-available/`.
  
  Para activar un sitio:
  ```bash
  sudo a2ensite ejemplo.conf
  sudo systemctl reload apache2
  ```
  
  Para desactivar un sitio:
  ```bash
  sudo a2dissite ejemplo.conf
  sudo systemctl reload apache2
  ```

### `mods-available/` y `mods-enabled/`
- **Ubicación:** `/etc/apache2/`
- **Descripción:**
  - **`mods-available/`**: Contiene los módulos que pueden activarse en Apache.
  - **`mods-enabled/`**: Contiene enlaces simbólicos a los módulos activos definidos en `mods-available/`.

  Para activar un módulo:
  ```bash
  sudo a2enmod rewrite
  sudo systemctl restart apache2
  ```

  Para desactivar un módulo:
  ```bash
  sudo a2dismod rewrite
  sudo systemctl restart apache2
  ```

---

## Archivos de Registro

- **`access.log`**:
  Registra todas las solicitudes HTTP realizadas al servidor.
  - Ubicación: `/var/log/apache2/access.log` o `/var/log/httpd/access.log`

- **`error.log`**:
  Registra todos los errores ocurridos en el servidor.
  - Ubicación: `/var/log/apache2/error.log` o `/var/log/httpd/error.log`

---

Conocer esta estructura es esencial para administrar Apache de manera eficiente, activar módulos, y configurar sitios web. Mantén la organización y respalda los archivos importantes antes de realizar cambios.


