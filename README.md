# GitHub Actions self-hosted runners con Docker Compose

Levanta N runners self-hosted de GitHub Actions para **cualquier repositorio**,
todos a partir de un unico servicio escalable. El repo destino se configura con
la variable `REPO_URL` en `.env`.

## 1) Preparar variables

1. Copia `.env.example` a `.env`.
2. En `.env`, fija:
   - `REPO_URL` con tu repositorio, ej. `https://github.com/TU_USUARIO/TU_REPO`.
   - `ACCESS_TOKEN` con un token valido (ver [Notas importantes](#notas-importantes)).

## 2) Levantar N runners

El servicio se llama `github-runner`. El numero tras `--scale github-runner=` es
la cantidad de runners que quieres levantar.

```bash
# Levantar 3 runners
docker compose --env-file .env up -d --scale github-runner=3

# Cambiar a 5 runners (re-ejecuta con el numero deseado)
docker compose --env-file .env up -d --scale github-runner=5

# Ver estado
docker compose ps

# Ver logs
docker compose logs -f github-runner
```

## 3) Parar y eliminar

```bash
docker compose down
```

## 4) Usar en workflows

En tus workflows de GitHub Actions:

```yaml
runs-on: [self-hosted, linux, x64]
```

Si quieres apuntar solo a estos runners con Docker, usa tambien la etiqueta `docker`
(definida en `LABELS` dentro de `.env`).

```yaml
runs-on: [self-hosted, linux, x64, docker]
```

## Notas importantes

- El token mostrado al crear runner en GitHub (pantalla New runner) es temporal.
- Para estabilidad en contenedores, usa un PAT con permisos de runners en el repositorio.
- Si habilitas `EPHEMERAL=true`, cada contenedor se registra para 1 job y luego se desregistra automaticamente.
