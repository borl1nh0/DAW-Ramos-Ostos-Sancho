---
layout: default
title: UD9 - Control de versiones
nav_order: 10
has_children: true
permalink: /ud9/
---

# UD9 - Control de versiones

## Contenidos Principales

- **Git avanzado:** comandos, workflows, best practices
- **Ramas (Branching):** estrategias, creación, merge
- **Merge vs Rebase:** diferencias, cuándo usar cada uno
- **GitHub Flow:** modelo de desarrollo colaborativo
- **GitLab CI/CD:** pipelines de automatización
- **Git Hooks:** pre-commit, pre-push, automatización
- **Resolución de conflictos:** estrategias, tools
- **Tags y releases:** versionado

## Objetivos de Aprendizaje

Al completar esta unidad, serás capaz de:

✓ Dominar Git en contextos collaborativos  
✓ Implementar GitHub Flow en equipos  
✓ Gestionar ramas eficientemente  
✓ Resolver conflictos correctamente  
✓ Crear pipelines CI/CD  
✓ Usar Git Hooks para automatización  
✓ Implementar estrategias de versionado  
✓ Auditar y revertir cambios  

## Conceptos Clave

### Git Workflow - Staging

```
Working Directory  →  Staging Area  →  Repository
   (archivos)       git add          git commit
```

### Estrategia GitHub Flow

```
          ┌─────────────────────────────┐
          │      main (producción)      │
          └──────────────┬──────────────┘
                         │
                    ┌────▼────┐
                    │  release │
                    │  testing │
                    └────┬────┘
                         │
     ┌───────────────────┼───────────────────┐
     │                   │                   │
   feature/1          feature/2           hotfix/1
   (develop)          (develop)           (main)
```

## Ejemplo Práctico

### 1. Configuración Inicial Git

```bash
# Configurar usuario global
git config --global user.name "Tu Nombre"
git config --global user.email "tu.email@ejemplo.com"

# Configurar editor
git config --global core.editor "nano"

# Ver configuración
git config --global --list
```

### 2. Rama Básicas y Merge

```bash
# Crear y cambiar a rama
git checkout -b feature/nueva-funcionalidad
# O en Git moderno:
git switch -c feature/nueva-funcionalidad

# Realizar cambios
echo "nuevo contenido" > archivo.txt
git add .
git commit -m "feat: nueva funcionalidad"

# Cambiar a main
git switch main
git pull origin main

# Merge rama
git merge feature/nueva-funcionalidad

# Eliminar rama
git branch -d feature/nueva-funcionalidad
git push origin --delete feature/nueva-funcionalidad
```

### 3. Merge vs Rebase

```bash
# MERGE - Mantiene historial completo
git checkout feature
git merge main

# Resultado: Commit de merge que une ambas historias
# ✓ Historial completo preservado
# ✓ Un poco más "sucio" en el historia
#  X Más difícil de seguir en ramas largas

# REBASE - Reescribe historial
git checkout feature
git rebase main

# Resultado: Commits de feature se replayan sobre main
# ✓ Historial lineal y limpio
# ✓ Fácil de entender
# ✗ Reescribe historial (no usar en ramas compartidas)
```

### 4. Resolución de Conflictos

```bash
# Durante un merge que tiene conflictos
# Archivo mostrará:

<<<<<<< HEAD (current branch)
contenido en rama actual
=======
contenido en rama fusionada
>>>>>>> nombre-rama

# Resolver: editar archivo y quedarse con lo deseado
# Luego:
git add archivo-resuelto.txt
git commit -m "Conflicto resuelto"

# Para ver conflictos
git diff --name-only --diff-filter=U
```

### 5. Git Hooks - Automatización

```bash
# Crear hook pre-commit
nano .git/hooks/pre-commit
```

```bash
#!/bin/bash
# Pre-commit hook: validar linting antes de commit

echo "Ejecutando linting..."
npm run lint

if [ $? -ne 0 ]; then
    echo "❌ Linting falló. Commit abortado."
    exit 1
fi

echo "✅ Linting pasado"
exit 0
```

```bash
# Hacer ejecutable
chmod +x .git/hooks/pre-commit

# Prueba
git commit -m "test"  # Ejecutará el hook
```

