---
title: REST API-Azure Event Grid IoT Edge | Microsoft Docs
description: REST API Event Grid na IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 414487d460d897eff787b11915db560706b29eb4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171750"
---
# <a name="rest-api"></a>REST API
Tento článek popisuje rozhraní REST API Azure Event Grid v IoT Edge

## <a name="common-api-behavior"></a>Běžné chování rozhraní API

### <a name="base-url"></a>Základní adresa URL
Event Grid v IoT Edge má následující rozhraní API vystavené přes protokol HTTP (port 5888) a HTTPS (port 4438).

* Základní adresa URL pro HTTP: http://eventgridmodule:5888
* Základní adresa URL pro protokol HTTPS: https://eventgridmodule:4438

### <a name="request-query-string"></a>Vyžádat řetězec dotazu
Všechny požadavky rozhraní API vyžadují následující parametr řetězce dotazu:

```?api-version=2019-01-01-preview```

### <a name="request-content-type"></a>Typ obsahu žádosti
Všechny požadavky rozhraní API musí mít **typ Content-Type**.

V případě **EventGridSchema** nebo **CustomSchema**může být hodnotou Content-type jedna z následujících hodnot:

```Content-Type: application/json```

```Content-Type: application/json; charset=utf-8```

V případě **CloudEventSchemaV1_0** ve strukturovaném režimu může být hodnotou Content-type jedna z následujících hodnot:

```Content-Type: application/cloudevents+json```
    
```Content-Type: application/cloudevents+json; charset=utf-8```
    
```Content-Type: application/cloudevents-batch+json```
    
```Content-Type: application/cloudevents-batch+json; charset=utf-8```

V případě **CloudEventSchemaV1_0** v binárním režimu najdete podrobnosti v [dokumentaci](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md) .

### <a name="error-response"></a>Chybová odezva
Všechna rozhraní API vrací chybu s následující datovou částí:

```json
{
    "error":
    {
        "code": "<HTTP STATUS CODE>",
        "details": 
        {
            "code": "<Detailed Error Code>",
            "message": "..."
        }
    }
}
```

## <a name="manage-topics"></a>Spravovat témata

### <a name="put-topic-create--update"></a>Vložit téma (vytvořit/aktualizovat)

**Požadavek**: ``` PUT /topics/<topic_name>?api-version=2019-01-01-preview ```

**Datová část**:

```json
    {
        "name": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "properties":
        {
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // optional
        }
    }
```

**Odpověď**: http 200

**Datová část**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<get_request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // populated with EventGridSchema if not explicitly specified in PUT request
    }
}
```

### <a name="get-topic"></a>Získat téma

**Požadavek**: ``` GET /topics/<topic_name>?api-version=2019-01-01-preview ```

**Odpověď**: http 200

**Datová část**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
    }
}
```

### <a name="get-all-topics"></a>Získat všechna témata

**Požadavek**: ``` GET /topics?api-version=2019-01-01-preview ```

**Odpověď**: http 200

**Datová část**:
```json
[
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    },
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    }
]
```

### <a name="delete-topic"></a>Odstranit téma

**Požadavek**: ``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**Odpověď**: http 200, prázdná datová část

## <a name="manage-event-subscriptions"></a>Správa odběrů událostí
Ukázky v tomto oddílu používají `EndpointType=Webhook;` . Ukázky JSON pro `EndpointType=EdgeHub / EndpointType=EventGrid` jsou v následující části. 

### <a name="put-event-subscription-create--update"></a>Vložit odběr události (vytvořit nebo aktualizovat)

