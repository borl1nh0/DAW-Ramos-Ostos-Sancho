---
layout: default
title: UD7 - Securización
nav_order: 8
has_children: true
permalink: /ud7/
---

# UD7 - Securización de aplicaciones web

## Contenidos Principales

- **HTTPS/TLS:** protocolo seguro, certificados digitales
- **Certificados SSL/TLS:** Let's Encrypt, auto-renovación
- **Cabeceras de seguridad:** CSP, HSTS, X-Frame-Options
- **Firewall:** reglas de entrada/salida, puertos
- **fail2ban:** protección contra ataques de fuerza bruta
- **OWASP:** top 10 vulnerabilidades web
- **Escaneo de vulnerabilidades y pruebas de seguridad**

## Objetivos de Aprendizaje

Al completar esta unidad, serás capaz de:

✓ Implementar HTTPS en servidores web  
✓ Obtener y renovar certificados Let's Encrypt  
✓ Configurar cabeceras de seguridad  
✓ Implementar firewall con reglas efectivas  
✓ Proteger contra ataques comunes (DoS, brute force)  
✓ Escanear vulnerabilidades  
✓ Implementar buenas prácticas de seguridad  
✓ Responder ante incidentes de seguridad  

## Conceptos Clave

### Capas de Seguridad

```
Aplicación
    ↓
Cabeceras HTTP
    ↓
HTTPS/TLS
    ↓
Firewall (iptables/ufw)
    ↓
Sistema Operativo (SELinux/AppArmor)
    ↓
Red (VPC, Security Groups)
    ↓
Física (Centro de datos)
```

## Ejemplo Práctico

### 1. Certificados Let's Encrypt con Certbot

```bash
# Instalar Certbot
sudo apt-get install certbot python3-certbot-nginx

# Obtener certificado
sudo certbot certonly --nginx -d miapp.com -d www.miapp.com

# Ver certificados instalados
sudo certbot certificates

# Renovación automática
sudo systemctl enable certbot.timer
sudo systemctl start certbot.timer

# Renovar manualmente
sudo certbot renew --dry-run
```

### 2. Configuración HTTPS en Nginx

```nginx
server {
    listen 443 ssl http2;
    server_name miapp.com www.miapp.com;
    
    # Certificados SSL
    ssl_certificate /etc/letsencrypt/live/miapp.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/miapp.com/privkey.pem;
    
    # Configuración TLS segura
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;
    
    # Cabeceras de seguridad
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Referrer-Policy "no-referrer-when-downgrade" always;
    add_header Permissions-Policy "accelerometer=(), camera=(), microphone=(), geolocation=()" always;
    
    # CSP - Content Security Policy
    add_header Content-Security-Policy "default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline';" always;
    
    location / {
        proxy_pass http://localhost:3000;
    }
}

# Redirección HTTP → HTTPS
server {
    listen 80;
    server_name miapp.com www.miapp.com;
    return 301 https://$server_name$request_uri;
}
```

### 3. Cabeceras de Seguridad Importantes

| Cabecera | Propósito | Ejemplo |
|----------|-----------|---------|
| HSTS | Forzar HTTPS | `Strict-Transport-Security: max-age=31536000` |
| X-Content-Type | Evitar MIME sniffing | `X-Content-Type-Options: nosniff` |
| X-Frame | Clickjacking | `X-Frame-Options: SAMEORIGIN` |
| CSP | Inyección de código | `Content-Security-Policy: default-src 'self'` |
| X-XSS-Protection | XSS protection | `X-XSS-Protection: 1; mode=block` |

### 4. Firewall con UFW

```bash
# Instalar UFW
sudo apt-get install ufw

# Configuración básica
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Permitir puertos
sudo ufw allow ssh          # Puerto 22
sudo ufw allow 80/tcp       # HTTP
sudo ufw allow 443/tcp      # HTTPS
sudo ufw allow 3000/tcp     # App

# Denegar puertos
sudo ufw deny 3306/tcp      # MySQL (solo local)

# Activar firewall
sudo ufw enable

# Ver reglas
sudo ufw status
```

### 5. fail2ban - Protección contra Ataques

```bash
# Instalar fail2ban
sudo apt-get install fail2ban

# Configuración del servicio
sudo nano /etc/fail2ban/jail.local
```

```ini
[DEFAULT]
bantime = 3600
findtime = 600
maxretry = 5

[sshd]
enabled = true
port = ssh
logpath = /var/log/auth.log
maxretry = 3
bantime = 1800

[nginx-http-auth]
enabled = true
port = http,https
logpath = /var/log/nginx/error.log

[nginx-limit-req]
enabled = true
port = http,https
logpath = /var/log/nginx/error.log

[nginx-noscript]
enabled = true
port = http,https
logpath = /var/log/nginx/access.log
```

```bash
# Iniciar servicio
sudo systemctl start fail2ban
sudo systemctl enable fail2ban

# Ver IPs bloqueadas
sudo fail2ban-client status sshd
sudo fail2ban-client set sshd unbanip 192.168.1.100

# Monitoreo
sudo tail -f /var/log/fail2ban.log
```

### 6. Top 10 OWASP - Prevención

```python
# Ejemplo: Prevenir SQL Injection en Python

# ❌ INSEGURO
def get_user_insecure(user_id):
    query = f"SELECT * FROM users WHERE id = {user_id}"
    return db.execute(query)

# ✅ SEGURO - Usar prepared statements
def get_user_secure(user_id):
    query = "SELECT * FROM users WHERE id = ?"
    return db.execute(query, (user_id,))
```

### 7. Escaneo de Vulnerabilidades

```bash
# OWASP ZAP
sudo apt-get install zaproxy

# Nessus
# Descargar de https://www.tenable.com/downloads/nessus

# Verificar certificados
openssl s_client -connect miapp.com:443

# SSL Labs test
# Ir a https://www.ssllabs.com/ssltest/

# Verificar headers
curl -I https://miapp.com
```

## Recursos

- [Let's Encrypt Documentation](https://letsencrypt.org/docs/)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [OWASP Security Headers](https://owasp.org/www-project-secure-headers/)
- [Mozilla SSL Configuration Generator](https://ssl-config.mozilla.org/)
- [fail2ban Documentation](https://www.fail2ban.org/)

## Actividades

1. Implementar HTTPS con Let's Encrypt
2. Configurar todas las cabeceras de seguridad
3. Implementar firewall con UFW/iptables
4. Configurar fail2ban para protección contra brute force
5. Realizar escaneo de vulnerabilidades con OWASP ZAP

---

[← Anterior: UD6](../ud6/index.md) | [Siguiente: UD8 - DNS →](../ud8/index.md)
