# Guion de Demo — Atrios MVP

## Pre-requisitos
- Docker corriendo (`docker compose up -d`)
- ngrok activo
- Workflow importado en n8n
- Webhook configurado en Meta
- Token de WhatsApp vigente (renovar antes de la demo)
- Zoho CRM con prefijo `[TEST]` para limpiar después

## Intenciones a probar

### 1. Consulta general de servicios
**Usuario escribe:** "Hola, ¿qué servicios ofrecen?"
**Esperado:** Bot responde con lista de servicios (cámaras, alarmas, control de acceso) y pregunta qué le interesa.

### 2. Solicitud de cotización
**Usuario escribe:** "Necesito cotizar 4 cámaras para mi oficina"
**Esperado:** Bot extrae datos (servicio: cámaras, cantidad: 4), responde con rango de precios o pide más detalles.

### 3. Pregunta técnica
**Usuario escribe:** "¿Las cámaras tienen visión nocturna?"
**Esperado:** Bot responde con especificación técnica y ofrece agendar visita.

### 4. Datos de contacto
**Usuario escribe:** "Mi nombre es Juan Pérez, mi empresa es Constructora ABC, mi correo es juan@abc.com"
**Esperado:** Bot confirma datos extraídos y pregunta si desea que un asesor lo contacte.

### 5. Mensaje ambiguo (Fallback)
**Usuario escribe:** "No sé qué necesito, solo quiero mejorar la seguridad"
**Esperado:** Bot responde con preguntas guía O deriva a humano si confianza < 0.7 después de 3 intentos.

## Post-demo
1. Verificar Leads creados en Zoho CRM
2. Limpiar datos de prueba (buscar `[TEST]` en descripción)
3. Renovar token de WhatsApp si es necesario