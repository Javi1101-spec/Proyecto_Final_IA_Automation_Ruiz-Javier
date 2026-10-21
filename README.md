
Ecosistema integral de atención al cliente y enrutamiento automatizado de consultas mediante procesamiento de lenguaje natural (NLP), persistencia relacional en Airtable CRM y escalación supervisada "Human-in-the-Loop".

---

## 📌 Enlaces del Proyecto

* **Base de Datos Relacional (Airtable - Solo Lectura):** [Acceder a la Base de Datos](https://airtable.com/appkCagYmb4iuipTt/shr5srVy97rjLTFBE)


---

## 🛠️ Pila Tecnológica

* **Canal Conversacional:** WhatsApp Business Cloud API (Meta Graph API).
* **Túnel y Red:** ngrok (exposición segura de endpoints locales en fase de desarrollo).
* **Orquestación:** n8n self-hosted (flujo transaccional con manejo de excepciones).
* **Motor Cognitivo (LLM):** Google Gemini Chat Model (`Basic LLM Chain`).
* **Persistencia / CRM:** Airtable (tablas relacionadas de Contactos y Tickets con control de estados).

---

## 🚀 Arquitectura y Funcionamiento del Flujo

1. **Ingesta Transaccional (Webhook POST):** Recepción de eventos en la ruta `/webhook/whatsapp` con respuesta inmediata `200 OK` para asegurar los SLAs de Meta y prevenir reintentos masivos de red.
2. **Filtro Anti-Loop (`If1`):** Validación estricta de presencia de mensajes entrantes (`messages exists`) para descartar acuses de recibo (`delivered`/`read`) o ecos generados por los mensajes salientes del propio bot.
3. **Persistencia en CRM (`Contactos`):** Inserción/actualización de la entidad cliente (`Telefono_ID` en formato internacional E.164 y nombre).
4. **Razonamiento Cognitivo (Gemini):** Análisis contextual del mensaje entrante y clasificación binaria estructurada en JSON plano:
   * **`GENERAL`:** Preguntas frecuentes, horarios, ubicación, servicios estándar o saludos.
   * **`ESPECIFICA`:** Cotizaciones a medida, quejas, reclamos o solicitudes explícitas de asistencia humana.
5. **Human-in-the-Loop y Enrutamiento (`If`):**
   * **Ruta General (`false`):** Envío autónomo de la respuesta redactada por la IA directamente al WhatsApp del cliente (`Send message`).
   * **Ruta Específica (`true`):** Mensaje de cortesía inmediato al cliente avisando la derivación (`automensaje`) y despacho proactivo de alerta al WhatsApp privado del asesor humano (`mensaje especifico`) con el resumen estructurado del caso.

---

## 📸 Evidencias del Sistema

### 1. Diagrama de la Arquitectura en n8n
<img width="1436" height="646" alt="Captura de pantalla 2026-10-21 a las 4 23 44 p  m" src="https://github.com/user-attachments/assets/497fbef1-dd29-4391-80ad-4533e2440a23" />


### 2. Base de Datos Relacional en Airtable (Contactos y Tickets)
<img width="1497" height="730" alt="Captura de pantalla 2026-10-21 a las 4 28 25 p  m" src="https://github.com/user-attachments/assets/5b7ce410-5266-4a66-a2b9-25e14872418d" />
<img width="1512" height="734" alt="Captura de pantalla 2026-10-21 a las 4 28 36 p  m" src="https://github.com/user-attachments/assets/969e38da-e39b-4ee5-8ff8-d5c40d0de416" />



### 3. Prueba de Interacción en WhatsApp
<img width="1179" height="1928" alt="IMG_0769" src="https://github.com/user-attachments/assets/43f35ec2-c176-40b8-87d9-a55f7f0e98d8" />

---

## 📁 Archivos Entregables en este Repositorio

* `Entrega_Final_Ecosistema_Automatizacion_IA-v3.pdf`: Documento técnico oficial con la matriz de 5 pruebas de estrés y checklist de auditoría pre-entrega.
* [Entrega_Final_Ecosistema_Automatizacion_IA-Ruiz_Javier.pdf](https://github.com/user-attachments/files/33788054/Entrega_Final_Ecosistema_Automatizacion_IA-Ruiz_Javier.pdf)

* `workflow_whatsapp_ia.json`: Blueprint exportable del flujo para importar directamente en cualquier instancia de n8n.
[Automatización.json](https://github.com/user-attachments/files/33788074/Automatizacion.json)
{
  "name": "Automatización",
  "nodes": [
    {
      "parameters": {
        "assignments": {
          "assignments": [
            {
              "id": "0fe7c4c2-3ca5-4875-b205-7ce8e3d01b07",
              "name": "telefono",
              "value": "={{ $json.body.entry[0].changes[0].value.messages[0].from }}",
              "type": "string"
            },
            {
              "id": "0a25764d-2488-4638-bb8d-4f3860b52554",
              "name": "nombre",
              "value": "={{ $json.body.entry[0].changes[0].value.contacts[0].profile.name }}",
              "type": "string"
            },
            {
              "id": "eed706c1-ce7d-41c4-a378-96029ce51207",
              "name": "mensaje",
              "value": "={{ $json.body.entry[0].changes[0].value.messages[0].text.body }}",
              "type": "string"
            }
          ]
        },
        "options": {}
      },
      "type": "n8n-nodes-base.set",
      "typeVersion": 3.5,
      "position": [
        432,
        432
      ],
      "id": "0c258fb3-2f74-4568-9963-d5f4436e5033",
      "name": "Edit Fields"
    },
    {
      "parameters": {
        "operation": "create",
        "base": {
          "__rl": true,
          "value": "appkCagYmb4iuipTt",
          "mode": "id"
        },
        "table": {
          "__rl": true,
          "value": "tblWtcb76waWuaEvO",
          "mode": "list",
          "cachedResultName": "Contactos",
          "cachedResultUrl": "https://airtable.com/appkCagYmb4iuipTt/tblWtcb76waWuaEvO"
        },
        "columns": {
          "mappingMode": "defineBelow",
          "value": {
            "Nombre": "={{ $json.nombre }}",
            "Teléfono": "={{ $json.telefono }}"
          },
          "matchingColumns": [],
          "schema": [
            {
              "id": "Nombre",
              "displayName": "Nombre",
              "required": false,
              "defaultMatch": false,
              "canBeUsedToMatch": true,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": false
            },
            {
              "id": "Teléfono",
              "displayName": "Teléfono",
              "required": false,
              "defaultMatch": false,
              "canBeUsedToMatch": true,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": false
            },
            {
              "id": "Tickets",
              "displayName": "Tickets",
              "required": false,
              "defaultMatch": false,
              "canBeUsedToMatch": true,
              "display": true,
              "type": "array",
              "readOnly": false,
              "removed": false
            },
            {
              "id": "Tickets 2",
              "displayName": "Tickets 2",
              "required": false,
              "defaultMatch": false,
              "canBeUsedToMatch": true,
              "display": true,
              "type": "array",
              "readOnly": false,
              "removed": false
            }
          ],
          "attemptToConvertTypes": false,
          "convertFieldsToString": false
        },
        "options": {}
      },
      "type": "n8n-nodes-base.airtable",
      "typeVersion": 2.2,
      "position": [
        592,
        -384
      ],
      "id": "8181fcad-39f9-4e20-a3fb-e332ac7e088f",
      "name": "Contactos",
      "credentials": {
        "airtableTokenApi": {
          "id": "WZdnwmd7sxNJ3Pdp",
          "name": "Airtable Personal Access Token account"
        }
      }
    },
    {
      "parameters": {
        "operation": "create",
        "base": {
          "__rl": true,
          "value": "appkCagYmb4iuipTt",
          "mode": "list",
          "cachedResultName": "Base de datos automatización ",
          "cachedResultUrl": "https://airtable.com/appkCagYmb4iuipTt"
        },
        "table": {
          "__rl": true,
          "value": "tblNwJHVWiCaeUqN9",
          "mode": "list",
          "cachedResultName": "Tickets",
          "cachedResultUrl": "https://airtable.com/appkCagYmb4iuipTt/tblNwJHVWiCaeUqN9"
        },
        "columns": {
          "mappingMode": "defineBelow",
          "value": {
            "Mensaje": "={{ $('Edit Fields').item.json.mensaje }}",
            "Contacto": "={{ [ $('Contactos').item.json.id ] }}",
            "Estado": "Pendiente",
            "Respuesta IA": "={{ $json.respuesta_ia }}",
            "Categoria": "={{ $json.categoria }}"
          },
          "matchingColumns": [],
          "schema": [
            {
              "id": "ID",
              "displayName": "ID",
              "required": false,
              "defaultMatch": false,
              "canBeUsedToMatch": true,
              "display": true,
              "type": "string",
              "readOnly": true,
              "removed": true
            },
            {
              "id": "Contacto",
              "displayName": "Contacto",
              "required": false,
              "defaultMatch": false,
              "canBeUsedToMatch": true,
              "display": true,
              "type": "array",
              "readOnly": false,
              "removed": false
            },
            {
              "id": "Mensaje",
              "displayName": "Mensaje",
              "required": false,
              "defaultMatch": false,
              "canBeUsedToMatch": true,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": false
            },
            {
              "id": "Categoria",
              "displayName": "Categoria",
              "required": false,
              "defaultMatch": false,
              "canBeUsedToMatch": true,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": false
            },
            {
              "id": "Respuesta IA",
              "displayName": "Respuesta IA",
              "required": false,
              "defaultMatch": false,
              "canBeUsedToMatch": true,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": false
            },
            {
              "id": "Estado",
              "displayName": "Estado",
              "required": false,
              "defaultMatch": false,
              "canBeUsedToMatch": true,
              "display": true,
              "type": "options",
              "options": [
                {
                  "name": "Pendiente",
                  "value": "Pendiente"
                },
                {
                  "name": "Procesado por IA",
                  "value": "Procesado por IA"
                },
                {
                  "name": "Esperando aprobación",
                  "value": "Esperando aprobación"
                },
                {
                  "name": "Aprobado por humano ",
                  "value": "Aprobado por humano "
                },
                {
                  "name": "Error en IA",
                  "value": "Error en IA"
                },
                {
                  "name": "Cerrado",
                  "value": "Cerrado"
                }
              ],
              "readOnly": false,
              "removed": false
            },
            {
              "id": "Error Log",
              "displayName": "Error Log",
              "required": false,
              "defaultMatch": false,
              "canBeUsedToMatch": true,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": false
            },
            {
              "id": "Notes",
              "displayName": "Notes",
              "required": false,
              "defaultMatch": false,
              "canBeUsedToMatch": true,
              "display": true,
              "type": "string",
              "readOnly": false,
              "removed": false
            },
            {
              "id": "Contactos",
              "displayName": "Contactos",
              "required": false,
              "defaultMatch": false,
              "canBeUsedToMatch": true,
              "display": true,
              "type": "array",
              "readOnly": false,
              "removed": false
            }
          ],
          "attemptToConvertTypes": false,
          "convertFieldsToString": false
        },
        "options": {}
      },
      "type": "n8n-nodes-base.airtable",
      "typeVersion": 2.2,
      "position": [
        1456,
        384
      ],
      "id": "ea60d921-51c2-416e-9e85-6ad473e74b56",
      "name": "Tickets",
      "credentials": {
        "airtableTokenApi": {
          "id": "WZdnwmd7sxNJ3Pdp",
          "name": "Airtable Personal Access Token account"
        }
      }
    },
    {
      "parameters": {
        "conditions": {
          "options": {
            "caseSensitive": true,
            "leftValue": "",
            "typeValidation": "strict",
            "version": 3
          },
          "conditions": [
            {
              "id": "16ad3279-fc6d-4331-8673-07541040bbfa",
              "leftValue": "={{ $json.categoria }}",
              "rightValue": " ESPECIFICA",
              "operator": {
                "type": "string",
                "operation": "equals"
              }
            }
          ],
          "combinator": "and"
        },
        "options": {}
      },
      "type": "n8n-nodes-base.if",
      "typeVersion": 2.3,
      "position": [
        1600,
        144
      ],
      "id": "f0bc337c-4990-4b1d-8d01-5f26fe778eb8",
      "name": "If"
    },
    {
      "parameters": {
        "operation": "send",
        "phoneNumberId": "=1400361703155547",
        "recipientPhoneNumber": "={{ $('Edit Fields').item.json.telefono.replace(/^54911/, '541115') }}",
        "textBody": "Hemos recibido tu consulta puntual. Tu caso fue asignado a un asesor y te responderemos a la brevedad por este medio.",
        "additionalFields": {}
      },
      "type": "n8n-nodes-base.whatsApp",
      "typeVersion": 1.1,
      "position": [
        1984,
        -208
      ],
      "id": "99b3ca3e-a19d-49bd-9a77-0999406908c0",
      "name": "mensaje especifico",
      "webhookId": "d94714ad-f695-4dab-89e0-f50a23e99e7a",
      "credentials": {
        "whatsAppApi": {
          "id": "PfFOUAuOPXr00LSY",
          "name": "WhatsApp account"
        }
      }
    },
    {
      "parameters": {
        "operation": "send",
        "phoneNumberId": "=1400361703155547",
        "recipientPhoneNumber": "54111521646954",
        "textBody": "=🚨 *NUEVA CONSULTA ESPECÍFICA*\n👤 Cliente: {{ $('Edit Fields').item.json.telefono }}\n💬 Mensaje: {{ $('Edit Fields').item.json.mensaje }}",
        "additionalFields": {}
      },
      "type": "n8n-nodes-base.whatsApp",
      "typeVersion": 1.1,
      "position": [
        1760,
        -208
      ],
      "id": "a2d98099-2f64-46c9-b34f-f7ae326c1905",
      "name": "automensaje",
      "webhookId": "e1aa1d30-2d5e-4bf8-a695-d214c5239ce6",
      "credentials": {
        "whatsAppApi": {
          "id": "PfFOUAuOPXr00LSY",
          "name": "WhatsApp account"
        }
      }
    },
    {
      "parameters": {
        "amount": 10
      },
      "type": "n8n-nodes-base.wait",
      "typeVersion": 1.1,
      "position": [
        864,
        -304
      ],
      "id": "d34707fd-3da2-4984-b9e9-290530541df7",
      "name": "Wait",
      "webhookId": "48a0bf5c-0868-4e4f-81c5-c8ea2ce59f5e"
    },
    {
      "parameters": {
        "jsCode": "const raw = $json.text || $json.response || '';\nconst cleanText = raw.replace(/```json/g, '').replace(/```/g, '').trim();\n\nlet parsed = {};\ntry {\n  parsed = JSON.parse(cleanText);\n} catch (e) {\n  parsed = {\n    categoria: \"GENERAL\",\n    respuesta_cliente: raw,\n    resumen_para_humano: \"\"\n  };\n}\n\nreturn {\n  categoria: parsed.categoria,\n  respuesta_cliente: parsed.respuesta_cliente,\n  resumen_para_humano: parsed.resumen_para_humano || '',\n  telefono_cliente: $('Edit Fields').first().json.telefono_cliente || ''\n};\n\n"
      },
      "type": "n8n-nodes-base.code",
      "typeVersion": 2,
      "position": [
        1392,
        0
      ],
      "id": "ff0f7636-906d-4361-966e-2b09f9d6b06c",
      "name": "Code in JavaScript"
    },
    {
      "parameters": {
        "operation": "send",
        "phoneNumberId": "=1400361703155547",
        "recipientPhoneNumber": "={{ ($('Edit Fields').item?.json?.telefono || '').replace(/^54911/, '541115') || $('Edit Fields').item?.json?.from || '' }}",
        "textBody": "={{ $json.respuesta_cliente }}",
        "additionalFields": {}
      },
      "type": "n8n-nodes-base.whatsApp",
      "typeVersion": 1.1,
      "position": [
        1824,
        240
      ],
      "id": "efb6759e-25ed-4f59-ab2f-5ce21b2b0ed7",
      "name": "Send message",
      "webhookId": "cc2d1509-5195-4a2c-a4c8-ecb0509511e3",
      "credentials": {
        "whatsAppApi": {
          "id": "PfFOUAuOPXr00LSY",
          "name": "WhatsApp account"
        }
      }
    },
    {
      "parameters": {
        "promptType": "define",
        "text": "=Eres el asistente de atención al cliente de la empresa. Analiza el mensaje recibido:\n\"{{ $json.mensaje_cliente }}\"\n\nClasifícalo en una de estas dos categorías:\n1. \"GENERAL\": Preguntas frecuentes, saludos, información básica, dudas comunes o precios estándar.\n2. \"ESPECIFICA\": Casos complejos, presupuestos personalizados, quejas, reclamos o cuando el cliente pide hablar con una persona.\n\nResponde ÚNICAMENTE en formato JSON plano (sin formato markdown ni bloques ```json):\n{\n  \"categoria\": \"GENERAL\",\n  \"respuesta_cliente\": \"Texto de la respuesta para el cliente\",\n  \"resumen_para_humano\": \"\"\n}\n\nSi la categoría es \"ESPECIFICA\":\n- En \"respuesta_cliente\" escribe un mensaje amable avisándole al cliente que su consulta fue transferida a un asesor humano.\n- En \"resumen_para_humano\" escribe un resumen conciso de lo que necesita el cliente.\n\n",
        "batching": {}
      },
      "type": "@n8n/n8n-nodes-langchain.chainLlm",
      "typeVersion": 1.9,
      "position": [
        1296,
        -272
      ],
      "id": "2abe4a24-f95d-4b3b-9c8a-8dbfb11f4cef",
      "name": "Basic LLM Chain"
    },
    {
      "parameters": {
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.lmChatGoogleGemini",
      "typeVersion": 1.1,
      "position": [
        720,
        208
      ],
      "id": "340f0c1c-ea23-432d-addd-0ad229730ce2",
      "name": "Google Gemini Chat Model",
      "credentials": {
        "googlePalmApi": {
          "id": "45JPwqreSY5egGog",
          "name": "Google Gemini(PaLM) Api account 2"
        }
      }
    },
    {
      "parameters": {
        "httpMethod": "POST",
        "path": "whatsapp",
        "options": {
          "responseCode": {
            "values": {}
          }
        }
      },
      "type": "n8n-nodes-base.webhook",
      "typeVersion": 2.1,
      "position": [
        0,
        0
      ],
      "id": "c5eca3db-eb3d-4b98-a6db-dd9b3e5ae09a",
      "name": "Webhook",
      "webhookId": "a5e15b78-4f4d-4d88-acfc-052d78a8d340"
    },
    {
      "parameters": {
        "conditions": {
          "options": {
            "caseSensitive": true,
            "leftValue": "",
            "typeValidation": "strict",
            "version": 3
          },
          "conditions": [
            {
              "id": "b7ce2793-1613-496c-b55b-e0d12f906de3",
              "leftValue": "={{ $json.body.entry[0].changes[0].value.messages }}",
              "rightValue": "",
              "operator": {
                "type": "array",
                "operation": "exists",
                "singleValue": true
              }
            }
          ],
          "combinator": "and"
        },
        "options": {}
      },
      "type": "n8n-nodes-base.if",
      "typeVersion": 2.3,
      "position": [
        48,
        400
      ],
      "id": "7fbcb8c7-db05-489b-a35b-94615c688f5c",
      "name": "If1"
    }
  ],
  "pinData": {},
  "connections": {
    "Edit Fields": {
      "main": [
        [
          {
            "node": "Contactos",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Contactos": {
      "main": [
        [
          {
            "node": "Wait",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Tickets": {
      "main": [
        [
          {
            "node": "If",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "If": {
      "main": [
        [
          {
            "node": "automensaje",
            "type": "main",
            "index": 0
          }
        ],
        [
          {
            "node": "Send message",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "automensaje": {
      "main": [
        [
          {
            "node": "mensaje especifico",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Wait": {
      "main": [
        [
          {
            "node": "Basic LLM Chain",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Code in JavaScript": {
      "main": [
        [
          {
            "node": "Tickets",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Basic LLM Chain": {
      "main": [
        [
          {
            "node": "Code in JavaScript",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Google Gemini Chat Model": {
      "ai_languageModel": [
        [
          {
            "node": "Basic LLM Chain",
            "type": "ai_languageModel",
            "index": 0
          }
        ]
      ]
    },
    "Webhook": {
      "main": [
        [
          {
            "node": "If1",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "If1": {
      "main": [
        [
          {
            "node": "Edit Fields",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  },
  "active": true,
  "settings": {
    "executionOrder": "v1",
    "binaryMode": "separate"
  },
  "versionId": "f6a6d442-3fd0-4ad2-bbef-d1a5a3fab795",
  "meta": {
    "templateCredsSetupCompleted": true,
    "instanceId": "741dbfdce94992ceda2e351a44f0d8ea7d96a81337ad6e32a4c97a03e7a90e20"
  },
  "nodeGroups": [],
  "id": "sh2mx7DxNPCIFxDC",
  "tags": []
}
          

