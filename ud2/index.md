---
layout: default
title: UD2 - Conexiones remotas
nav_order: 3
has_children: true
permalink: /ud2/
---

# UD2 - Conexiones remotas para administración de servidores web

## Contenidos Principales

- **Protocolo SSH (Secure Shell):** autenticación segura, conexión remota
- **Transferencia de archivos:** SCP, SFTP
- **Criptografía de clave pública/privada**
- **Túneles SSH y port forwarding**
- **Administración remota de servidores**
- **Automatización con scripts SSH**

## Objetivos de Aprendizaje

Al completar esta unidad, serás capaz de:

✓ Conectarse remotamente a servidores usando SSH  
✓ Generar y gestionar pares de claves públicas/privadas  
✓ Transferir archivos de forma segura (SCP/SFTP)  
✓ Configurar túneles SSH para acceso seguro  
✓ Ejecutar comandos remotos de forma segura  
✓ Automatizar tareas administrativas  

## Conceptos Clave

### SSH (Secure Shell)

SSH es un protocolo criptográfico para:
- Conexión remota segura a servidores
- Ejecución de comandos remotos
- Transferencia segura de archivos

### Par de Claves Pública/Privada

```
┌─────────────────┐         ┌──────────────────┐
│  Clave Privada  │  ←→     │  Clave Pública   │
│  (máquina local)│         │  (servidor)      │
└─────────────────┘         └──────────────────┘
       ↓ Firma                      ↑ Verifica
```

## Ejemplo Práctico

### 1. Generar Par de Claves SSH

```bash
# Crear par de claves SSH
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa
# Genera:
# - ~/.ssh/id_rsa (PRIVADA - guardar con seguridad)
# - ~/.ssh/id_rsa.pub (PÚBLICA - compartir)
```

### 2. Conectar a Servidor Remoto

```bash
# Conexión SSH básica
ssh usuario@servidor.com

# Conexión con puerto personalizado
ssh -p 2222 usuario@servidor.com

# Ejecutar comando remoto
ssh usuario@servidor.com "ls -la"
```

### 3. Transferencia de Archivos con SCP

```bash
# Descargar desde servidor
scp usuario@servidor.com:/ruta/archivo.txt ~/local/

# Subir a servidor
scp ~/archivo.txt usuario@servidor.com:/ruta/destino/

# Copiar directorio
scp -r usuario@servidor.com:/ruta/directorio ~/local/
```

### 4. SFTP - Protocolo Seguro

```bash
# Abrir sesión SFTP interactiva
sftp usuario@servidor.com

# Comandos dentro de SFTP:
get archivo.txt          # Descargar
put archivo.txt          # Subir
ls                       # Listar archivos
cd directorio            # Cambiar directorio
quit                     # Salir
```

### 5. Túnel SSH (Port Forwarding)

```bash
# Túnel local: acceder a servicio remoto en localhost
ssh -L 8080:servidor.interno:3000 usuario@servidor-puerta

# Túnel remoto: exponer puerto local en servidor remoto
ssh -R 8080:localhost:3000 usuario@servidor.com
```

## Configuración de Acceso sin Contraseña

### ~/.ssh/config

```
Host servidor-prod
    HostName prod.empresa.com
    User deploy
    IdentityFile ~/.ssh/id_rsa
    Port 22
    
Host servidor-dev
    HostName dev.empresa.com
    User developer
    IdentityFile ~/.ssh/dev_rsa
    Port 2222
```

Conexión simplificada:
```bash
ssh servidor-prod
ssh servidor-dev
```

## Recursos

- [SSH Key-based Authentication](https://www.ssh.com/ssh/key/)
- [SFTP Manual](https://man.openbsd.org/sftp)
- [SSH Tunneling Guide](https://www.ssh.com/ssh/tunneling/)

## Actividades

1. Generar un par de claves SSH para un servidor de práctica
2. Configurar acceso SSH sin contraseña
3. Crear un script que se ejecute remotamente vía SSH
4. Implementar un túnel SSH para acceso seguro a base de datos

---

[← Anterior: UD1](../ud1/index.md) | [Siguiente: UD3 - Docker →](../ud3/index.md)
