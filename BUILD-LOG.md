# BUILD-LOG.md

**Proyecto:** Atrios MVP — Bot WhatsApp → Gemini → Zoho CRM
**Plan:** plan.md v1.3
**Inicio:** 2026-11-16

---

## Re-estructuración: Estructura de Carpetas SM

**Status:** DONE
**Completed:** 2026-11-16T01:06Z

### Scope
Reorganización completa del repo según la estructura solicitada por el Scrum Master para que el equipo pueda trabajar.

### Estructura final

```
atrios-mvp-bot/
├── .github/workflows/
│   └── security.yml              # Trufflehog (solo --only-verified)
├── docs/
│   ├── PLAN.md                   # Plan maestro
│   ├── CONTRATO_DATOS.md         # JSON entre sub-flujos
│   ├── DEMO_SCRIPT.md            # Guion de demo (QA)
│   ├── ROLE_ScrumMaster.md       # Rol SM
│   ├── ROLE_ProductOwner.md      # Rol PO (David)
│   ├── ROLE_DevelopmentTeam.md   # Rol Dev (Alonzo, Benjamin, Antony)
│   ├── ROLE_QA.md                # Rol QA (Jostyn)
│   └── TASKS_BY_ROLE.md          # Tareas por rol
├── infra/
│   ├── docker-compose.yml        # n8n + Redis (imagen oficial, no build)
│   └── .env.example              # Variables (sin valores reales)
├── tests/
│   ├── payload_whatsapp.json     # Payload para simular mensaje Meta
│   └── test_cases.md             # Matriz de pruebas QA
├── workflows/
│   ├── 00_master_workflow.json   # Flujo unificado (importar en n8n)
│   └── subflows/                 # (vacío, para Devs)
├── .gitignore
├── BUILD-LOG.md
└── README.md
```

### Cambios clave
- `docker-compose.yml` movido a `infra/` — usa imagen oficial `n8nio/n8n:latest` (no build custom)
- `.env.example` movido a `infra/` — simplificado a solo variables de infra
- `security.yml` simplificado — solo Trufflehog con `--only-verified` (sin Semgrep)
- Workflow renombrado a `00_master_workflow.json`
- Creados: `docs/CONTRATO_DATOS.md`, `docs/TASKS_BY_ROLE.md`, `tests/payload_whatsapp.json`, `tests/test_cases.md`
- Eliminado: `Dockerfile` (ya no se necesita build custom)

### Verificación
- Estructura confirmada con `find`
- `.gitignore` actualizado
- README actualizado con nueva ruta de workflows

---

## Slice 1: Project Scaffolding

**Status:** DONE
**Completed:** 2026-11-16T00:56Z

### Files created
- `docker-compose.yml` — n8n + Redis con password y volumen persistente
- `Dockerfile` — imagen custom de n8n con curl/jq
- `.env.example` — template completo (n8n, Redis, WhatsApp, Gemini, Zoho, Telegram)
- `.gitignore` — exclusiones para secrets, n8n data, node_modules, etc.

### Verification
- `docker compose config` valida correctamente (warnings esperados sin .env real)

---

## Slice 2: GitHub Actions — Trufflehog + Semgrep

**Status:** DONE
**Completed:** 2026-11-16T00:57Z

### Files created
- `.github/workflows/security.yml` — Trufflehog (secretos verificados, fail on detected) + Semgrep (p/security-audit) en push/PR a main y develop

---

## Slice 3: n8n Workflow JSON

**Status:** DONE
**Completed:** 2026-11-16T00:58Z

### Files created
- `n8n/workflows/whatsapp-bot.json` — Workflow completo:
  1. Webhook recibe mensaje de WhatsApp
  2. Responde 200 OK inmediato a Meta
  3. Extrae phone, message_text, message_id
  4. Consulta estado en Redis
  5. Si no hay estado, inicializa retry_count=0, history=[]
  6. Llama Gemini con prompt de sistema (extracción de datos, intención, confianza)
  7. Parsea respuesta JSON de Gemini
  8. Si confianza >= 0.7 → envía respuesta por WhatsApp, guarda estado, crea Lead en Zoho
  9. Si confianza < 0.7 → incrementa retry, guarda estado
  10. Si retry >= 3 → deriva a humano con mensaje fallback