### 6. GitHub Flow Completo

```bash
# Paso 1: Crear rama desde main
git checkout main
git pull origin main
git checkout -b feature/login-mejorado

# Paso 2: Desarrollar
echo "// Código mejorado" >> auth.js
git add auth.js
git commit -m "feat: mejorar lógica de login"

# Paso 3: Subir rama
git push -u origin feature/login-mejorado

# Paso 4: Crear Pull Request en GitHub
# (Ir a GitHub, abrir PR, añadir descripción)

# Paso 5: Si hay review, hacer cambios
git add archivo-corregido.js
git commit -m "fix: revisar comentarios"
git push

# Paso 6: Merge en GitHub (o local)
git checkout main
git pull origin main
git merge --no-ff feature/login-mejorado
git push origin main

# Paso 7: Limpiar rama
git branch -d feature/login-mejorado
git push origin --delete feature/login-mejorado
```

### 7. Tags y Releases

```bash
# Crear tag anotado
git tag -a v1.0.0 -m "Release versión 1.0.0"

# Crear tag ligero
git tag v1.0.0

# Listar tags
git tag -l

# Ver información del tag
git show v1.0.0

# Subir tags a remoto
git push origin v1.0.0
git push origin --tags

# Deletar tag
git tag -d v1.0.0
git push origin --delete v1.0.0
```

### 8. Revertir Cambios

```bash
# El mejor amigo: git reflog
git reflog

# Revert: crear commit que deshace cambios
git revert abc123
# Crea nuevo commit que revierte abc123

# Reset: cambiar puntero (⚠️ cuidado)
git reset --soft HEAD~1   # Deshacer último commit, mantener cambios
git reset --mixed HEAD~1  # Deshacer último commit, mantener archivos modificados
git reset --hard HEAD~1   # Deshacer último commit, perder todo

# Recuperar commit "perdido"
git reflog
git checkout abc123  # Volver al commit

# Cherry-pick: aplicar un commit en otra rama
git cherry-pick abc123
```

### 9. CI/CD con GitHub Actions

```yaml
name: Tests and Deploy

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [16.x, 18.x]
    
    steps:
      - uses: actions/checkout@v2
      
      - name: Setup Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v2
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Lint
        run: npm run lint
      
      - name: Tests
        run: npm test
      
      - name: Build
        run: npm run build

  deploy:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v2
      - name: Deploy to production
        run: |
          curl -X POST https://deploy.ejemplo.com/webhook \
               -H "Authorization: Bearer ${{ secrets.DEPLOY_TOKEN }}" \
               -d "branch=main"
```

### 10. Convención de Commits

```bash
# Tipo: feat, fix, docs, style, refactor, test, chore, perf

# Ejemplos:
git commit -m "feat: añadir autenticación OAuth"
git commit -m "fix: corregir validación de email"
git commit -m "docs: actualizar README"
git commit -m "refactor: simplificar lógica"
git commit -m "test: añadir tests para login"
git commit -m "chore: actualizar dependencias"
```

## Comandos Útiles

```bash
# Ver diferencias
git diff                    # Entre WD y staging
git diff --staged           # Entre staging y repository
git diff rama1 rama2        # Entre ramas

# Log avanzado
git log --oneline --graph --all --decorate
git log --author="nombre" --since="2023-01-01"
git log -S "texto buscar"   # Búsqueda en código

# Bisect: encontrar commit que introdujo bug
git bisect start
git bisect bad              # Marca HEAD como mal
git bisect good v1.0        # Marca v1.0 como bien
# Git busca automáticamente
```

## Recursos

- [Pro Git Book](https://git-scm.com/book/en/v2)
- [GitHub Flow Guide](https://guides.github.com/introduction/flow/)
- [Conventional Commits](https://www.conventionalcommits.org/)
- [Git Hooks](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)
- [GitHub Actions](https://github.com/features/actions)

## Actividades

1. Implementar GitHub Flow en equipo
2. Crear y resolver conflictos en merge/rebase
3. Configurar Git hooks para validación automática
4. Crear pipeline CI/CD con GitHub Actions
5. Implementar convención de commits
6. Auditar historial con git reflog y git bisect

---

[← Anterior: UD8](../ud8/index.md)
