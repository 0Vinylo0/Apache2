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

### Usar Certbot para Certificados SSL/TLS

Certbot simplifica la obtención y configuración de certificados SSL/TLS de Let's Encrypt.

1. **Instalar Certbot:**
   ```bash
   sudo apt install certbot python3-certbot-apache -y
   ```

2. **Obtener un certificado SSL/TLS:**
   ```bash
   sudo certbot --apache -d yourdomain.com -d www.yourdomain.com
   ```
   Sigue las instrucciones en pantalla para completar la configuración. ***IMPORTANTE*** La web o el virtual host tiene que estar en el puerto 80

3. **Configurar el sitio***
    Asegúrate de que contenga algo como lo siguiente:
   ```bash
   <VirtualHost *:443>
    ServerName tu-dominio.com
    ServerAlias www.tu-dominio.com
    DocumentRoot /var/www/seguro

    SSLEngine on
    SSLCertificateFile /etc/letsencrypt/live/tu-dominio.com/fullchain.pem
    SSLCertificateKeyFile /etc/letsencrypt/live/tu-dominio.com/privkey.pem

    <Directory /var/www/seguro>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/seguro_error.log
    CustomLog ${APACHE_LOG_DIR}/seguro_access.log combined
   </VirtualHost>
   ```

5. **Verificar la configuración:**
   Accede a tu dominio usando HTTPS: `https://yourdomain.com`. Puedes usar herramientas como [SSL Labs](https://www.ssllabs.com/ssltest/) para verificar la seguridad del certificado.

6. **Renovación automática:**
   Certbot configura automáticamente un cron job para renovar certificados. Puedes verificarlo con:
   ```bash
   sudo systemctl list-timers | grep certbot
   ```
   O probar manualmente:
   ```bash
   sudo certbot renew --dry-run
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

