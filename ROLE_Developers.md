# ROLE_Developers.md

## Development Team — Alonzo, Benjamin, Antony

**Responsabilidades:**
- Desarrollar los sub-flujos de n8n en archivos separados (regla crítica anti-conflictos)
- Mantener la lógica de Redis (estado, TTL, limpieza)
- Integrar llamadas a Gemini y Zoho CRM

**Sub-flujos asignados:**

| Dev | Archivo | Responsabilidad |
|-----|---------|-----------------|
| Alonzo | `workflows/subflows/01_whatsapp_in.json` | Webhook, respuesta 200 OK, extracción de datos del mensaje |
| Benjamin | `workflows/subflows/02_ia_processing.json` | Consulta Redis, loop Gemini (máx 3), parseo JSON, evaluación de confianza |
| Antony | `workflows/subflows/03_zoho_out.json` | Crear Lead en Zoho, guardar estado, enviar respuesta WhatsApp |

**Archivo de referencia:**
- `docs/referencia/ejemplo_flujo_completo_para_devs.json` — Flujo completo de ejemplo. Usar como guía pero **dividir en 3 JSONs separados**.

**Regla crítica:** Prohibido editar el mismo JSON de n8n dos personas a la vez. Cada dev trabaja únicamente en su sub-flujo.

**Flujo de trabajo:**
1. Crear rama `feature/nombre-tarea` desde `develop`
2. Editar tu sub-flujo en n8n UI
3. Exportar JSON y hacer commit
4. Crear PR hacia `develop`
5. Esperar aprobación (Trufflehog debe pasar)