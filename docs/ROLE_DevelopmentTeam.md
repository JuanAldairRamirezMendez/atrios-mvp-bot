# ROLE_DevelopmentTeam.md

## Development Team — Alonzo, Benjamin, Antony

**Responsabilidades:**
- Desarrollar sub-flujos en n8n
- Mantener el workflow `whatsapp-bot.json`
- Implementar lógica de Redis (estado, TTL)
- Integrar llamadas a Gemini y Zoho CRM

**Regla crítica:** Prohibido editar el mismo JSON de n8n dos personas a la vez. Usar ramas `feature/nombre-tarea`.

**Flujo de trabajo:**
1. Crear rama `feature/nombre-tarea` desde `develop`
2. Editar el workflow en n8n UI
3. Exportar JSON y hacer commit
4. Crear PR hacia `develop`
5. Esperar aprobación (Trufflehog debe pasar)