**Požadavek**: ``` PUT /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Datová část**:
```json
{
    "name": "<subscription_name>", // optional, inferred from URL. If specified must match URL subscription_name
    "properties":
    {
        "topicName": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0", // optional
        "retryPolicy":  //optional
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "persistencePolicy": "true",
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // optional
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

**Odpověď**: http 200

**Datová část**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```


### <a name="get-event-subscription"></a>Získat odběr události

**Požadavek**: ``` GET /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Odpověď**: http 200

**Datová část**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

### <a name="get-event-subscriptions"></a>Získat odběry událostí

**Požadavek**: ``` GET /topics/<topic_name>/eventSubscriptions?api-version=2019-01-01-preview ```

**Odpověď**: http 200

**Datová část**:
```json
[
    {
        // same event-subscription json as that returned from Get-EventSubscription above
    },
    {
    },
    ...
]
```

### <a name="delete-event-subscription"></a>Odstranit odběr události

**Požadavek**: ``` DELETE /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Odpověď**: http 200, žádná datová část


## <a name="publish-events-api"></a>Rozhraní API pro publikování událostí

### <a name="send-batch-of-events-in-event-grid-schema"></a>Odeslat dávku událostí (ve schématu Event Grid)

**Požadavek**: ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        "id": "<user-defined-event-id>",
        "topic": "<topic_name>",
        "subject": "",
        "eventType": "",
        "eventTime": ""
        "dataVersion": "",
        "metadataVersion": "1",
        "data": 
            ...
    }
]
```

**Odpověď**: http 200, prázdná datová část


**Popisy polí datové části**
- ```Id``` je povinné. Může to být jakákoli řetězcová hodnota, která je vyplněna volajícím. Event Grid neprovádí žádnou duplicitu ani neuplatňuje žádné sémantiky v tomto poli.
- ```Topic``` je volitelný, ale pokud je zadaný, musí odpovídat topic_name z adresy URL požadavku.
- ```Subject``` je povinná, může být libovolná hodnota řetězce.
- ```EventType``` je povinná, může být libovolná hodnota řetězce.
- ```EventTime``` je povinná, není ověřená, ale měla by být správného typu DateTime.
- ```DataVersion``` je povinné
- ```MetadataVersion``` je nepovinný, pokud je zadaný, musí to být řetězec s hodnotou. ```"1"```
- ```Data``` je volitelná a může to být libovolný token JSON (číslo, řetězec, logická hodnota, pole, objekt).

### <a name="send-batch-of-events-in-custom-schema"></a>Odeslat dávku událostí (ve vlastním schématu)

**Požadavek**: ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        ...
    }
]
```

**Odpověď**: http 200, prázdná datová část


**Omezení datové části**
- MUSÍ se jednat o pole událostí.
- Každá položka pole musí být objekt JSON.
- Žádná jiná omezení (jiné než velikost datové části).

## <a name="examples"></a>Příklady

### <a name="set-up-topic-with-eventgrid-schema"></a>Nastavení tématu se schématem EventGrid
Nastaví téma pro vyžadování událostí, které budou publikovány v **eventgridschema**.

```json
    {
        "name": "myeventgridtopic",
        "properties":
        {
            "inputSchema": "EventGridSchema"
        }
    }
```

### <a name="set-up-topic-with-custom-schema"></a>Nastavení tématu s vlastním schématem
Nastaví téma pro vyžadování událostí, které mají být publikovány v nástroji `customschema` .

```json
    {
        "name": "mycustomschematopic",
        "properties":
        {
            "inputSchema": "CustomSchema"
        }
    }
```

### <a name="set-up-topic-with-cloud-event-schema"></a>Nastavení tématu se schématem cloudové události
Nastaví téma pro vyžadování událostí, které mají být publikovány v nástroji `cloudeventschema` .

```json
    {
        "name": "mycloudeventschematopic",
        "properties":
        {
            "inputSchema": "CloudEventSchemaV1_0"
        }
    }
```

### <a name="set-up-webhook-as-destination-events-to-be-delivered-in-eventgridschema"></a>Nastavení Webhooku jako cíle, události, které se mají doručit v eventgridschema
Tento typ cíle použijte k odeslání událostí do jakéhokoli jiného modulu (který je hostitelem koncového bodu HTTP) nebo do libovolného koncového bodu s adresou HTTP na síti nebo Internetu.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "eventDeliverySchema": "eventgridschema",
            }
        }
    }
}
```

Omezení pro `endpointUrl` atribut:
- Hodnota nesmí být null.
- Musí se jednat o absolutní adresu URL.
- Je-outbound__webhook__httpsOnly li v nastavení EventGridModule nastaveno na hodnotu true, musí být pouze HTTPS.
- Pokud je outbound__webhook__httpsOnly nastaveno na hodnotu false, může to být HTTP nebo HTTPS.

Omezení `eventDeliverySchema` vlastnosti:
- Musí odpovídat vstupnímu schématu tématu přihlášení k odběru.
- Může mít hodnotu null. Ve výchozím nastavení se jedná o schéma vstupu tématu.

### <a name="set-up-iot-edge-as-destination"></a>Nastavit IoT Edge jako cíl

Pomocí tohoto cíle můžete odesílat události do centra IoT Edge a podrobit se subsystému směrování/filtrování a předávání hraničního centra.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EdgeHub",
            "properties":
            {
                "outputName": "<eventgridmodule_output_port_name>"
            }
        }
    }
}
```

### <a name="set-up-event-grid-cloud-as-destination"></a>Nastavit Event Grid Cloud jako cíl

Pomocí tohoto cíle můžete odesílat události do Event Grid v cloudu (Azure). Předtím, než vytvoříte odběr události na hraničních zařízeních, budete muset nejprve nastavit uživatelské téma v cloudu, do kterého se mají události odesílat.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EventGrid",
            "properties":
            {
                "endpointUrl": "<eventgrid_user_topic_url>",
                "sasKey": "<user_topic_sas_key>",
                "topicName": "<new value to populate in forwarded EventGridEvent.Topic>" // if not specified, the Topic field on every event gets nulled out before being sent to Azure Event Grid
            }
        }
    }
}
```

