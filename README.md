# Gestor de pedidos - Casa de pastas - n8n

Workflow de automatización que atiende pedidos por WhatsApp con un agente de IA, pensado para un comercio de retiro en local (pastas y rotisería).

## 📋 Descripción

Automatiza la toma de pedidos por WhatsApp de principio a fin: recibe el mensaje del cliente, lo entiende (texto, audio o imagen), conversa para armar el pedido completo y lo deriva al local con un número de orden asignado. Pensado para reducir el tiempo que el dueño/encargado pierde respondiendo manualmente durante los picos de pedidos.

- Agente de IA (GPT-5.1) que conduce la conversación: saluda, muestra el menú con precios, pregunta variantes (relleno, salsa, tamaño), pide el nombre del cliente y confirma el pedido antes de derivarlo.
- Soporte multimodal: transcribe audios con Whisper y describe imágenes con GPT-4o, para que el cliente pueda pedir por el medio que le resulte más cómodo.
- Buffer de mensajes con Redis, para agrupar varios mensajes seguidos del mismo cliente antes de procesarlos como un solo pedido.
- Memoria conversacional persistente en Postgres, para mantener el contexto entre mensajes.
- Registro automático de cada pedido confirmado en una Google Sheet, con número de orden y fecha.
- Comandos administrativos (`/activar`, `/desactivar`, `/reset`) para que un humano tome el control de una conversación puntual en cualquier momento.
- Filtro básico de seguridad para detectar intentos de inyección de código/SQL en los mensajes entrantes.

## 🔄 Flujo del proceso

1. El cliente escribe por WhatsApp → el mensaje llega por webhook.
2. Se valida si el bot está activo para ese número y si el mensaje no es un comando administrativo.
3. Según el tipo de contenido (texto, audio o imagen), se normaliza a texto plano.
4. El agente de IA arma el pedido con el cliente, turno a turno, usando el catálogo y las reglas de conversación definidas.
5. Al confirmar el pedido, se genera un número de orden, se registra en Google Sheets y se envía un resumen al local.
6. La respuesta se divide en mensajes cortos (hasta 3 bloques) para que se lea más natural en WhatsApp.

## 🛠 Tecnologías usadas

**Automatización:** n8n (orquestación del flujo completo)

**IA:** OpenAI GPT-5.1 (agente conversacional), GPT-4o (análisis de imágenes), Whisper (transcripción de audio)

**Datos:** PostgreSQL (memoria conversacional y estado del bot), Redis (buffer de mensajes), Google Sheets (registro de pedidos)

**Integración WhatsApp:** GoHighLevel (GHL) API

![](https://skillicons.dev/icons?i=n8n,postgres,redis,js)

## 💻 Instalación y despliegue

> ⚠️ Este workflow usa GoHighLevel (GHL) como proveedor de WhatsApp API. Si lo corrés con otro proveedor, vas a tener que adaptar el nodo `Webhook` de entrada (formato del payload) y los nodos `HTTP Request` que envían las respuestas (endpoint y autenticación).

```bash
# 1. Importar el workflow
# En tu instancia de n8n: Workflows → Import from File → seleccionar el .json de este repo

# 2. Configurar credenciales en n8n
# - OpenAI (API key)
# - PostgreSQL (conexión a la base usada para memoria e historial)
# - Redis (conexión para el buffer de mensajes)
# - Google Sheets (OAuth2, con acceso a la planilla de pedidos)
# - GoHighLevel / proveedor de WhatsApp API (token de autenticación)

# 3. Reemplazar valores propios del negocio
# - URL del webhook (se genera al activar el workflow en n8n)
# - ID de la Google Sheet de pedidos
# - Catálogo de productos y precios (nodo "Catálogo y precios")
# - Número(s) de WhatsApp autorizados para comandos administrativos

# 4. Activar el workflow en n8n
```

## 📫 Contacto

Si buscas un perfil especializado en automatización de procesos con IA, trabajando con n8n, SQL y JavaScript asistido por IA:

- 📧 Email: [marcosantini03@gmail.com](mailto:marcosantini03@gmail.com)
- 💼 LinkedIn: [LinkedIn](https://www.linkedin.com/in/marco-santini-a0391b309/)
- 📄 CV: [CV](https://docs.google.com/document/d/1rJgnOuMdzq41D2oTrjc76o5_w-R3_nv15-Lxg2TFE54/edit?usp=sharing)
