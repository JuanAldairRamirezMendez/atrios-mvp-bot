# PLAN.md

**Proyecto:** MVP de Transformación Digital para Atrios (Sistema de Información Empresarial)
**Fecha:** 15/10/2026
**Versión:** 1.3 (Final - Incluye roles, Git y Seguridad)

## 1. Problem & scope
*   **Problema:** Atrios (microempresa de seguridad electrónica) pierde prospectos y depende de procesos manuales en WhatsApp personal.
*   **Alcance del MVP (Core):** Demostrar el flujo comercial automatizado. Un cliente escribe al Sandbox de WhatsApp, el bot (Gemini) responde, extrae los datos y n8n crea el Lead en Zoho CRM.
*   **Fuera de Alcance (Backlog):** Gestión de técnicos en campo y subida de evidencias fotográficas a Firebase (Ruta B).
*   **Contexto:** Proyecto universitario (UTP), con potencial de ser presentado a la empresa real en el futuro.

## 2. Requirements
*   **Funcionales:**
    *   Recepción de mensajes vía WhatsApp Cloud API (Entorno Sandbox).
    *   Procesamiento de lenguaje natural con Google AI Studio (Gemini).
    *   Derivación a humano cuando la IA no esté segura (fallback).
    *   Creación y actualización de Leads en Zoho CRM Free Edition.
*   **No Funcionales:**
    *   **Manejo de Rate Limits:** Loop de hasta 3 intentos para Gemini. Respuesta inmediata de `200 OK` a Meta para evitar timeouts.
    *   **Infraestructura:** n8n en Docker (Local/ngrok) y Railway (para despliegue final). Redis en Railway para el estado.
    *   **Gestión de Estado:** Uso de Redis para mantener el contexto de la conversación por número de teléfono.

## 3. Architecture overview
*   **Componentes:**
    *   **WhatsApp Cloud API (Meta):** Punto de entrada del mensaje del cliente.
    *   **n8n (Self-hosted):** Orquestador central. Recibe webhooks, gestiona la lógica, el estado en Redis, y llama a las APIs.
    *   **Google AI Studio (Gemini):** Motor de IA para comprensión de texto.
    *   **Zoho CRM:** Base de datos de prospectos (Leads).
    *   **Redis:** Almacenamiento en caché del estado de la conversación (desplegado en Railway).
*   **Flujo (Texto):**
    `Cliente -> WhatsApp API -> [Webhook] -> n8n -> (Responde 200 OK a Meta) -> [Consulta estado a Redis] -> [Loop a Gemini] -> [Crea/Actualiza Lead en Zoho] -> [HTTP POST a WhatsApp API para enviar respuesta final al Cliente]`

## 4. External integrations & APIs

| Servicio | Propósito | Autenticación | Modo de Falla | Costo |
| :--- | :--- | :--- | :--- | :--- |
| **WhatsApp Cloud API** | Canal de chat | Token temporal (Sandbox) | Timeout si no se responde rápido; Expiración de token a las 24h. | Gratis (Sandbox) |
| **Gemini (Google AI Studio)** | Procesamiento de lenguaje | API Key | Rate Limit (Límite de peticiones). Se resuelve con reintentos. | Gratis (Tier Free) |
| **Zoho CRM** | Base de datos de Leads | OAuth 2.0 | Límite de 5,000 llamadas diarias. | Gratis (Free Edition) |
| **Redis** | Estado de la conversación | Password | Pérdida de contexto de la conversación. | Gratis (Railway) |

## 5. DevOps plan
*   **Entornos:** Desarrollo (Local con Docker + ngrok) y Demo/Prod (Railway). Se usará la misma cuenta de Zoho, con prefijo `[TEST]` para limpiar leads falsos antes de la presentación.
*   **CI/CD:** Repositorio en GitHub conectado a Railway para el despliegue del contenedor de n8n.
*   **Secretos:** Uso de `N8N_ENCRYPTION_KEY` en Railway y montaje de un **Volumen Persistente** en `/home/node/.n8n`. Uso de credenciales nativas de n8n.
*   **Rollback:** Si Railway falla durante la demo, se levanta el entorno local con Docker y se usa una nueva URL de ngrok (Plan B manual).

