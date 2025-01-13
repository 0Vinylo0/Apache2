# Optimización

Optimizar Apache HTTP Server es crucial para garantizar un rendimiento eficiente, especialmente en servidores que manejan alto tráfico. A continuación, se describen estrategias y configuraciones clave para mejorar su rendimiento.

---

## Configuración de Workers

Apache utiliza módulos Multi-Processing (MPM) para gestionar cómo se manejan las solicitudes. Los MPM más comunes son `prefork`, `worker` y `event`.

### Configuración del MPM Prefork

- Adecuado para sitios que utilizan módulos no compatibles con threads, como `mod_php`.

#### Ejemplo:

```apache
<IfModule mpm_prefork_module>
    StartServers             5
    MinSpareServers          5
    MaxSpareServers         10
    MaxRequestWorkers      150
    MaxConnectionsPerChild 3000
</IfModule>
```

### Configuración del MPM Worker

- Más eficiente que `prefork`, utiliza threads para manejar solicitudes.

#### Ejemplo:

```apache
<IfModule mpm_worker_module>
    StartServers             2
    MinSpareThreads         25
    MaxSpareThreads         75
    ThreadLimit             64
    ThreadsPerChild         25
    MaxRequestWorkers      150
    MaxConnectionsPerChild 3000
</IfModule>
```

### Configuración del MPM Event

- Similar a `worker`, pero optimizado para manejar conexiones persistentes como HTTPS.

#### Ejemplo:

```apache
<IfModule mpm_event_module>
    StartServers             2
    MinSpareThreads         25
    MaxSpareThreads         75
    ThreadLimit             64
    ThreadsPerChild         25
    MaxRequestWorkers      150
    MaxConnectionsPerChild 3000
</IfModule>
```

---

## Habilitar Compresión con `mod_deflate`

La compresión reduce el tamaño de las respuestas HTTP, mejorando los tiempos de carga para los clientes.

### Activar el Módulo

```bash
sudo a2enmod deflate
sudo systemctl restart apache2
```

### Configuración Básica

```apache
<IfModule mod_deflate.c>
    AddOutputFilterByType DEFLATE text/html text/plain text/xml text/css text/javascript application/javascript application/json
</IfModule>
```

---

## Caching con `mod_cache`

El almacenamiento en caché mejora el rendimiento al servir contenido estático sin procesarlo repetidamente.

### Activar los Módulos

```bash
sudo a2enmod cache cache_disk
sudo systemctl restart apache2
```

### Configuración Básica

```apache
<IfModule mod_cache.c>
    CacheQuickHandler off
    CacheLock on
    CacheRoot "/var/cache/apache2/mod_cache_disk"
    CacheEnable disk /
    CacheHeader on
    CacheDefaultExpire 3600
    CacheMaxExpire 86400
    CacheIgnoreNoLastMod On
</IfModule>
```

---

## Optimización de Conexiones Persistentes

Las conexiones persistentes (`KeepAlive`) reducen la latencia al mantener abiertas las conexiones entre el cliente y el servidor para múltiples solicitudes.

### Configuración

```apache
KeepAlive On
MaxKeepAliveRequests 100
KeepAliveTimeout 5
```

---

## Deshabilitar Módulos Innecesarios

Reducir la cantidad de módulos cargados mejora el rendimiento y reduce el uso de recursos.

### Listar Módulos Activos

```bash
apachectl -M
```

### Desactivar un Módulo

```bash
sudo a2dismod nombre_modulo
sudo systemctl restart apache2
```

---

## Balanceo de Carga con `mod_proxy`

Para entornos con alto tráfico, el balanceo de carga distribuye las solicitudes entre múltiples servidores backend.

### Activar los Módulos

```bash
sudo a2enmod proxy proxy_balancer proxy_http lbmethod_byrequests
sudo systemctl restart apache2
```

### Configuración Básica

```apache
<Proxy "balancer://mycluster">
    BalancerMember http://backend1.ejemplo.com
    BalancerMember http://backend2.ejemplo.com
    ProxySet lbmethod=byrequests
</Proxy>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / balancer://mycluster/
    ProxyPassReverse / balancer://mycluster/
</VirtualHost>
```

---

Estas configuraciones y ajustes pueden marcar una gran diferencia en el rendimiento y escalabilidad de Apache.

**Fuente:** [Guía de rendimiento de Apache](https://httpd.apache.org/docs/current/misc/perf-tuning.html).
