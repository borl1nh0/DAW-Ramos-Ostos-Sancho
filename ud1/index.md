---
layout: default
title: UD1 - Introducción al despliegue
nav_order: 2
has_children: true
permalink: /ud1/
---

# UD1 - Introducción al despliegue de aplicaciones web

## Contenidos Principales

- **¿Qué es el despliegue?** Definición, concepto y relevancia
- **Ciclo de vida del software** (SDLC): desarrollo, testing, despliegue, mantenimiento
- **Entornos de ejecución:**
  - Entorno de desarrollo (DEV)
  - Entorno de staging/pre-producción (STAGING)
  - Entorno de producción (PROD)
- **Arquitecturas web:** aplicaciones monolíticas vs microservicios
- **Infraestructura y escalabilidad**

## Objetivos de Aprendizaje

Al completar esta unidad, serás capaz de:

✓ Entender qué es el despliegue de aplicaciones web  
✓ Identificar las diferencias entre entornos (DEV, STAGING, PROD)  
✓ Conocer el ciclo de vida completo de una aplicación web  
✓ Reconocer las distintas arquitecturas de despliegue  
✓ Aplicar mejores prácticas en la gestión de entornos  

## Conceptos Clave

### ¿Qué es el Despliegue?

El despliegue es el proceso de **llevar una aplicación desde el entorno de desarrollo hasta producción**, haciéndola accesible para los usuarios finales.

### Ciclo de Vida del Software (SDLC)

```
Diseño → Desarrollo → Testing → Despliegue → Mantenimiento
   ↑                                              ↓
   └──────────────── Actualización ───────────────┘
```

### Los Tres Entornos

| Entorno | Propósito | Usuarios | Datos |
|---------|----------|----------|-------|
| **DEV** | Desarrollar y probar cambios | Desarrolladores | No reales |
| **STAGING** | Simular producción para testing | Testers/QA | No reales |
| **PROD** | Servir aplicación a usuarios | Usuarios finales | Datos reales |

## Ejemplo Práctico

```bash
# Workflow típico de despliegue

# 1. Cambios en DEV
git commit -m "Nueva feature"

# 2. Push a STAGING para testing
git push origin staging

# 3. Tests y validación en STAGING

# 4. Merge a PROD cuando esté listo
git push origin main

# 5. Despliegue en PROD
./deploy-production.sh
```

## Recursos

- [SDLC - Modelo de ciclo de vida](https://en.wikipedia.org/wiki/Systems_development_life_cycle)
- [Infrastructure as Code](https://en.wikipedia.org/wiki/Infrastructure_as_code)
- [DevOps Handbook](https://itrevolution.com/the-devops-handbook/)

## Actividades

1. Investigar y documentar el ciclo de vida actual de tu empresa/proyecto
2. Diseñar una estrategia de entornos para una aplicación web
3. Mapeo de cambios: de DEV → STAGING → PROD

---

[Siguiente: UD2 - Conexiones remotas →](../ud2/index.md)
