# Contrato de Datos — Atrios MVP

## JSON entre Sub-flujos

Este documento define la estructura de datos que fluye entre los sub-workflows de n8n.

### 1. Entrada (01_whatsapp_in.json → 02_ia_processing.json)

```json
{
  "phone": "51999888777",
  "message_text": "Hola, necesito cotizar cámaras",
  "message_id": "wamid.xxx",
  "timestamp": "2026-11-16T01:00:00Z"
}
```

### 2. Estado en Redis

Key: `conversation:{phone}`
TTL: 86400 segundos (24h)

```json
{
  "history": [
    { "role": "user", "text": "Hola", "timestamp": "..." },
    { "role": "assistant", "text": "¡Hola! ¿En qué puedo ayudarte?", "timestamp": "..." }
  ],
  "retry_count": 0,
  "lead_data": {
    "name": "",
    "company": "",
    "email": "",
    "phone": "",
    "service": "",
    "budget": ""
  }
}
```

### 3. Respuesta de Gemini (02_ia_processing.json → 03_zoho_out.json)

```json
{
  "response": "¡Claro! Tenemos cámaras desde S/ 350. ¿Cuántas necesitas?",
  "lead_data": {
    "name": "Juan Pérez",
    "company": "Constructora ABC",
    "email": "juan@abc.com",
    "phone": "51999888777",
    "service": "cámaras",
    "budget": "1500"
  },
  "intent": "cotizacion",
  "confidence": 0.85
}
```

### 4. Lead para Zoho CRM (03_zoho_out.json)

```json
{
  "data": [{
    "Last_Name": "Juan Pérez",
    "Company": "Constructora ABC",
    "Email": "juan@abc.com",
    "Phone": "51999888777",
    "Description": "Servicio: cámaras | Interes: cotizacion | Confianza: 0.85 | Origen: WhatsApp Bot [TEST]"
  }]
}
```

## Reglas

- **Prohibido** cambiar la estructura del JSON sin actualizar este contrato
- Todos los sub-flujos deben usar los mismos nombres de campos
- El campo `intent` puede ser: `cotizacion`, `info`, `no_interesado`, `humano`
- El campo `confidence` es un float entre 0.0 y 1.0
- Si `confidence` < 0.7 después de 3 intentos → derivar a humano