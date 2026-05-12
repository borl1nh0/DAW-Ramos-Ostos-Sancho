---
layout: default
title: UD8 - DNS
nav_order: 9
has_children: true
permalink: /ud8/
---

# UD8 - Servicios de nombre de dominio (DNS)

## Contenidos Principales

- **Sistema DNS:** arquitectura, jerarquía, servidores
- **Registro de dominios:** procedimientos, costos, gestión
- **Tipos de registros DNS:** A, AAAA, CNAME, MX, NS, TXT, SRV
- **BIND9:** servidor DNS autoritativo
- **Resolución de nombres:** cliente, servidor, iterativa, recursiva
- **TTL (Time To Live):** caché, propagación
- **DNS inverso:** resolución reversa
- **Seguridad DNS:** DNSSEC

## Objetivos de Aprendizaje

Al completar esta unidad, serás capaz de:

✓ Entender la jerarquía y arquitectura del DNS  
✓ Configurar registros DNS correctamente  
✓ Instalar y configurar servidores DNS (BIND9)  
✓ Resolver problemas de propagación DNS  
✓ Implementar DNS inverso  
✓ Optimizar rendimiento con TTL  
✓ Implementar seguridad DNS (DNSSEC)  
✓ Monitorear y auditar registros DNS  

## Conceptos Clave

### Jerarquía DNS

```
                    .
                    |
        ┌───────────┼───────────┐
        |           |           |
       com         org         es
        |           |           |
    ┌───┴───┐   ┌───┴───┐   ┌───┴───┐
   acme   google mozilla  rfc  ietf  iespsur
    |       |      |      |     |      |
  www     www    www    www   ftp    dns
```

### Tipos de Registros

| Registro | Propósito | Ejemplo |
|----------|-----------|---------|
| **A** | IPv4 address | `miapp.com → 192.0.2.1` |
| **AAAA** | IPv6 address | `miapp.com → 2001:db8::1` |
| **CNAME** | Alias | `www.miapp.com → miapp.com` |
| **MX** | Mail server | `miapp.com → mail.miapp.com` |
| **NS** | Name server | `miapp.com → ns1.miapp.com` |
| **TXT** | Texto (SPF, DKIM) | `v=spf1 mx -all` |
| **SRV** | Servicio | `_sip._tcp.miapp.com` |
| **SOA** | Start of authority | Información autoritativa |

## Ejemplo Práctico

### 1. Resolución de Nombres

```bash
# Consultar registro A
nslookup miapp.com
dig miapp.com

# Consultar tipo específico
dig miapp.com A
dig miapp.com MX
dig miapp.com TXT

# Resolver inverso
dig -x 8.8.8.8
nslookup 8.8.8.8

# Usar servidor DNS específico
dig @8.8.8.8 miapp.com
nslookup miapp.com 8.8.8.8

# Rastrear resolución
dig miapp.com +trace

# Ver TTL actual
dig miapp.com +noall +answer
```

### 2. Instalación BIND9

```bash
# Instalar BIND9
sudo apt-get install bind9 bind9-utils bind9-doc

# Configuración básica
sudo nano /etc/bind/named.conf.local
```

### 3. Zonda DNS Primaria (named.conf.local)

```
// Zona DNS para miapp.com
zone "miapp.com" {
    type master;
    file "/etc/bind/zones/db.miapp.com";
    allow-transfer { 192.0.2.2; };  // Servidor secundario
};

// Zona inversa
zone "2.0.192.in-addr.arpa" {
    type master;
    file "/etc/bind/zones/db.192.0.2";
};
```

### 4. Archivo de Zona (db.miapp.com)

```
$TTL 3600
@   IN  SOA ns1.miapp.com. admin.miapp.com. (
            2024010101  ; Serial (YYYYMMDDNN)
            7200        ; Refresh (sync cada 2h)
            3600        ; Retry (reintento cada 1h)
            1209600     ; Expire (expira en 14 días)
            3600 )      ; Minimum TTL
    IN  NS  ns1.miapp.com.
    IN  NS  ns2.miapp.com.
    IN  A   192.0.2.1

ns1     IN  A   192.0.2.10
ns2     IN  A   192.0.2.11
www     IN  A   192.0.2.1
mail    IN  A   192.0.2.5
api     IN  CNAME www

@       IN  MX  10 mail.miapp.com.
@       IN  TXT "v=spf1 mx -all"

_acme-challenge IN TXT "validate-token-aqui"
```

### 5. Zona Inversa (db.192.0.2)

```
$TTL 3600
@   IN  SOA ns1.miapp.com. admin.miapp.com. (
            2024010101
            7200
            3600
            1209600
            3600 )
    IN  NS  ns1.miapp.com.
    IN  NS  ns2.miapp.com.

1   IN  PTR miapp.com.
5   IN  PTR mail.miapp.com.
10  IN  PTR ns1.miapp.com.
11  IN  PTR ns2.miapp.com.
```

### 6. Comandos Operacionales

```bash
# Verificar configuración
sudo named-checkconf
sudo named-checkzone miapp.com /etc/bind/zones/db.miapp.com

# Iniciar servicio
sudo systemctl start bind9
sudo systemctl enable bind9

# Ver logs
sudo tail -f /var/log/syslog | grep named

# Recargar zona
sudo rndc reload
rndc reload miapp.com

# Flush de caché
sudo rndc flush

# Estadísticas
rndc stats
```

### 7. Propagación DNS y TTL

```bash
# Monitorear propagación (durante cambios)
watch -n 1 'dig @8.8.8.8 miapp.com +short'
watch -n 1 'dig @1.1.1.1 miapp.com +short'

# Ver TTL actual
dig miapp.com +noall +answer | awk '{print $2}'

# Registro de cambios importantes
# TTL = 300  durante cambios (propagación rápida)
# TTL = 3600 en producción normal
```

### 8. DNSSEC - Seguridad DNS

```bash
# Generar claves DNSSEC
sudo dnssec-keygen -a NSEC3RSASHA256 -b 2048 -n ZONE miapp.com
sudo dnssec-keygen -a NSEC3RSASHA256 -b 1024 -n ZONE -f KSK miapp.com

# Firmar zona
sudo dnssec-signzone -A -3 $(head -c 1000 /dev/urandom | md5sum | cut -c1-16) \
                     -N increment -o miapp.com -t \
                     /etc/bind/zones/db.miapp.com

# Publicar claves DS en registro
cat /etc/bind/zones/db.miapp.com.signed | grep "^miapp.com. IN DS"

# Validar DNSSEC
dig +dnssec miapp.com
```

## Herramientas de Diagnóstico

```bash
# DNS Checker en línea
# https://dnschecker.org/

# WHOIS
whois miapp.com

# Host command
host miapp.com
host -l miapp.com  # Transferencia de zona (si permitida)

# nslookup con debug
nslookup -debug miapp.com

# tcpdump para análisis
sudo tcpdump -i eth0 -n 'udp port 53'
```

## Recursos

- [BIND9 Documentation](https://bind9.readthedocs.io/)
- [RFC 1035 - DNS Specification](https://tools.ietf.org/html/rfc1035)
- [DNSSEC Introduction](https://www.icann.org/dnssec/)
- [DNS Propagation Checker](https://dnschecker.org/)

## Actividades

1. Configurar BIND9 como servidor DNS autoritativo
2. Crear registros DNS para un dominio de prueba
3. Implementar zona inversa
4. Diagnosticar problemas de resolución DNS
5. Implementar DNSSEC en zona existente

---

[← Anterior: UD7](../ud7/index.md) | [Siguiente: UD9 - Control de versiones →](../ud9/index.md)
