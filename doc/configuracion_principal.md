# Configuración Principal

La configuración principal de Apache HTTP Server está diseñada para proporcionar flexibilidad y adaptarse a múltiples entornos. A continuación, se detallan los archivos más relevantes y sus funciones clave.

---

## Archivos Principales de Configuración

### `apache2.conf` o `httpd.conf`

- **Ubicación:**
  - Ubuntu/Debian: `/etc/apache2/apache2.conf`
  - CentOS/RedHat: `/etc/httpd/conf/httpd.conf`

- **Descripción:** Este archivo contiene directivas globales que afectan al comportamiento de todo el servidor. Incluye configuraciones de tiempo de espera, conexiones persistentes, y módulos.

#### Ejemplo de Configuración Básica:

```apache
ServerTokens Prod           # Muestra solo información mínima del servidor.
ServerSignature Off         # Elimina firmas del servidor en páginas de error.
Timeout 300                 # Tiempo máximo para completar una solicitud.
KeepAlive On                # Habilita conexiones persistentes.
MaxKeepAliveRequests 100    # Límite de solicitudes en una conexión persistente.
KeepAliveTimeout 5          # Tiempo de espera para mantener la conexión.
```

### `envvars`

- **Ubicación:** `/etc/apache2/envvars`
- **Descripción:** Archivo para definir variables de entorno globales para Apache. Controla el usuario y el grupo con los que se ejecuta el servicio.

#### Ejemplo:

```bash
export APACHE_RUN_USER=www-data   # Usuario con permisos limitados.
export APACHE_RUN_GROUP=www-data  # Grupo asociado.
export APACHE_LOG_DIR=/var/log/apache2  # Directorio de registros.
```

### `ports.conf`

- **Ubicación:** `/etc/apache2/ports.conf`
- **Descripción:** Especifica los puertos en los que Apache escucha las conexiones.

#### Ejemplo:

```apache
Listen 80               # Puerto HTTP estándar.
Listen 443              # Puerto HTTPS estándar.

<IfModule ssl_module>
    Listen 443          # Puerto adicional para SSL.
</IfModule>

<IfModule mod_gnutls.c>
    Listen 443
</IfModule>
```

---

## Gestión de Sitios: `sites-available` y `sites-enabled`

Apache utiliza estos directorios para gestionar configuraciones de sitios web.

### `sites-available/`
- Contiene archivos de configuración para sitios no activos.

### `sites-enabled/`
- Contiene enlaces simbólicos a los archivos de `sites-available` que están activos.

#### Activar un Sitio:

```bash
sudo a2ensite ejemplo.conf
sudo systemctl reload apache2
```

#### Desactivar un Sitio:

```bash
sudo a2dissite ejemplo.conf
sudo systemctl reload apache2
```

#### Ejemplo de Configuración de un Sitio:

```apache
<VirtualHost *:80>
    ServerName ejemplo.com
    ServerAlias www.ejemplo.com
    DocumentRoot /var/www/ejemplo

    <Directory /var/www/ejemplo>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/ejemplo_error.log
    CustomLog ${APACHE_LOG_DIR}/ejemplo_access.log combined
</VirtualHost>
```

---

## Gestión de Módulos: `mods-available` y `mods-enabled`

Apache permite la activación o desactivación de módulos mediante comandos específicos.

### `mods-available/`
- Contiene la lista de todos los módulos disponibles.

### `mods-enabled/`
- Contiene enlaces simbólicos a los módulos activados.

#### Activar un Módulo:

```bash
sudo a2enmod rewrite
sudo systemctl restart apache2
```

#### Desactivar un Módulo:

```bash
sudo a2dismod rewrite
sudo systemctl restart apache2
```

---

La configuración principal permite ajustar el comportamiento global de Apache, habilitar módulos y gestionar sitios de forma flexible.


