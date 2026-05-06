---
layout: default
title: UD6 - Servidores de aplicaciones
nav_order: 7
has_children: true
permalink: /ud6/
---

# UD6 - Administración de servidores de aplicaciones web

## Contenidos Principales

- **Tomcat:** servidor de aplicaciones Java
- **Node.js y PM2:** ejecución y gestión de aplicaciones Node
- **Gunicorn/uWSGI:** servidores WSGI para Python
- **PHP-FPM:** gestor de procesos para PHP
- **Balanceo de carga:** distribución de tráfico
- **Supervisión de procesos:** uptime, monitoreo
- **Clustering y alta disponibilidad**

## Objetivos de Aprendizaje

Al completar esta unidad, serás capaz de:

✓ Instalar y configurar servidores de aplicaciones  
✓ Gestionar procesos de aplicaciones con PM2  
✓ Implementar balanceo de carga  
✓ Configurar clustering para alta disponibilidad  
✓ Monitorear y mantener aplicaciones en producción  
✓ Escalar horizontalmente  
✓ Implementar rolling updates sin downtime  

## Conceptos Clave

### Servidores de Aplicaciones por Tecnología

```
Lenguaje    Servidor           Función
─────────────────────────────────────────
Java        Tomcat, JBoss      Ejecuta aplicaciones Java/JSP
Node.js     Express, Fastify   Runtime JavaScript
Python      Gunicorn, uWSGI    Servidor WSGI
PHP         PHP-FPM            FastCGI Process Manager
.NET        IIS, Kestrel       Servidor Microsoft
```

## Ejemplo Práctico

### 1. Node.js con PM2

```bash
# Instalar Node.js
sudo apt-get install nodejs npm

# Instalar PM2 globalmente
sudo npm install -g pm2

# Iniciar aplicación
pm2 start app.js --name "miapp"

# Listar procesos
pm2 list

# Ver logs en tiempo real
pm2 logs miapp

# Reiniciar aplicación
pm2 restart miapp

# Monitoreo
pm2 monit

# Guardias del sistema
pm2 startup
pm2 save
```

### 2. Configuración PM2 (ecosystem.config.js)

```javascript
module.exports = {
  apps: [
    {
      name: 'api-server',
      script: './server.js',
      instances: 4,
      exec_mode: 'cluster',
      max_memory_restart: '1G',
      env: {
        NODE_ENV: 'production',
        PORT: 3000
      },
      error_file: './logs/error.log',
      out_file: './logs/out.log',
      log_date_format: 'YYYY-MM-DD HH:mm:ss Z'
    },
    {
      name: 'worker',
      script: './worker.js',
      instances: 2,
      exec_mode: 'cluster'
    }
  ]
};
```

```bash
# Ejecutar con ecosystem
pm2 start ecosystem.config.js
```

### 3. Gunicorn (Python)

```bash
# Instalar Gunicorn
pip install gunicorn

# Ejecutar aplicación Flask/Django
gunicorn -w 4 -b 0.0.0.0:8000 app:app

# Con opciones avanzadas
gunicorn -w 4 \
         --threads 2 \
         --worker-class sync \
         --max-requests 1000 \
         --max-requests-jitter 50 \
         --timeout 60 \
         -b 0.0.0.0:8000 \
         app:app
```

### 4. Supervisor - Monitoreo de Procesos

```ini
# /etc/supervisor/conf.d/myapp.conf
[program:myapp]
command=/usr/bin/python3 /opt/myapp/app.py
directory=/opt/myapp
user=www-data
autostart=true
autorestart=true
redirect_stderr=true
stdout_logfile=/var/log/supervisor/myapp.log
environment=PATH="/opt/myapp/.venv/bin"
```

```bash
# Controlar servicios
sudo supervisorctl reread
sudo supervisorctl update
sudo supervisorctl start myapp
sudo supervisorctl status
```

### 5. Balanceo de Carga Nginx

```nginx
upstream app_cluster {
    least_conn;  # Algoritmo: conexiones menos recientes
    
    server 127.0.0.1:3000 weight=2;
    server 127.0.0.1:3001;
    server 127.0.0.1:3002;
    
    # Health check downstream (nginx plus)
    check interval=3000 rise=2 fall=5 timeout=1000 type=http;
    check_http_send "GET / HTTP/1.0\r\n\r\n";
    check_http_expect_alive http_2xx;
}

server {
    listen 80;
    server_name api.miapp.com;
    
    location / {
        proxy_pass http://app_cluster;
        proxy_redirect off;
    }
}
```

### 6. PHP-FPM

```bash
# Instalar PHP-FPM
sudo apt-get install php-fpm

# Configuración
sudo nano /etc/php/8.1/fpm/pool.d/www.conf

# Iniciar servicio
sudo systemctl start php8.1-fpm
```

## Clustering con Node.js

```javascript
// Clustering nativo (sin PM2)
const cluster = require('cluster');
const os = require('os');
const express = require('express');

if (cluster.isMaster) {
    const numCPUs = os.cpus().length;
    
    for (let i = 0; i < numCPUs; i++) {
        cluster.fork();
    }
    
    cluster.on('exit', (worker, code, signal) => {
        console.log(`Worker ${worker.process.pid} died`);
        cluster.fork(); // Reiniciar proceso muerto
    });
} else {
    const app = express();
    app.get('/', (req, res) => {
        res.send(`Process ${process.pid}`);
    });
    
    app.listen(3000);
}
```

## Recursos

- [PM2 Documentation](https://pm2.keymetrics.io/docs/)
- [Gunicorn Documentation](https://docs.gunicorn.org/)
- [Supervisor Documentation](http://supervisord.org/)
- [Node.js Cluster Module](https://nodejs.org/api/cluster.html)
- [Tomcat Documentation](https://tomcat.apache.org/tomcat-10.0-doc/)

## Actividades

1. Desplegar aplicación Node.js con PM2 en clustering
2. Configurar Gunicorn para aplicación Python
3. Implementar balanceo de carga con Nginx
4. Monitorear procesos con Supervisor
5. Implementar rolling updates sin downtime

---

[← Anterior: UD5](../ud5/index.md) | [Siguiente: UD7 - Securización →](../ud7/index.md)