EndpointUrl
- Hodnota nesmí být null.
- Musí se jednat o absolutní adresu URL.
- Cesta `/api/events` musí být definována v cestě URL požadavku.
- Musí mít `api-version=2018-01-01` v řetězci dotazu.
- Outbound__eventgrid__httpsOnly Pokud je v nastavení EventGridModule nastavena hodnota true (výchozí hodnota je true), musí být pouze HTTPS.
- Pokud je outbound__eventgrid__httpsOnly nastavené na false, může to být HTTP nebo HTTPS.
- Pokud je outbound__eventgrid__allowInvalidHostnames nastaveno na hodnotu false (výchozí hodnota je false), musí se cílit na jeden z následujících koncových bodů:
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

SasKey:
- Musí mít hodnotu, která není null.

Téma:
- Pokud je předplatné. EventDeliverySchema nastavené na EventGridSchema, hodnota z tohoto pole se před přesměrováním do Event Grid v cloudu vloží do každého pole události.
- Pokud je předplatné. EventDeliverySchema nastavené na CustomEventSchema, tato vlastnost se ignoruje a vlastní datová část události se přepošle přesně tak, jak byla přijata.

## <a name="set-up-event-hubs-as-a-destination"></a>Nastavit Event Hubs jako cíl

Pokud chcete publikovat do centra událostí, nastavte na `endpointType` `eventHub` a poskytnutí:

* connectionString: připojovací řetězec pro konkrétní centrum událostí, které cílíte vygenerovat prostřednictvím zásad sdíleného přístupu.

    >[!NOTE]
    > Připojovací řetězec musí být specifický pro entitu. Použití připojovacího řetězce oboru názvů nebude fungovat. Připojovací řetězec specifický pro entitu můžete vygenerovat tak, že přejdete do konkrétního centra událostí, na které chcete publikovat na portálu Azure Portal, a kliknutím na **zásady sdíleného přístupu** vygenerujete nový connecection řetězec specifický pro danou entitu.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-queues-as-a-destination"></a>Nastavit Service Bus fronty jako cíl

Chcete-li publikovat do fronty Service Bus, nastavte `endpointType` na `serviceBusQueue` a zadejte:

* connectionString: připojovací řetězec pro konkrétní frontu Service Bus, na kterou cílíte, vygenerovali pomocí zásad sdíleného přístupu.

    >[!NOTE]
    > Připojovací řetězec musí být specifický pro entitu. Použití připojovacího řetězce oboru názvů nebude fungovat. Vygenerujte připojovací řetězec specifický pro entitu tak, že přejdete na konkrétní Service Bus frontu, na kterou chcete publikovat na portálu Azure Portal, a kliknutím na **zásady sdíleného přístupu** vygenerujete nový connecection řetězec specifický pro danou entitu.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-topics-as-a-destination"></a>Nastavit Service Bus témata jako cíl

Chcete-li publikovat do tématu Service Bus, nastavte `endpointType` na `serviceBusTopic` a poskytnutí:

* connectionString: připojovací řetězec pro konkrétní Service Bus téma, které cílíte vygenerované pomocí zásad sdíleného přístupu.

    >[!NOTE]
    > Připojovací řetězec musí být specifický pro entitu. Použití připojovacího řetězce oboru názvů nebude fungovat. Vygenerujte připojovací řetězec specifický pro entitu tak, že přejdete na konkrétní Service Bus téma, na které byste chtěli publikovat na portálu Azure Portal, a kliknutím na **zásady sdíleného přístupu** vygenerujete nový connecection řetězec specifický pro danou entitu.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-storage-queues-as-a-destination"></a>Nastavit fronty úložiště jako cíl

K publikování do fronty úložiště nastavte  `endpointType` `storageQueue` a zadejte:

* Queue: název fronty úložiště, do které se chystáte publikovat.
* connectionString: připojovací řetězec pro účet úložiště, ve kterém je fronta úložiště.

    >[!NOTE]
    > Odřádkování Event Hubs, front Service Bus a Service Busch témat, připojovací řetězec používaný pro fronty úložiště není specifický pro entitu. Místo toho je třeba použít připojovací řetězec pro účet úložiště.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```