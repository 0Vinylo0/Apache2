# Configuración de Módulos

Apache HTTP Server es altamente modular, lo que significa que puedes cargar o descargar módulos según las necesidades de tu servidor. Estos módulos permiten agregar funcionalidad extra, como la reescritura de URLs, el soporte para proxy inverso, y la seguridad mejorada.

---

## Módulos Disponibles

### mods-available y mods-enabled

En sistemas basados en Ubuntu/Debian, los módulos se gestionan mediante dos directorios:

- **`/etc/apache2/mods-available/`**: Contiene todos los módulos instalados en el servidor.
- **`/etc/apache2/mods-enabled/`**: Contiene enlaces simbólicos a los módulos activados.

En sistemas basados en CentOS/RedHat, los módulos se gestionan directamente desde el archivo `httpd.conf`.

---

## Gestión de Módulos en Ubuntu/Debian

### Activar un Módulo

```bash
sudo a2enmod nombre_modulo
sudo systemctl restart apache2
```

### Desactivar un Módulo

```bash
sudo a2dismod nombre_modulo
sudo systemctl restart apache2
```

---

## Módulos Importantes

### `mod_rewrite`

Permite reescribir URLs de manera dinámica.

#### Activar el Módulo

```bash
sudo a2enmod rewrite
sudo systemctl restart apache2
```

#### Configuración Básica

```apache
<Directory /var/www/html>
    AllowOverride All
</Directory>

RewriteEngine On
RewriteRule ^index\.html$ home.html [L]
```

- **`RewriteEngine On`:** Habilita la reescritura de URLs.
- **`RewriteRule`:** Define reglas de reescritura.

---

### `mod_security`

Actúa como un firewall para aplicaciones web, protegiendo contra ataques comunes.

#### Instalación

```bash
sudo apt install libapache2-mod-security2
sudo a2enmod security2
sudo systemctl restart apache2
```

#### Configuración Básica

```apache
<IfModule security2_module>
    SecRuleEngine On
    SecRequestBodyAccess On
    SecResponseBodyAccess On
</IfModule>
```

- **`SecRuleEngine On`:** Habilita las reglas de seguridad.
- **`SecRequestBodyAccess`:** Inspecciona los cuerpos de las solicitudes.
- **`SecResponseBodyAccess`:** Inspecciona las respuestas del servidor.

---

### `mod_proxy`

Permite a Apache actuar como un proxy inverso para redirigir tráfico a otros servidores.

#### Activar el Módulo

```bash
sudo a2enmod proxy proxy_http
sudo systemctl restart apache2
```

#### Configuración Básica

```apache
<VirtualHost *:80>
    ServerName proxy.ejemplo.com

    ProxyPreserveHost On
    ProxyPass / http://backend.ejemplo.com:8080/
    ProxyPassReverse / http://backend.ejemplo.com:8080/
</VirtualHost>
```

- **`ProxyPreserveHost`:** Mantiene el encabezado `Host` original.
- **`ProxyPass` y `ProxyPassReverse`:** Redirigen el tráfico entrante al servidor backend.

---

### `mod_ssl`

Habilita conexiones HTTPS utilizando certificados SSL/TLS.

#### Activar el Módulo

```bash
sudo a2enmod ssl
sudo systemctl restart apache2
```

#### Configuración Básica

```apache
<VirtualHost *:443>
    ServerName seguro.ejemplo.com

    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/ejemplo.crt
    SSLCertificateKeyFile /etc/ssl/private/ejemplo.key
</VirtualHost>
```

- **`SSLEngine on`:** Habilita SSL para el Virtual Host.
- **`SSLCertificateFile`:** Ruta al certificado SSL.
- **`SSLCertificateKeyFile`:** Ruta a la clave privada.

---

La correcta gestión y configuración de módulos es esencial para aprovechar al máximo las capacidades de Apache.

**Fuente:** [Módulos en Apache HTTP Server](https://httpd.apache.org/docs/current/mod/).
