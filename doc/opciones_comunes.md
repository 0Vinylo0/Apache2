# Opciones de Configuración Comunes

Apache HTTP Server permite configurar múltiples aspectos del servidor para adaptarlo a diferentes necesidades. A continuación, se detallan algunas de las opciones más utilizadas y su configuración.

---

## DocumentRoot

Define el directorio raíz donde se almacenan los archivos web del servidor.

- **Directiva:** `DocumentRoot`
- **Ubicación típica:** En los archivos de configuración de un Virtual Host.

#### Ejemplo:

```apache
DocumentRoot /var/www/html
```

---

## Directory

Controla los permisos y las opciones aplicadas a un directorio específico.

- **Directiva:** `<Directory>`

#### Ejemplo:

```apache
<Directory /var/www/html>
    Options Indexes FollowSymLinks       # Permitir listados y enlaces simbólicos.
    AllowOverride All                    # Permitir el uso de archivos .htaccess.
    Require all granted                  # Acceso permitido a todos los usuarios.
</Directory>
```

### Opciones Comunes de Directory

- **`Indexes`:** Permite mostrar listados del contenido del directorio si no hay un archivo de índice.
- **`FollowSymLinks`:** Habilita el uso de enlaces simbólicos.
- **`AllowOverride`:** Define si los archivos `.htaccess` pueden sobrescribir configuraciones.
- **`Require`:** Controla el acceso (ejemplo: `Require all granted`, `Require ip 192.168.1.0/24`).

---

## Virtual Hosts

Permite alojar múltiples sitios web en el mismo servidor, diferenciándolos por el dominio o la dirección IP.

- **Directiva:** `<VirtualHost>`

#### Ejemplo:

```apache
<VirtualHost *:80>
    ServerName ejemplo.com
    ServerAlias www.ejemplo.com
    DocumentRoot /var/www/ejemplo

    <Directory /var/www/ejemplo>
        Options -Indexes +FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/ejemplo_error.log
    CustomLog ${APACHE_LOG_DIR}/ejemplo_access.log combined
</VirtualHost>
```

### Explicación de las Opciones

- **`ServerName`:** Nombre principal del dominio que apunta al Virtual Host.
- **`ServerAlias`:** Alias adicionales para el dominio (ejemplo: `www.ejemplo.com`, `subdominio.ejemplo.com`).
- **`DocumentRoot`:** Directorio raíz de los archivos para este sitio.
- **`ErrorLog` y `CustomLog`:** Especifican los archivos de registro para errores y accesos.

---

## AccessFileName

La directiva `AccessFileName` define el nombre de los archivos utilizados para configurar permisos y opciones de acceso por directorio. Por defecto, Apache utiliza `.htaccess`.

- **Uso principal:** Controlar configuraciones locales en directorios específicos sin modificar los archivos de configuración global.

#### Ejemplo:

```apache
AccessFileName .htaccess
```

### Explicación Adicional

Cuando `AllowOverride` está habilitado, Apache busca el archivo `.htaccess` en cada directorio del camino hacia el recurso solicitado. Este archivo puede contener configuraciones para:

- Redirecciones (mediante `mod_rewrite`).
- Control de acceso (como autentificación básica).
- Encabezados HTTP personalizados.

---

## DirectoryIndex

La directiva `DirectoryIndex` especifica qué archivos buscará Apache como página de inicio cuando un usuario acceda a un directorio.

- **Uso principal:** Definir el orden de prioridad para los archivos de índice.

#### Ejemplo:

```apache
DirectoryIndex index.html index.php home.html
```

### Explicación Adicional

En este ejemplo, Apache buscará en este orden:
1. `index.html`
2. `index.php`
3. `home.html`

Si ninguno de estos archivos está presente, Apache puede devolver un listado del directorio (si está habilitado) o un error 403 (prohibido).

---

## ErrorDocument

La directiva `ErrorDocument` permite personalizar las páginas de error que se muestran a los usuarios cuando ocurren problemas como un recurso no encontrado (404) o un error interno del servidor (500).

#### Ejemplo:

```apache
ErrorDocument 404 /custom_404.html
ErrorDocument 500 "Error interno del servidor. Por favor, contacte al administrador."
```

### Explicación Adicional

1. **Redirección a un archivo interno:**
   ```apache
   ErrorDocument 404 /errors/404.html
   ```
   En este caso, Apache buscará el archivo `/errors/404.html` en el `DocumentRoot` configurado.

2. **Mostrar un mensaje personalizado:**
   ```apache
   ErrorDocument 500 "Ha ocurrido un error inesperado."
   ```
   Apache mostrará este texto directamente en la página de error.

3. **Redirección a una URL externa:**
   ```apache
   ErrorDocument 403 http://ejemplo.com/prohibido.html
   ```
   Redirige al usuario a un sitio web externo.

---

Estas opciones son esenciales para personalizar el comportamiento de Apache y administrar el acceso y la estructura de los sitios alojados en el servidor.


