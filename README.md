# Checkpoint 1: Agente de triaje de consultas (Tuttilandia)

Primera versión del proyecto integrador de la carrera de IA Automation (CoderHouse). Es un agente autónomo en n8n que atiende consultas de clientes de Tuttilandia (marroquinería, Cáceres SRL), consulta un catálogo de productos y registra cada interacción por mail.

## Caso de uso

Un cliente escribe por chat. El agente clasifica la consulta (`STOCK_PRECIO`, `ENVIO`, `CAMBIO_RECLAMO`, `MAYORISTA` u `OTRO`). Si es sobre productos, responde con datos reales del catálogo. Si es cualquier otra cosa, deriva a una persona del equipo.

## Componentes del flujo

| Componente | Detalle |
|---|---|
| Disparador | Chat Trigger (`Mensaje del cliente`) |
| Cerebro | AI Agent en modo Tools Agent |
| Modelo | Groq Chat Model (`openai/gpt-oss-120b`) |
| Guardrail | Máximo de iteraciones: 10 |
| Herramienta 1 | Google Sheets Tool: consulta el catálogo (solo lectura) |
| Herramienta 2 | Gmail Tool: envía un log interno por cada consulta |
| System Prompt | Modular: Rol, Ámbito, Objetivo, Reglas, Escalamiento, Límites y Estilo |

Ambas herramientas están conectadas de forma lateral al agente y tienen una descripción manual que indica en qué casos usarlas.

## Por qué Groq

Se eligió Groq por costo y velocidad (plan gratuito para desarrollo). El nodo es nativo de n8n y compatible con Tools Agent. El flujo puede migrarse a OpenAI o Anthropic cambiando solo el nodo del modelo.

## Cómo probarlo

1. Importar `checkpoint1_milagros_sguazzini.json` en n8n (menú de tres puntos → Import from file).
2. Conectar credenciales propias de Groq, Google Sheets y Gmail.
3. Crear una hoja de Google Sheets con las columnas `Producto`, `Categoria`, `Color`, `Precio_ARS`, `Stock_Total` y vincularla en el nodo de Sheets.
4. Cambiar el destinatario del Gmail Tool por un mail propio.
5. Abrir el chat y enviar estos mensajes de prueba:
   - `Hola, buenas tardes. ¿Tienen mochilas disponibles? ¿Qué colores y precios manejan?`
   - `Compré una mochila hace una semana y llegó con el cierre roto. Quiero que me devuelvan la plata.`
   - `Hola, buenas tardes`

## Resultados esperados

| Prueba | Catálogo | Log por Gmail | Resultado |
|---|---|---|---|
| Consulta de productos | Sí | Sí (1 mail) | Responde con datos reales de la hoja |
| Reclamo | No | Sí (1 mail) | Deriva a una persona, sin inventar políticas |

Las capturas de las pruebas están en la carpeta `capturas/`.

## Evolución prevista

Este flujo es la base del proyecto integrador. En los próximos módulos se sumará multi-agente, memoria por sesión, integraciones reales, RAG sobre catálogo y políticas, y voz.

**Autora:** Milagros Sguazzini
