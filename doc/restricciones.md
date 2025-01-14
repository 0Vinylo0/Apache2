# Restricciones en Directivas de Apache 2.4

## Introducción
En Apache 2.4, las restricciones de acceso y las políticas de autenticación se controlan principalmente mediante la directiva `Require` y sus variantes lógicas (`RequireAll`, `RequireAny`, `RequireNone`).

---

## Directivas de Acceso
### 1. **Allow/Deny en Apache 2.4**
En Apache 2.4, las antiguas directivas `Order`, `Allow`, y `Deny` han sido reemplazadas por `Require`. Esto permite una configuración más legible y flexible.

#### Ejemplo: Acceso Global Permitido
```apache
<Directory "/var/www">
    Require all granted
</Directory>
```

#### Ejemplo: Acceso Global Denegado
```apache
<Directory "/">
    Require all denied
</Directory>
```

---

## Operadores Lógicos
Apache 2.4 introduce operadores lógicos para combinar múltiples condiciones de acceso.

### 1. **RequireAll**
Todas las condiciones deben cumplirse para permitir el acceso.
```apache
<Directory "/var/www/privado">
    <RequireAll>
        Require ip 192.168.0
        Require user usuario1
    </RequireAll>
</Directory>
```

### 2. **RequireAny**
Al menos una de las condiciones debe cumplirse para permitir el acceso.
```apache
<Directory "/var/www/complejo">
    <RequireAny>
        Require ip 10.0.0.0/8
        Require group admins
    </RequireAny>
</Directory>
```

### 3. **RequireNone**
Ninguna de las condiciones debe cumplirse para permitir el acceso.
```apache
<Directory "/var/www/prohibido">
    <RequireNone>
        Require ip 192.168.0
        Require group noaccess
    </RequireNone>
</Directory>
```

---

## Autenticación
### 1. **Autenticación Básica**
La autenticación básica utiliza nombres de usuario y contraseñas almacenadas en texto plano (hash).

#### Ejemplo de Configuración
```apache
<Directory "/var/www/privado">
    AuthType Basic
    AuthName "Zona Restringida"
    AuthUserFile "/etc/apache2/claves/passwd.txt"
    Require valid-user
</Directory>
```

#### Comando para Crear Usuarios
```bash
htpasswd -c /etc/apache2/claves/passwd.txt usuario1
```

### 2. **Autenticación Digest**
La autenticación Digest mejora la seguridad al evitar que las contraseñas viajen en texto plano.

#### Ejemplo de Configuración
```apache
<Directory "/var/www/digest">
    AuthType Digest
    AuthName "Zona Protegida"
    AuthUserFile "/etc/apache2/claves/digest.txt"
    Require valid-user
</Directory>
```

#### Comando para Crear Usuarios Digest
```bash
htdigest -c /etc/apache2/claves/digest.txt dominio usuario1
```

---

## Ejemplo Completo
Configuración combinada de políticas de acceso y autenticación.
```apache
<VirtualHost *:80>
    ServerName www.ejemplo.org
    DocumentRoot /var/www/ejemplo

    # Restricción Global
    <Directory />
        Require all denied
    </Directory>

    # Directorio Público
    <Directory "/var/www/ejemplo">
        Require all granted
    </Directory>

    # Directorio Privado con Restricciones Combinadas
    <Directory "/var/www/ejemplo/privado">
        <RequireAll>
            Require ip 192.168.0
            Require user usuario1
        </RequireAll>

        AuthType Basic
        AuthName "Zona Restringida"
        AuthUserFile "/etc/apache2/claves/passwd.txt"
        Require valid-user
    </Directory>

    # Directorio Protegido con Digest
    <Directory "/var/www/ejemplo/digest">
        AuthType Digest
        AuthName "Zona Protegida"
        AuthUserFile "/etc/apache2/claves/digest.txt"
        Require valid-user
    </Directory>
</VirtualHost>
```

---

## Diagnóstico y Solución de Problemas
1. **Errores Sintácticos**:
   - Revisa los logs en `/var/log/apache2/error.log`.
2. **Comprobación de Módulos**:
   - Asegúrate de que los módulos están habilitados:
     ```bash
     sudo a2enmod auth_basic auth_digest
     sudo systemctl restart apache2
     ```

---

## Notas Finales
- Usa `Require ip` y `Require user` con combinaciones de operadores lógicos para configuraciones complejas.
- En entornos de producción, complementa estas configuraciones con HTTPS para proteger los datos en tránsito.
