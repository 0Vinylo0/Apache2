# Directivas y Opciones de un Virtual Host en Apache

Un Virtual Host en Apache permite alojar múltiples sitios web en un único servidor, ya sea por nombre, IP o puerto.

---

## Estructura Básica
Un Virtual Host se define dentro de un bloque `<VirtualHost>`. Aquí está la estructura mínima:

```apache
<VirtualHost *:80>
    ServerName ejemplo.com
    DocumentRoot /var/www/ejemplo

    ErrorLog ${APACHE_LOG_DIR}/ejemplo_error.log
    CustomLog ${APACHE_LOG_DIR}/ejemplo_access.log combined
</VirtualHost>
```

---

## Directivas Principales

### 1. **ServerName**
Especifica el nombre del host que este Virtual Host responde.

```apache
ServerName ejemplo.com
```

### 2. **ServerAlias**
Define nombres alternativos para el Virtual Host.

```apache
ServerAlias www.ejemplo.com ejemplo.org
```

### 3. **DocumentRoot**
Directorio donde se encuentran los archivos del sitio web.

```apache
DocumentRoot /var/www/ejemplo
```

### 4. **ErrorLog**
Ruta del archivo de log para los errores.

```apache
ErrorLog ${APACHE_LOG_DIR}/ejemplo_error.log
```

### 5. **CustomLog**
Define el archivo y formato para los registros de acceso.

```apache
CustomLog ${APACHE_LOG_DIR}/ejemplo_access.log combined
```

---

## Opciones Adicionales

### 1. **Directory**
Configura permisos y opciones para un directorio específico.

```apache
<Directory /var/www/ejemplo>
    Options Indexes FollowSymLinks
    AllowOverride All
    Require all granted
</Directory>
```

#### Opciones:
- **Indexes**: Permite listar los archivos del directorio si no hay un archivo `index`.
- **FollowSymLinks**: Permite seguir enlaces simbólicos.
- **AllowOverride**: Permite sobrescribir configuraciones con `.htaccess`.
- **Require**: Controla el acceso (e.g., `Require all granted`).

### 2. **Alias**
Crea rutas virtuales para directorios físicos.

```apache
Alias /imagenes /var/www/ejemplo/imagenes

<Directory /var/www/ejemplo/imagenes>
    Options Indexes
    Require all granted
</Directory>
```

### 3. **Redirect**
Redirecciona URLs.

```apache
Redirect "/antiguo" "/nuevo"
Redirect permanent "/viejo" "https://ejemplo.com/nuevo"
```

### 4. **Proxy**
Actúa como proxy inverso (requiere módulo `mod_proxy`).

```apache
ProxyPass "/api" "http://backend.local:8080/"
ProxyPassReverse "/api" "http://backend.local:8080/"
```
---

## Configuraciones Especiales

### 1. **Logs por Nivel**
Puedes ajustar el nivel de detalle en los logs.

```apache
LogLevel warn
```
Valores disponibles: `emerg`, `alert`, `crit`, `error`, `warn`, `notice`, `info`, `debug`.

### 2. **Configuración de Caché**
Mejora el rendimiento configurando la caché.

```apache
<IfModule mod_cache.c>
    CacheEnable disk "/"
    CacheRoot "/var/cache/apache2"
</IfModule>
```
---

## Notas Finales
- Asegúrate de habilitar los módulos necesarios con `a2enmod`.
- Reinicia Apache después de modificar configuraciones: `sudo systemctl restart apache2`.
