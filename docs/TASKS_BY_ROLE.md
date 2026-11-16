# Tareas por Rol — Atrios MVP

## Scrum Master (Tú)
- [ ] Crear repo en GitHub con ramas `main` (protegida) y `develop`
- [ ] Configurar GitHub Actions (Trufflehog)
- [ ] Mantener `infra/docker-compose.yml` y `infra/.env.example`
- [ ] Revisar PRs: verificar que Trufflehog pase en verde
- [ ] Aprobar merges a `develop`

## Product Owner (David)
- [ ] Configurar cuenta Zoho CRM Free Edition
- [ ] Crear campos personalizados en Leads si es necesario
- [ ] Definir criterios de aceptación para cada historia de usuario
- [ ] Aprobar flujos de conversación del bot

## Development Team (Alonzo, Benjamin, Antony)

### Alonzo — `workflows/subflows/01_whatsapp_in.json`
- [ ] Crear webhook node para recibir mensajes de Meta
- [ ] Responder 200 OK inmediato
- [ ] Extraer phone, message_text, message_id
- [ ] Pasar datos al siguiente sub-flujo

### Benjamin — `workflows/subflows/02_ia_processing.json`
- [ ] Consultar estado en Redis
- [ ] Construir contexto para Gemini
- [ ] Llamar Gemini API (loop máx 3 intentos)
- [ ] Parsear respuesta JSON
- [ ] Evaluar confianza (>= 0.7 o fallback)

### Antony — `workflows/subflows/03_zoho_out.json`
- [ ] Crear Lead en Zoho CRM
- [ ] Guardar estado en Redis
- [ ] Enviar respuesta por WhatsApp API

## QA / Tester UAT (Jostyn)
- [ ] Ejecutar `tests/test_cases.md` en local
- [ ] Probar con `tests/payload_whatsapp.json`
- [ ] Reportar bugs con pasos de reproducción
- [ ] Validar flujo completo antes de cada demo

---

**Regla crítica:** Prohibido editar el mismo JSON de n8n dos personas a la vez. Cada dev trabaja en su propio sub-flujo.