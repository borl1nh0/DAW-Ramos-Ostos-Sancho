---
layout: default
title: UD5 - Servidores web
nav_order: 6
has_children: true
permalink: /ud5/
---

# UD5 - Administración de servidores web

## Contenidos Principales

- **Apache HTTP Server:** configuración, módulos, virtualhosting
- **Nginx:** servidor web de alto rendimiento, reverse proxy
- **Hosts virtuales:** múltiples sitios en un servidor
- **Módulos y extensiones:** mod_rewrite, mod_ssl, mod_proxy
- **Archivos de configuración y logs**
- **Optimización de rendimiento**
- **SSL/TLS en servidores web**

## Objetivos de Aprendizaje

Al completar esta unidad, serás capaz de:

✓ Instalar y configurar Apache y Nginx  
✓ Crear hosts virtuales en servidores web  
✓ Habilitar módulos necesarios  
✓ Configurar reescritura de URLs  
✓ Implementar proxy inverso  
✓ Optimizar rendimiento del servidor  
✓ Interpretar y gestionar logs  
✓ Implementar HTTPS  

## Conceptos Clave

### Apache vs Nginx

| Característica | Apache | Nginx |
|---|---|---|
| Arquitectura | Model basado en procesos (.htaccess) | Event-driven |
| Rendimiento | Bueno | Excelente para alto tráfico |
| Memoria | Mayor consumo | Menor consumo |
| Configuración | Más flexible | Más estricta |
| Módulos | Muchos disponibles | Menos, más enfocado |
| Uso | Legacy, hosting compartido | Moderno, alto rendimiento |

## Ejemplo Práctico

### 1. Instalación y Configuración Básica Apache

```bash
# Instalar Apache
sudo apt-get install apache2

# Habilitar módulos
sudo a2enmod rewrite
sudo a2enmod ssl
sudo a2enmod proxy
sudo a2enmod headers

# Crear virtual host
sudo nano /etc/apache2/sites-available/miapp.conf
```

### 2. Configuración Apache Virtual Host

```apache
<VirtualHost *:80>
    ServerName miapp.com
    ServerAlias www.miapp.com
    ServerAdmin admin@miapp.com
    
    DocumentRoot /var/www/miapp
    
    <Directory /var/www/miapp>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>
    
    # Logs
    ErrorLog ${APACHE_LOG_DIR}/miapp-error.log
    CustomLog ${APACHE_LOG_DIR}/miapp-access.log combined
    
    # Reescritura de URLs
    <IfModule mod_rewrite.c>
        RewriteEngine On
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteRule ^ index.php [QSA,L]
    </IfModule>
</VirtualHost>
```

### 3. Instalación y Configuración Nginx

```bash
# Instalar Nginx
sudo apt-get install nginx

# Iniciar servicio
sudo systemctl start nginx
sudo systemctl enable nginx

# Ver configuración
sudo nginx -t
```

### 4. Configuración Nginx Virtual Host

```nginx
server {
    listen 80;
    server_name miapp.com www.miapp.com;
    
    root /var/www/miapp;
    index index.html index.php;
    
    access_log /var/log/nginx/miapp-access.log;
    error_log /var/log/nginx/miapp-error.log;
    
    # Reescritura de URLs
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }
    
    # Procesamiento PHP
    location ~ \.php$ {
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```

### 5. Proxy Inverso (Nginx)

```nginx
upstream backend {
    server 127.0.0.1:3000;
    server 127.0.0.1:3001;
    keep_alive 64;
}

server {
    listen 80;
    server_name api.miapp.com;
    
    location / {
        proxy_pass http://backend;
        proxy_http_version 1.1;
        proxy_set_header Connection "";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### 6. Comandos Útiles

```bash
# Apache
sudo a2ensite miapp.conf         # Habilitar sitio
sudo a2dissite default.conf      # Deshabilitar sitio
sudo apache2ctl configtest       # Probar configuración
sudo systemctl reload apache2    # Recargar sin parar

# Nginx
sudo ln -s /etc/nginx/sites-available/miapp.conf \
           /etc/nginx/sites-enabled/
sudo nginx -t                    # Probar configuración
sudo systemctl reload nginx      # Recargar sin parar
```

## Logs Importantes

```bash
# Apache
tail -f /var/log/apache2/access.log
tail -f /var/log/apache2/error.log

# Nginx
tail -f /var/log/nginx/access.log
tail -f /var/log/nginx/error.log

# Analizar logs
grep "GET" /var/log/nginx/access.log | awk '{print $1}' | sort | uniq -c
```

## Recursos

- [Apache Documentation](https://httpd.apache.org/docs/)
- [Nginx Documentation](https://nginx.org/en/docs/)
- [Apache Modules](https://httpd.apache.org/docs/current/mod/)

## Actividades

1. Instalar y configurar Apache con 2 virtual hosts
2. Migrar configuración de Apache a Nginx
3. Implementar proxy inverso hacia aplicación backend
4. Configurar reescritura de URLs amigables
5. Analizar y optimizar logs de acceso

---

[← Anterior: UD4](../ud4/index.md) | [Siguiente: UD6 - App Servers →](../ud6/index.md)
