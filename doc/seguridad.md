# Seguridad

La seguridad es un aspecto fundamental en la configuración de Apache HTTP Server. Esta sección detalla cómo proteger tu servidor mediante SSL/TLS, encabezados HTTP seguros, y autenticación básica.

---

## SSL/TLS

La implementación de SSL/TLS permite que las conexiones entre el servidor y los clientes sean seguras mediante el cifrado de datos.

### Habilitar SSL en Apache

1. **Instalar los paquetes necesarios:**
   ```bash
   sudo apt install openssl
   sudo apt install libapache2-mod-ssl
   sudo a2enmod ssl
   sudo systemctl restart apache2
   ```

2. **Generar un certificado SSL auto-firmado (opcional):**
   ```bash
   sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
       -keyout /etc/ssl/private/apache-selfsigned.key \
       -out /etc/ssl/certs/apache-selfsigned.crt
   ```

3. **Configurar un Virtual Host para HTTPS:**
   ```apache
   <VirtualHost *:443>
       ServerName seguro.ejemplo.com

       SSLEngine on
       SSLCertificateFile /etc/ssl/certs/apache-selfsigned.crt
       SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key

       <Directory /var/www/seguro>
           Options -Indexes +FollowSymLinks
           AllowOverride All
           Require all granted
       </Directory>

       ErrorLog ${APACHE_LOG_DIR}/seguro_error.log
       CustomLog ${APACHE_LOG_DIR}/seguro_access.log combined
   </VirtualHost>
   ```

4. **Reiniciar Apache:**
   ```bash
   sudo systemctl restart apache2
   ```

---

## Encabezados HTTP de Seguridad

Los encabezados HTTP pueden proteger contra ataques comunes, como la inyección de contenido y el robo de datos.

### Habilitar Encabezados Seguros

1. **Activar el módulo de encabezados:**
   ```bash
   sudo a2enmod headers
   sudo systemctl restart apache2
   ```

2. **Configurar encabezados seguros en el archivo de configuración:**
   ```apache
   <IfModule mod_headers.c>
       Header always set X-Content-Type-Options "nosniff"
       Header always set X-Frame-Options "DENY"
       Header always set Strict-Transport-Security "max-age=31536000; includeSubDomains"
       Header always set Content-Security-Policy "default-src 'self';"
   </IfModule>
   ```

   **Descripción:**
   - **`X-Content-Type-Options`:** Previene que el navegador interprete archivos con tipos MIME incorrectos.
   - **`X-Frame-Options`:** Bloquea la posibilidad de que la página sea cargada dentro de un iframe.
   - **`Strict-Transport-Security`:** Obliga el uso de HTTPS para futuras conexiones.
   - **`Content-Security-Policy`:** Restringe las fuentes de contenido permitidas.

---

## Autenticación Básica

La autenticación básica protege directorios restringidos solicitando credenciales.

### Configuración de Autenticación Básica

1. **Crear un archivo de contraseñas:**
   ```bash
   sudo htpasswd -c /etc/apache2/.htpasswd usuario
   ```

2. **Configurar el directorio protegido:**
   ```apache
   <Directory /var/www/restringido>
       AuthType Basic
       AuthName "Acceso Restringido"
       AuthUserFile /etc/apache2/.htpasswd
       Require valid-user
   </Directory>
   ```

3. **Reiniciar Apache:**
   ```bash
   sudo systemctl restart apache2
   ```

---

## Deshabilitar Métodos HTTP Inseguros

Restringir métodos inseguros como `TRACE` y `OPTIONS` puede reducir vectores de ataque.

#### Configuración:
```apache
<IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteCond %{REQUEST_METHOD} ^(TRACE|TRACK)
    RewriteRule .* - [F]
</IfModule>
```

---

Implementar estas configuraciones asegura que Apache sea más robusto contra amenazas comunes y vulnerabilidades.

