---
layout: default
title: UD3 - Docker
nav_order: 4
has_children: true
permalink: /ud3/
---

# UD3 - Despliegue de aplicaciones web en contenedores Docker

## Contenidos Principales

- **Contenedores vs máquinas virtuales**
- **Imágenes Docker:** construcción y distribución
- **Contenedores:** ciclo de vida, redes, volúmenes
- **Dockerfile:** instrucciones y mejores prácticas
- **Docker Compose:** orquestación de múltiples servicios
- **Registros de imágenes:** Docker Hub, registros privados
- **Redes y persistencia de datos**

## Objetivos de Aprendizaje

Al completar esta unidad, serás capaz de:

✓ Entender la diferencia entre contenedores e imágenes  
✓ Crear Dockerfile para aplicaciones web  
✓ Ejecutar, pausar y detener contenedores Docker  
✓ Usar Docker Compose para aplicaciones multi-servicio  
✓ Persistir datos con volúmenes Docker  
✓ Networking entre contenedores  
✓ Publicar imágenes en registros  

## Conceptos Clave

### Contenedores vs Máquinas Virtuales

```
Máquina Virtual:          Contenedor:
┌──────────────┐        ┌─────────────┐
│  App + Deps  │        │   App       │
├──────────────┤        ├─────────────┤
│   SO Guest   │        │  Runtime    │
├──────────────┤        ├─────────────┤
│  Hypervisor  │        │  Docker     │
└──────────────┘        └─────────────┘
   700MB-2GB               10-100MB
   Lento                   Rápido
```

### Imagen vs Contenedor

- **Imagen:** plantilla inmutable (como un .iso)
- **Contenedor:** instancia ejecutable de una imagen (como una máquina en uso)

## Ejemplo Práctico

### 1. Dockerfile Básico

```dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
```

### 2. Construir y Ejecutar Imagen

```bash
# Construir imagen
docker build -t miapp:1.0 .

# Ver imágenes disponibles
docker images

# Ejecutar contenedor
docker run -p 3000:3000 --name miapp-container miapp:1.0

# Ver contenedores en ejecución
docker ps

# Detener contenedor
docker stop miapp-container

# Eliminar contenedor
docker rm miapp-container
```

### 3. Docker Compose - Multi-Servicio

```yaml
version: '3.8'

services:
  web:
    image: nginx:latest
    ports:
      - "80:80"
    volumes:
      - ./html:/usr/share/nginx/html
    depends_on:
      - app

  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      DATABASE_URL: postgresql://postgres:password@db:5432/myapp
    volumes:
      - ./code:/app
    depends_on:
      - db

  db:
    image: postgres:15
    ports:
      - "5432:5432"
    environment:
      POSTGRES_PASSWORD: password
      POSTGRES_DB: myapp
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

### 4. Ejecutar Docker Compose

```bash
# Iniciar servicios
docker-compose up -d

# Ver logs
docker-compose logs -f

# Detener servicios
docker-compose down

# Reconstruir imágenes
docker-compose up --build
```

### 5. Persistencia de Datos con Volúmenes

```bash
# Crear volumen nombrado
docker volume create datos-app

# Usar volumen en contenedor
docker run -v datos-app:/data miapp:1.0

# Volumen de bind mount (directorio local)
docker run -v /ruta/local:/ruta/contenedor miapp:1.0
```

## Mejores Prácticas

1. **Minimizar capas:** Combinar comandos RUN
2. **Multi-stage builds:** Optimizar tamaño final
3. **Usar .dockerignore:** Excluir archivos innecesarios
4. **Tags significativos:** `app:1.0`, `app:latest`
5. **Healthchecks:** Monitoreo de contenedores

## Recursos

- [Docker Documentation](https://docs.docker.com/)
- [Docker Compose Manual](https://docs.docker.com/compose/)
- [Best Practices for Writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

## Actividades

1. Crear Dockerfile para una aplicación web existente
2. Configurar docker-compose.yml con múltiples servicios
3. Implementar volúmenes para persistencia de base de datos
4. Publicar imagen en Docker Hub

---

[← Anterior: UD2](../ud2/index.md) | [Siguiente: UD4 - Cloud →](../ud4/index.md)
