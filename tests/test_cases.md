# Casos de Prueba — Atrios MVP

## Matriz de Pruebas para QA (Jostyn)

| ID | Caso | Entrada | Resultado Esperado | Tipo |
|----|------|---------|-------------------|------|
| TC01 | Consulta general | "Hola, ¿qué servicios ofrecen?" | Bot lista servicios y pregunta interés | Funcional |
| TC02 | Cotización | "Necesito cotizar cámaras" | Bot extrae servicio=cámaras, pide detalles | Funcional |
| TC03 | Datos contacto | "Mi nombre es Juan, empresa ABC" | Bot confirma datos extraídos | Funcional |
| TC04 | Mensaje ambiguo | "No sé qué necesito" | Bot hace preguntas guía o deriva a humano | Fallback |
| TC05 | Fuera de horario | (cualquier mensaje) | Bot responde en español profesional | Funcional |
| TC06 | Rate limit Gemini | Simular 429 | n8n reintenta hasta 3 veces | No Funcional |
| TC07 | Redis caído | Stop Redis container | Bot responde error graceful | No Funcional |
| TC08 | Zoho caído | Simular 500 | Lead no se crea, respuesta al usuario OK | No Funcional |

## Payload de Prueba

Ver `payload_whatsapp.json` para simular mensaje entrante de Meta.

## Checklist Pre-Commit (para Devs)

- [ ] El JSON de n8n es válido (sin errores de sintaxis)
- [ ] No hay credenciales hardcodeadas en el workflow
- [ ] Commit sigue convención: `feat:`, `fix:`, `chore:`, etc.
- [ ] PR va dirigido a `develop`, nunca a `main`