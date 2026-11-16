# Atrios MVP — Bot WhatsApp → Gemini → Zoho CRM

Bot conversacional para Atrios (seguridad electrónica) que automatiza la captación de prospectos vía WhatsApp.

## Arquitectura

```
Cliente → WhatsApp Cloud API → n8n Webhook → 200 OK → Redis (estado) → Gemini (IA) → Zoho CRM (Lead) → WhatsApp (respuesta)
```

## Requisitos

- Docker + Docker Compose
- ngrok (para desarrollo local)
- Cuenta Meta Developer (WhatsApp Sandbox)
- Google AI Studio (Gemini API Key)
- Zoho CRM Free Edition (OAuth 2.0)

## Setup rápido (Entorno Local)

```bash
# 1. Clonar repo
git clone <repo-url> && cd atrios-mvp-bot

# 2. Configurar variables de entorno
cd infra
cp .env.example .env
# Editar .env con los valores reales

# 3. Levantar infraestructura
docker compose up -d

# 4. Acceder a n8n
# http://localhost:5678

# 5. Importar workflow
# Importar workflows/00_master_workflow.json en n8n

# 6. Configurar webhook en Meta Developer
# URL: https://<tu-ngrok-url>/webhook/whatsapp-webhook
```

## Estructura del Proyecto

```
atrios-mvp-bot/
├── .github/workflows/     # CI/CD (Trufflehog)
├── docs/                  # Documentación (PLAN, ROADMAP, roles, demo)
├── infra/                 # Docker, variables de entorno
├── tests/                 # Payloads de prueba
├── workflows/             # Flujos de n8n
│   └── subflows/          # Sub-flujos individuales
├── .gitignore
└── README.md
```

## Variables de entorno (infra/.env)

| Variable | Descripción |
|----------|-------------|
| `N8N_ENCRYPTION_KEY` | Clave de encriptación de n8n (32 chars, generar con `openssl rand -hex 32`) |
| `REDIS_PASSWORD` | Password de Redis |

## Workflow de Git

- `main`: Producción/estable (protegida)
- `develop`: Integración
- `feature/nombre-tarea`: Ramas individuales

Convención de commits: `feat:`, `fix:`, `docs:`, `chore:`, `test:`

**Regla crítica:** NUNCA hacer push directo a `main` o `develop`. Todo va vía Pull Request.

## Seguridad

- Trufflehog en GitHub Actions (bloquea PRs con secretos verificados)
- Redis con password obligatoria
- No se valida HMAC de Meta (riesgo aceptado para MVP)

## Equipo

| Rol | Persona | Responsabilidad |
|-----|---------|-----------------|
| Scrum Master | (Tú) | Infra, CI/CD, Seguridad, Docs |
| Product Owner | David | Backlog, Criterios, Zoho |
| Dev Team | Alonzo, Benjamin, Antony | Sub-flujos n8n |
| QA/UAT | Jostyn | Pruebas, Demo, Bugs |

## Stack

- **n8n** — Orquestación de workflows
- **Redis 7** — Estado de conversaciones
- **Gemini Pro** — Procesamiento de lenguaje natural
- **WhatsApp Cloud API v18** — Canal de mensajería
- **Zoho CRM** — Gestión de prospectos