### Flow (resumen)
```
Webhook → 200 OK → Extract Data → Redis GET → State exists?
  → NO → Init State → Build Context → Gemini API (3 retries)
  → Parse Response → Confidence >= 0.7?
    → YES → WhatsApp Reply → Save State → Zoho Lead
    → NO → Max Retries?
      → YES → Fallback Reply
      → NO → Increment Retry → Save Retry State
```

---

## Slice 4: Documentation (COMPLETADO)

**Status:** DONE
**Completed:** 2026-11-16T00:59Z
**Revisado:** 2026-11-16T01:08Z — Role files completados en raíz

### Files created
- `README.md` — Quick start, arquitectura, variables de entorno, git workflow
- `ROLE_ScrumMaster.md` — Responsabilidades del Scrum Master
- `ROLE_ProductOwner.md` — Responsabilidades del Product Owner (David)
- `ROLE_Developers.md` — Responsabilidades del Dev Team (Alonzo, Benjamin, Antony)
- `ROLE_QA.md` — Responsabilidades de QA/UAT (Jostyn)
- `DEMO_SCRIPT.md` — Guion de demo con 5+ intenciones

---

## Slice 5: Course Correction (Scrum Master Feedback)

**Status:** DONE
**Date:** 2026-11-16T01:08Z

### Cambios realizados según feedback del SM

1. **n8n Workflow — Corregido exceso de alcance:**
   - Movido `workflows/00_master_workflow.json` → `docs/referencia/ejemplo_flujo_completo_para_devs.json`
   - Este archivo es ahora solo **referencia** para los Devs. No es el workflow de producción.
   - Los Devs deben crear sus propios sub-flujos en `workflows/subflows/`:
     - `01_whatsapp_in.json` (Alonzo)
     - `02_ia_processing.json` (Benjamin)
     - `03_zoho_out.json` (Antony)

2. **CI/CD Security — Simplificado:**
   - `.github/workflows/security.yml` ya estaba limpio (solo Trufflehog) ✓
   - `.pre-commit-config.yaml` — Removido hook de Semgrep. Ahora solo Trufflehog.

3. **Role Files — Completados en raíz:**
   - `ROLE_ProductOwner.md` — Creado (Zoho, GitHub issues, criterios)
   - `ROLE_Developers.md` — Creado (sub-flujos, referencia, regla anti-conflicto)
   - `ROLE_QA.md` — Creado (demo, UAT, bug reporting)

---

## Resumen de Slices Completados

| Slice | Descripción | Status |
|-------|-------------|--------|
| 1 | Project scaffolding (Docker, env, gitignore) | DONE |
| 2 | GitHub Actions (Trufflehog + Semgrep) | DONE |
| 3 | n8n Workflow (WhatsApp → Gemini → Zoho) | DONE |
| 4 | Documentation (README, ROLEs, demo) | DONE |

## Slice 7 — Despliegue en Producción (Railway) — 2026-11-16
Scope: Configurar el entorno de producción en la nube para el MVP.
Changes: 
- Creado `infra/Dockerfile` optimizado para Railway (usando imagen oficial de n8n).
- Se solucionó el error de permisos (`EACCES`) configurando `USER root` y `ENV N8N_USER_FOLDER=/home/node`.
- Se solucionó el error de comando no encontrado configurando `CMD ["n8n"]`.
- Creado servicio de Redis en Railway.
- Montado Volumen Persistente exactamente en `/home/node/.n8n`.
- Configuradas variables de entorno en Railway: `N8N_ENCRYPTION_KEY`, `N8N_PORT=5678`, `N8N_PROTOCOL=https`, `RAILWAY_RUN_UID=0`.
Commands run: git push origin main (Railway auto-deploy).
Result: Despliegue exitoso (Status: Green). n8n accesible vía dominio público de Railway.
Status: DONE
Notes: La URL de producción es https://atrios-mvp-bot-production.up.railway.app. El compañero Alonzo (Dev de entrada) se encargará de crear la cuenta de administrador y configurar el Webhook en producción posteriormente. La verificación en Meta sigue pausada hasta que el flujo local esté listo.

## Siguientes pasos (post-MVP)
- Configurar credenciales reales en `.env`
- Levantar infraestructura con `docker compose up -d`
- Importar workflow en n8n
- Configurar webhook en Meta Developer
- Ejecutar pruebas manuales con guion de demo