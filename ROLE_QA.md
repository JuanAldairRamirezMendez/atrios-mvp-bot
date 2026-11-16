# ROLE_QA.md

## QA / Tester UAT — Jostyn

**Responsabilidades:**
- Ejecutar pruebas manuales con el guion de demo
- Reportar bugs con pasos de reproducción claros
- Validar que el bot responde correctamente a las 5+ intenciones del guion
- Verificar que los Leads se crean correctamente en Zoho CRM
- Confirmar que el fallback a humano funciona cuando la confianza es baja
- Probar con `tests/payload_whatsapp.json` y `tests/test_cases.md`

**Archivos bajo su control:**
- `docs/DEMO_SCRIPT.md` — Guion exacto de la presentación
- `tests/test_cases.md` — Matriz de pruebas
- `tests/payload_whatsapp.json` — Payload de simulación

**Flujo de trabajo:**
1. Hacer checkout de la rama del PR
2. Levantar entorno local (`cd infra && docker compose up -d`)
3. Ejecutar casos de prueba
4. Reportar bugs como GitHub Issues con etiqueta `bug`
5. Aprobar o rechazar el PR en GitHub