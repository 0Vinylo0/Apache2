# Logs y Monitorización

El registro de eventos y la monitorización del rendimiento son esenciales para mantener un servidor Apache eficiente y seguro. Apache proporciona herramientas integradas para gestionar logs y supervisar la actividad del servidor.

---

## Archivos de Registro Principales

Apache almacena los eventos del servidor en dos tipos principales de logs:

### 1. **Access Log**
- **Ubicación por defecto:**
  - Ubuntu/Debian: `/var/log/apache2/access.log`
  - CentOS/RedHat: `/var/log/httpd/access_log`
- **Descripción:** Registra todas las solicitudes HTTP realizadas al servidor, incluyendo detalles como:
  - IP del cliente
  - Método de solicitud (GET, POST, etc.)
  - Código de estado HTTP
  - Tamaño de la respuesta

#### Ejemplo de Entrada:
```
192.168.1.1 - - [12/Jan/2025:12:34:56 +0000] "GET /index.html HTTP/1.1" 200 1043
```

### 2. **Error Log**
- **Ubicación por defecto:**
  - Ubuntu/Debian: `/var/log/apache2/error.log`
  - CentOS/RedHat: `/var/log/httpd/error_log`
- **Descripción:** Registra errores relacionados con el servidor, como:
  - Problemas de configuración
  - Errores en módulos
  - Fallos en solicitudes HTTP

#### Ejemplo de Entrada:
```
[Mon Jan 12 12:34:56 2025] [error] [client 192.168.1.1] File does not exist: /var/www/html/favicon.ico
```

---

## Configuración de Logs

Los archivos de registro se configuran principalmente en los archivos de configuración de Apache, como `apache2.conf` o en bloques de Virtual Hosts.

### Configuración Básica:

```apache
ErrorLog ${APACHE_LOG_DIR}/error.log
CustomLog ${APACHE_LOG_DIR}/access.log combined
```

### Formatos de Log

El formato de los registros puede personalizarse mediante la directiva `LogFormat`:

```apache
LogFormat "%h %l %u %t \"%r\" %>s %b" common
LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" combined
```

- **`%h`:** Dirección IP del cliente.
- **`%t`:** Fecha y hora de la solicitud.
- **`%r`:** Solicitud completa (método, recurso y protocolo).
- **`%s`:** Código de estado HTTP.
- **`%b`:** Tamaño del cuerpo de la respuesta.

---

## Supervisión en Tiempo Real

### Comando `tail`
Visualiza los logs en tiempo real utilizando `tail`:

```bash
tail -f /var/log/apache2/access.log /var/log/apache2/error.log
```

### Herramientas de Análisis de Logs

1. **GoAccess**
   - Herramienta interactiva para analizar logs de acceso en tiempo real.
   - Instalación:
     ```bash
     sudo apt install goaccess
     ```
   - Uso:
     ```bash
     goaccess /var/log/apache2/access.log -o report.html --log-format=COMBINED
     ```

2. **AWStats**
   - Genera informes gráficos detallados sobre el tráfico del sitio web.
   - Instalación:
     ```bash
     sudo apt install awstats
     ```

---

## Monitorización del Rendimiento

### Herramientas Integradas

#### `mod_status`
Proporciona estadísticas en tiempo real sobre el rendimiento del servidor.

1. **Habilitar el módulo:**
   ```bash
   sudo a2enmod status
   sudo systemctl restart apache2
   ```

2. **Configurar el acceso a `mod_status`:**

   ```apache
   <Location /server-status>
       SetHandler server-status
       Require ip 192.168.1.0/24
   </Location>
   ```

3. **Acceder a las estadísticas:**
   - URL: `http://<tu-servidor>/server-status`

### Herramientas Externas

1. **Prometheus + Grafana**
   - Proporciona métricas detalladas y paneles gráficos.
   - Requiere instalar un exportador como `apache_exporter`.

2. **ELK Stack (Elasticsearch, Logstash, Kibana)**
   - Ideal para analizar grandes volúmenes de datos de logs y generar gráficos interactivos.

---

Implementar una gestión adecuada de logs y herramientas de monitorización asegura que Apache funcione de manera óptima y que los problemas se detecten a tiempo.

**Fuente:** [Logs y diagnóstico en Apache](https://httpd.apache.org/docs/current/logs.html).
