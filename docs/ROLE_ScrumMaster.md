# ROLE_ScrumMaster.md

## Scrum Master — Infraestructura, CI/CD, Seguridad

**Responsabilidades:**
- Mantener `docker-compose.yml` y `Dockerfile`
- Configurar GitHub Actions (Trufflehog, Semgrep)
- Gestionar secretos y variables de entorno
- Documentación técnica (README, BUILD-LOG)
- Resolver conflictos de Git entre desarrolladores

**Archivos bajo su control:**
- `docker-compose.yml`
- `Dockerfile`
- `.env.example`
- `.github/workflows/`
- `.gitignore`
- `README.md`
- `BUILD-LOG.md`

**Regla:** Ningún PR se merge si Trufflehog detecta secretos.