---
name: git-workflow
description: Experto en flujos de trabajo Git profesionales. Conventional commits, branching strategies, hooks y resolución de conflictos.
---

# 🌿 Git Workflow Expert

Skill para implementar flujos de trabajo Git profesionales.

## Cuándo Usar Esta Skill

- Configurar estrategias de branching
- Implementar conventional commits
- Configurar pre-commit hooks
- Resolver conflictos complejos
- Mantener historial limpio

---

## 📋 Conventional Commits

Formato: `<tipo>(<ámbito>): <descripción>`

### Tipos Permitidos

| Tipo       | Descripción                 | Ejemplo                                |
| ---------- | --------------------------- | -------------------------------------- |
| `feat`     | Nueva funcionalidad         | `feat(auth): add OAuth2 login`         |
| `fix`      | Corrección de bug           | `fix(api): handle null response`       |
| `docs`     | Documentación               | `docs(readme): update install steps`   |
| `style`    | Formato (sin cambio lógico) | `style: apply black formatter`         |
| `refactor` | Refactorización             | `refactor(utils): simplify validation` |
| `perf`     | Mejora de rendimiento       | `perf(db): add query index`            |
| `test`     | Añadir/modificar tests      | `test(auth): add login unit tests`     |
| `build`    | Build system, deps          | `build: upgrade pytest to 8.0`         |
| `ci`       | CI/CD configuration         | `ci: add GitHub Actions workflow`      |
| `chore`    | Tareas de mantenimiento     | `chore: update .gitignore`             |
| `revert`   | Revertir commit             | `revert: feat(auth): add OAuth2`       |

### Breaking Changes

```bash
feat(api)!: remove deprecated endpoints

BREAKING CHANGE: Los endpoints /v1/* han sido eliminados.
Migrar a /v2/* antes de actualizar.
```

---

## 🌳 Estrategias de Branching

### Git Flow (proyectos con releases)

```
main ─────●─────────────────●─────────── (producción)
           \               /
develop ────●───●───●───●───●──────────── (integración)
             \     / \     /
feature/x ────●───●   \   /
                       \ /
feature/y ──────────────●
```

```bash
# Crear feature branch
git checkout develop
git checkout -b feature/nueva-funcionalidad

# Finalizar feature
git checkout develop
git merge --no-ff feature/nueva-funcionalidad
git branch -d feature/nueva-funcionalidad
```

### GitHub Flow (deploy continuo)

```
main ─────●───●───●───●───●──────────── (siempre deployable)
           \   /     \   /
feature ────●─┘       ●─┘
```

```bash
# Crear branch desde main
git checkout main
git checkout -b feature/mi-cambio

# PR y merge via GitHub
```

### Trunk-Based (equipos maduros)

```
main ─────●───●───●───●───●──────────── (commits frecuentes)
           │       │
           └───────┴── feature flags
```

---

## 🪝 Pre-commit Hooks

### Configuración .pre-commit-config.yaml

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.5.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-json
      - id: check-added-large-files
        args: ["--maxkb=1000"]
      - id: detect-private-key
      - id: check-merge-conflict

  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.3.0
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format

  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.8.0
    hooks:
      - id: mypy
        additional_dependencies: [types-requests]

  - repo: https://github.com/commitizen-tools/commitizen
    rev: v3.14.0
    hooks:
      - id: commitizen
        stages: [commit-msg]
```

### Instalación

```bash
pip install pre-commit
pre-commit install
pre-commit install --hook-type commit-msg  # Para validar commits
pre-commit run --all-files  # Ejecutar en todos los archivos
```

---

## 📝 Procedimientos

### 1. Configuración Inicial

```bash
# Configurar identidad
git config --global user.name "Tu Nombre"
git config --global user.email "tu@email.com"

# Configurar editor
git config --global core.editor "code --wait"

# Configurar merge tool
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'

# Alias útiles
git config --global alias.lg "log --oneline --graph --decorate -20"
git config --global alias.st "status -sb"
git config --global alias.co "checkout"
git config --global alias.br "branch"
git config --global alias.unstage "reset HEAD --"
git config --global alias.last "log -1 HEAD --stat"
```

### 2. Flujo de Trabajo Diario

```bash
# 1. Actualizar main
git checkout main
git pull origin main

# 2. Crear feature branch
git checkout -b feature/mi-cambio

# 3. Hacer commits atómicos
git add archivo.py
git commit -m "feat(modulo): add new validation"

# 4. Mantener actualizado con main
git fetch origin
git rebase origin/main

# 5. Push y crear PR
git push -u origin feature/mi-cambio
```

### 3. Resolución de Conflictos

```bash
# Durante merge/rebase
git status  # Ver archivos en conflicto

# Editar archivos manualmente o usar mergetool
git mergetool

# Marcar como resuelto
git add archivo_resuelto.py

# Continuar rebase
git rebase --continue

# O abortar si es necesario
git rebase --abort
```

### 4. Limpiar Historial

```bash
# Rebase interactivo (últimos 5 commits)
git rebase -i HEAD~5

# En el editor:
# pick abc1234 feat: first commit
# squash def5678 fix: typo          <- combinar con anterior
# reword ghi9012 feat: other thing  <- editar mensaje
# drop jkl3456 WIP                  <- eliminar

# Amend último commit
git commit --amend -m "nuevo mensaje"

# Amend sin cambiar mensaje
git commit --amend --no-edit
```

### 5. Deshacer Cambios

```bash
# Deshacer cambios en working directory
git checkout -- archivo.py

# Unstage archivo
git reset HEAD archivo.py

# Deshacer último commit (mantener cambios)
git reset --soft HEAD~1

# Deshacer último commit (descartar cambios)
git reset --hard HEAD~1

# Revertir commit específico (crea nuevo commit)
git revert abc1234
```

### 6. Stash para Cambios Temporales

```bash
# Guardar cambios
git stash push -m "trabajo en progreso"

# Listar stashes
git stash list

# Aplicar último stash
git stash pop

# Aplicar stash específico
git stash apply stash@{2}

# Eliminar stash
git stash drop stash@{0}
```

---

## 🏷️ Versionado Semántico

```bash
# Formato: MAJOR.MINOR.PATCH
# 1.0.0 -> 1.0.1 (patch: bug fix)
# 1.0.0 -> 1.1.0 (minor: new feature, backward compatible)
# 1.0.0 -> 2.0.0 (major: breaking change)

# Crear tag
git tag -a v1.2.0 -m "Release 1.2.0: nueva funcionalidad X"
git push origin v1.2.0

# Listar tags
git tag -l "v1.*"
```

---

## ✅ Checklist Git

- [ ] ¿Pre-commit hooks configurados?
- [ ] ¿Conventional commits en uso?
- [ ] ¿Branching strategy definida?
- [ ] ¿.gitignore actualizado?
- [ ] ¿Commits atómicos y descriptivos?
- [ ] ¿Historial limpio (sin WIP, fixup)?
- [ ] ¿Tags para releases?