## 6. Security & distribution model
*   **Distribución:** Código fuente privado en el repositorio. No hay distribución de binarios.
*   **BLA (Blind LLM Agents):** **NO APLICA**.
*   **Seguridad en el MVP:** 
    *   Contraseña configurada para Redis.
    *   No se validará la firma HMAC (`X-Hub-Signature-256`) de Meta por falta de tiempo (Riesgo aceptado).
    *   **Auditoría de Código:** Uso exclusivo de **Trufflehog** en GitHub Actions para evitar la filtración de credenciales (API Keys, tokens) en los Pull Requests.

## 7. Testing & reliability plan
*   **Pruebas:** Manuales, basadas en un "Guion de Demo" con al menos 5 intenciones de usuario distintas.
*   **Confiabilidad:** Uso de un nodo de "Error Trigger" en n8n conectado a Telegram/Discord para alertas en tiempo real al equipo.

## 8. Maintenance plan
*   **Dueño:** El equipo del proyecto. Si el MVP es exitoso, se evaluará la transferencia y viabilidad comercial frente a competidores para Atrios.
*   **Actualizaciones:** El "prompt" de Gemini se almacenará dentro del nodo de n8n (respaldado en GitHub).
*   **Limitaciones:** El equipo no tiene la capacidad actual de mantener actualizaciones de las APIs a largo plazo.

## 9. Open risks (Riesgos Reales)
1.  **Expiración del Token de WhatsApp (Alto):** El token del Sandbox expira cada 24h. Obligatorio renovarlo horas antes de la demo.
2.  **Contaminación de datos en Zoho (Medio):** Mezclar datos de prueba con datos de demo. Requiere limpieza manual el día anterior.
3.  **Conflictos de Git en n8n (Alto):** Múltiples desarrolladores editando el mismo JSON de flujo de n8n causará conflictos de fusión irreparables.

## 10. Assumptions log
*   **[ASSUMPTION] Entorno Meta:** Se usará el Sandbox de Meta y los números de prueba autorizados, sin verificación de negocio.
*   **[ASSUMPTION] Firma Webhook:** No se validará criptográficamente la firma de Meta (Riesgo aceptado).
*   **[ASSUMPTION] Redis:** Se desplegará como un servicio independiente en Railway conectado por red privada a n8n.
*   **[ASSUMPTION] OAuth Zoho:** El Scrum Master (Tú) será el dueño de la cuenta y gestionará los permisos.

## 11. Open questions (Pendientes)
*   ¿Atrios estará dispuesta a pagar los costos de la API de WhatsApp a futuro si el MVP les gusta?
*   ¿Cómo se manejará la rotación del personal técnico en el sistema de Firebase en producción?

## 12. Decisions log
*   **Uso de Zoho CRM Free:** Elegido por alineación con el documento original.
*   **Uso de n8n:** Elegido sobre Zapier/Make por la capacidad de self-hosting.
*   **Arquitectura Asíncrona:** Responder a Meta con `200 OK` inmediato y procesar la IA en paralelo.
*   **Loop de Gemini:** Ciclo de reintentos (máximo 3) antes de derivar a un humano.
*   **Seguridad (Trufflehog):** Seleccionada como la única herramienta de auditoría para el MVP por su enfoque en prevención de filtración de secretos, descartando herramientas de análisis de código o contenedores por ser overkill para el alcance.

## 13. Team Collaboration & Git Workflow (Scrum)
*   **Estrategia de Ramas (GitFlow simplificado):** `main` (estable), `develop` (integración), `feature/nombre-tarea` (ramas individuales).
*   **Convención de Commits (Conventional Commits):** `feat:`, `fix:`, `docs:`, `chore:`, `test:`.
*   **Asignación por Roles (Ver archivos `ROLE_*.md`):**
    *   **Scrum Master (Tú):** Infraestructura, CI/CD, Seguridad (Trufflehog) y Documentación.
    *   **Product Owner (David):** Backlog, Criterios de Aceptación y configuración de Zoho.
    *   **Development Team (Alonzo, Benjamin, Antony):** Desarrollo de sub-flujos en n8n. Prohibido editar el mismo JSON dos personas a la vez.
    *   **Digital Champion / Tester UAT (Jostyn):** Pruebas manuales, Guion de Demo y reporte de bugs.
*   **Regla de Pull Requests:** Ningún PR se aprueba si falla la acción de Trufflehog (detección de secretos).