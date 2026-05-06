---
layout: default
title: UD4 - Servicios en la nube
nav_order: 5
has_children: true
permalink: /ud4/
---

# UD4 - Despliegue de aplicaciones web en servicios en la nube

## Contenidos Principales

- **Proveedores principales:** AWS, Azure, GCP
- **Modelos de servicio:** IaaS, PaaS, SaaS
- **Plataformas PaaS:** Heroku, Railway, Vercel, Netlify
- **Servicios gestionados:** bases de datos, almacenamiento, CDN
- **CI/CD en la nube:** pipelines de despliegue automático
- **Monitoreo y escalado automático**
- **Costos y optimización**

## Objetivos de Aprendizaje

Al completar esta unidad, serás capaz de:

✓ Diferenciar entre IaaS, PaaS y SaaS  
✓ Desplegar aplicaciones en plataformas PaaS (Heroku, Railway, Vercel)  
✓ Usar servicios gestionados en la nube  
✓ Configurar pipelines CI/CD  
✓ Escalar aplicaciones automáticamente  
✓ Monitorear y optimizar costos  
✓ Implementar estrategias de backup y recuperación  

## Conceptos Clave

### Modelos de Servicio

```
En Premisas          IaaS              PaaS              SaaS
─────────────────────────────────────────────────────────────
Tú responsable de:
✗ Infraestructura   ✓ App+Datos       ✓ App            ✓ Nada
✓ SO                ✗ SO              ✗ Middleware     ✗ Todo
✓ Middleware        ✗ Middleware      ✗ Runtime
✓ Runtime           ✗ Runtime         ✗ BD
✓ Aplicación        ✗ BD              ✗ Redes
✓ Datos             ✗ Redes           ✗ Seguridad
✓ BD                ✗ Seguridad
✓ Redes
✓ Seguridad

Ejemplos:
AWS, GCP, Azure    Heroku,            Salesforce,
                   Railway, Vercel    Office 365
```

## Ejemplo Práctico

### 1. Despliegue en Heroku

```bash
# Instalar Heroku CLI
curl https://cli-assets.heroku.com/install.sh | sh

# Iniciar sesión
heroku login

# Crear aplicación en Heroku
heroku create nombre-app

# Definir variables de entorno
heroku config:set DATABASE_URL=postgresql://...
heroku config:set NODE_ENV=production

# Desplegar con Git
git push heroku main

# Ver logs
heroku logs --tail

# Escalar dynos
heroku ps:scale web=2
```

### 2. Heroku Procfile (especificación de procesos)

```
web: node server.js
worker: node worker.js
scheduler: node scheduler.js
```

### 3. Despliegue en Vercel (Next.js)

```bash
# Instalar CLI de Vercel
npm install -g vercel

# Desplegar proyecto
vercel

# Despliegues adicionales con variables de entorno
vercel env add SECRET_API_KEY
vercel --prod
```

### 4. GitHub Actions para CI/CD

```yaml
name: Deploy

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      
      - name: Install dependencies
        run: npm install
      
      - name: Run tests
        run: npm test
      
      - name: Deploy to Heroku
        run: |
          git config user.email "ci@example.com"
          git config user.name "CI Bot"
          git push https://heroku:${{ secrets.HEROKU_API_KEY }}@git.heroku.com/${{ secrets.HEROKU_APP_NAME }}.git main
```

### 5. AWS - Elastic Beanstalk

```bash
# Instalar EB CLI
pip install awsebcli

# Inicializar aplicación
eb init -p node.js-18 myapp

# Crear entorno
eb create myapp-env

# Desplegar cambios
eb deploy

# Ver estado
eb status

# Escalar
eb scale 3

# Ver logs
eb logs
```

## Comparativa de PaaS Populares

| Plataforma | Precio | Lenguajes | Mejores para | Ventajas |
|-----------|--------|-----------|-------------|----------|
| **Heroku** | $12-500/mes | Multi | Prototipos, Startups | Simple, intuitivo |
| **Railway** | Pay-as-you-go | Multi | Full-stack | Económico, moderno |
| **Vercel** | Freemium | JS/Node | Frontend, Next.js | Optimizado para frontend |
| **AWS** | Variable | Todos | Enterprise | Escalable, completo |
| **Azure** | Variable | Multi | Empresa | Integración Microsoft |

## Recursos

- [AWS Documentation](https://docs.aws.amazon.com/)
- [Heroku Deployment](https://devcenter.heroku.com/)
- [Vercel Deployment Guide](https://vercel.com/docs)
- [Railway Documentation](https://railway.app/docs)
- [CI/CD Best Practices](https://en.wikipedia.org/wiki/Continuous_integration)

## Actividades

1. Desplegar una aplicación en Heroku (prueba gratuita)
2. Configurar CI/CD con GitHub Actions hacia Railway
3. Implementar escalado automático en AWS
4. Monitorear costos y optimizar infraestructura
5. Crear un plan de recuperación ante desastres

---

[← Anterior: UD3](../ud3/index.md) | [Siguiente: UD5 - Servidores Web →](../ud5/index.md)
