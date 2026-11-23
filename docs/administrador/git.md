# Control de versiones con Git y GitHub

El proyecto está en un repositorio de GitHub. El servidor `B-NNC` se autentica con un par de claves pública/privada, sin contraseñas.

## Autenticación por clave SSH

En el servidor, con el usuario `bchecker`:

```bash
ssh-keygen -t ed25519 -C "b-nnc" -N "" -f ~/.ssh/id_ed25519
cat ~/.ssh/id_ed25519.pub
```

La clave pública (la línea entera que empieza por `ssh-ed25519`) se añade en GitHub, no en la terminal: repositorio, Settings, Deploy keys, Add deploy key, marcando "Allow write access". La clave privada no sale del servidor.

Comprobación:

```bash
ssh-keyscan -t ed25519 github.com >> ~/.ssh/known_hosts
ssh -T git@github.com
```

Tiene que responder con "successfully authenticated".

## Configuración inicial de Git

```bash
git config --global user.name "bchecker"
git config --global user.email "bchecker@sitioitb.cat"
git config --global init.defaultBranch main
```

## Comandos principales

| Comando | Para qué sirve |
| :--- | :--- |
| `git clone git@github.com:ITB2526-JosueAmador/Projecte-Zero.git ~/projecte-zero` | Descarga el repositorio la primera vez, en la carpeta `~/projecte-zero` |
| `git status` | Ver qué ha cambiado |
| `git add .` | Preparar los cambios para el commit |
| `git commit -m "mensaje"` | Guardar los cambios en local con un mensaje |
| `git push origin main` | Subir los commits a GitHub |
| `git pull origin main` | Bajar los cambios nuevos de GitHub |
| `git log --oneline` | Ver el historial |

## Flujo de subida a producción

1. Se editan los ficheros (configuración, documentación, scripts).
2. `git add .` y `git commit -m "descripción del cambio"`.
3. `git push origin main` sube los cambios a GitHub.
4. En el servidor de producción se hace `git pull origin main` para actualizar.

Si el push se rechaza porque hay cambios nuevos en GitHub, primero `git pull --rebase origin main` y luego otra vez `git push`.

Comprobación:

```bash
ssh -T git@github.com
git -C ~/projecte-zero status
git -C ~/projecte-zero push
```

---

[Volver al índice](../../README.md)
