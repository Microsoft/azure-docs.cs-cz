---
title: ROZHRANÍ REST API – Azure Event Grid IoT Edge | Dokumenty společnosti Microsoft
description: ROZHRANÍ REST API na síti událostí na IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 19f86b1d8233e05844201e1095c1f79324955cd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841825"
---
# <a name="rest-api"></a>REST API
Tento článek popisuje rest API azure event grid na IoT Edge

## <a name="common-api-behavior"></a>Běžné chování rozhraní API

### <a name="base-url"></a>Základní adresa URL
Event Grid na IoT Edge má následující api vystavené přes HTTP (port 5888) a HTTPS (port 4438).

* Základní adresa URL pro protokol HTTP:http://eventgridmodule:5888
* Základní adresa URL pro protokol HTTPS:https://eventgridmodule:4438

### <a name="request-query-string"></a>Řetězec dotazu požadavku
Všechny požadavky rozhraní API vyžadují následující parametr řetězce dotazu:

```?api-version=2019-01-01-preview```

### <a name="request-content-type"></a>Požádat o typ obsahu
Všechny požadavky rozhraní API musí mít **typ obsahu**.

V případě **EventGridSchema** nebo **CustomSchema**může být hodnota Content-Type jedna z následujících hodnot:

```Content-Type: application/json```

```Content-Type: application/json; charset=utf-8```

V případě **CloudEventSchemaV1_0** ve strukturovaném režimu může být hodnota typu Content jedna z následujících hodnot:

```Content-Type: application/cloudevents+json```
    
```Content-Type: application/cloudevents+json; charset=utf-8```
    
```Content-Type: application/cloudevents-batch+json```
    
```Content-Type: application/cloudevents-batch+json; charset=utf-8```

V případě **CloudEventSchemaV1_0** v binárním režimu, naleznete [v dokumentaci](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md) pro podrobnosti.

### <a name="error-response"></a>Odpověď na chybu
Všechna rozhraní API vrátí chybu s následující datovou částí:

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

## <a name="manage-topics"></a>Správa témat

### <a name="put-topic-create--update"></a>Put téma (vytvořit / aktualizovat)

**Žádost**:``` PUT /topics/<topic_name>?api-version=2019-01-01-preview ```

**Užitečné zatížení**:

```json
    {
        "name": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "properties":
        {
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // optional
        }
    }
```

**Odpověď**: HTTP 200

**Užitečné zatížení**:

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

**Žádost**:``` GET /topics/<topic_name>?api-version=2019-01-01-preview ```

**Odpověď**: HTTP 200

**Užitečné zatížení**:
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

**Žádost**:``` GET /topics?api-version=2019-01-01-preview ```

**Odpověď**: HTTP 200

**Užitečné zatížení**:
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

**Žádost**:``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**Odpověď**: HTTP 200, prázdná datová část

## <a name="manage-event-subscriptions"></a>Správa odběrů událostí
Vzorky v `EndpointType=Webhook;`této části používají . Vzorky jsonpro `EndpointType=EdgeHub / EndpointType=EventGrid` jsou v další části. 

### <a name="put-event-subscription-create--update"></a>Odběr událostí (vytvoření /aktualizace)

**Žádost**:``` PUT /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Užitečné zatížení**:
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

**Odpověď**: HTTP 200

**Užitečné zatížení**:

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


### <a name="get-event-subscription"></a>Získání předplatného událostí

**Žádost**:``` GET /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Odpověď**: HTTP 200

**Užitečné zatížení**:
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

### <a name="get-event-subscriptions"></a>Získání odběrů událostí

**Žádost**:``` GET /topics/<topic_name>/eventSubscriptions?api-version=2019-01-01-preview ```

**Odpověď**: HTTP 200

**Užitečné zatížení**:
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

### <a name="delete-event-subscription"></a>Odstranit odběr událostí

**Žádost**:``` DELETE /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Odpověď**: HTTP 200, bez datové části


## <a name="publish-events-api"></a>Publikovat rozhraní API událostí

### <a name="send-batch-of-events-in-event-grid-schema"></a>Odeslat dávku událostí (ve schématu Mřížky událostí)

**Žádost**:``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

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

**Odpověď**: HTTP 200, prázdná datová část


**Popisy polí datové části**
- ```Id```je povinné. Může se jedná o libovolnou hodnotu řetězce, která je naplněna volajícím. Event Grid neprovádí žádné vyhledávání duplicit nebo vynucovat sémantiku v tomto poli.
- ```Topic```je nepovinný, ale pokud je zadán, musí odpovídat topic_name z adresy URL požadavku
- ```Subject```je povinné, může být libovolná hodnota řetězce
- ```EventType```je povinné, může být libovolná hodnota řetězce
- ```EventTime```je povinné, není ověřena, ale měla by být správná DateTime.
- ```DataVersion```je povinné
- ```MetadataVersion```je nepovinné, pokud je zadán, musí to být řetězec s hodnotou```"1"```
- ```Data```je volitelný a může být libovolný token JSON (číslo, řetězec, logický, logický, maticový, objekt)

### <a name="send-batch-of-events-in-custom-schema"></a>Odeslat dávku událostí (ve vlastním schématu)

**Žádost**:``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        ...
    }
]
```

**Odpověď**: HTTP 200, prázdná datová část


**Omezení datovéčásti**
- MUSÍ být pole událostí.
- Každá položka pole musí být objektjového objektu JSON.
- Žádná jiná omezení (kromě velikosti datové části).

## <a name="examples"></a>Příklady

### <a name="set-up-topic-with-eventgrid-schema"></a>Nastavení tématu pomocí schématu EventGrid
Nastaví téma tak, aby vyžadovalo publikování událostí v **eventgridschema**.

```json
    {
        "name": "myeventgridtopic",
        "properties":
        {
            "inputSchema": "EventGridSchema"
        }
    }
```

### <a name="set-up-topic-with-custom-schema"></a>Nastavení tématu pomocí vlastního schématu
Nastaví téma, které bude vyžadovat `customschema`publikování událostí v aplikaci .

```json
    {
        "name": "mycustomschematopic",
        "properties":
        {
            "inputSchema": "CustomSchema"
        }
    }
```

### <a name="set-up-topic-with-cloud-event-schema"></a>Nastavení tématu pomocí schématu cloudových událostí
Nastaví téma, které bude vyžadovat `cloudeventschema`publikování událostí v aplikaci .

```json
    {
        "name": "mycloudeventschematopic",
        "properties":
        {
            "inputSchema": "CloudEventSchemaV1_0"
        }
    }
```

### <a name="set-up-webhook-as-destination-events-to-be-delivered-in-eventgridschema"></a>Nastavit WebHook jako cíl, události, které mají být dodány v eventgridschema
Tento cílový typ slouží k odesílání událostí do jiného modulu (který je hostitelem koncového bodu HTTP) nebo do libovolného koncového bodu HTTP adresovatelného v síti nebo internetu.

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

Omezení atributu: `endpointUrl`
- Musí být non-null.
- Musí to být absolutní adresa URL.
- Pokud je outbound__webhook__httpsOnly nastavena na hodnotu true v nastavení EventGridModule, musí být pouze https.
- Pokud outbound__webhook__httpsOnly nastavena na hodnotu false, může to být HTTP nebo HTTPS.

Omezení vlastnosti: `eventDeliverySchema`
- Musí odpovídat vstupnímu schématu tématu přihlášení.
- Může být null. Výchozí nastavení vstupní schéma tématu.

### <a name="set-up-iot-edge-as-destination"></a>Nastavení IoT Edge jako cíle

Tento cíl slouží k odesílání událostí do služby IoT Edge Hub a být podroben y podsystému směrování/filtrování/předávání hraničního centra.

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

### <a name="set-up-event-grid-cloud-as-destination"></a>Nastavit cloud sítě událostí jako cíl

Tento cíl slouží k odesílání událostí do event gridu v cloudu (Azure). Před vytvořením předplatného událostí na hraničních zařízeních budete muset nejprve nastavit uživatelské téma v cloudu, do kterého by se měly události odesílat.

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

Koncovka url:
- Musí být non-null.
- Musí to být absolutní adresa URL.
- Cesta `/api/events` musí být definována v cestě url požadavku.
- Musí mít `api-version=2018-01-01` v řetězci dotazu.
- Pokud je outbound__eventgrid__httpsOnly nastavena na hodnotu true v nastavení EventGridModule (true ve výchozím nastavení), musí být pouze https.
- Pokud je outbound__eventgrid__httpsOnly nastavena na false, může to být HTTP nebo HTTPS.
- Pokud je outbound__eventgrid__allowInvalidHostnames nastavena na false (false ve výchozím nastavení), musí cílit na jeden z následujících koncových bodů:
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

SasKey:
- Musí být non-null.

Název_ tématu:
- Pokud Subscription.EventDeliverySchema je nastavena na EventGridSchema, hodnota z tohoto pole je umístěn do každé události téma pole před předáním do Event Grid v cloudu.
- Pokud Subscription.EventDeliverySchema je nastavena na CustomEventSchema, tato vlastnost je ignorována a vlastní datové části události je předánpřesně tak, jak byla přijata.

## <a name="set-up-event-hubs-as-a-destination"></a>Nastavení centra událostí jako cíle

Chcete-li publikovat v centru `endpointType` `eventHub` událostí, nastavte to to a zadejte:

* ConnectionString: Připojovací řetězec pro konkrétní centrum událostí, na které cílíte, generovaný pomocí zásad sdíleného přístupu.

    >[!NOTE]
    > Připojovací řetězec musí být specifický pro entitu. Použití připojovacího řetězce oboru názvů nebude fungovat. Připojovací řetězec specifický pro entitu můžete vygenerovat tak, že přejdete do konkrétního centra událostí, do kterého chcete publikovat na webu Azure Portal, a kliknutím na **zásady sdíleného přístupu** vygenerujete nový řetězec connecection specifické pro entitu.

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

## <a name="set-up-service-bus-queues-as-a-destination"></a>Nastavení front sběrnice jako cíle

Chcete-li publikovat do fronty `endpointType` služby Service Bus, nastavte to to `serviceBusQueue` a zadejte:

* ConnectionString: Připojovací řetězec pro konkrétní frontu služby Service Bus, na kterou cílíte, generovaný pomocí zásad sdíleného přístupu.

    >[!NOTE]
    > Připojovací řetězec musí být specifický pro entitu. Použití připojovacího řetězce oboru názvů nebude fungovat. Vygenerujte připojovací řetězec specifický pro entitu tak, že přejdete na konkrétní frontu služby Service Bus, do které chcete publikovat na webu Azure Portal, a kliknutím na **zásady sdíleného přístupu** vygenerujete řetězec útěchy specifický pro novou entitu.

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

## <a name="set-up-service-bus-topics-as-a-destination"></a>Nastavení témat sběrnice jako cíle

Chcete-li publikovat v tématu `endpointType` `serviceBusTopic` sběrnice, nastavte to to a zadejte:

* ConnectionString: Připojovací řetězec pro konkrétní téma sběrnice, na které cílíte, generovaný pomocí zásad sdíleného přístupu.

    >[!NOTE]
    > Připojovací řetězec musí být specifický pro entitu. Použití připojovacího řetězce oboru názvů nebude fungovat. Vygenerujte připojovací řetězec specifický pro entitu tak, že přejdete na konkrétní téma služby Service Bus, které chcete publikovat na webu Azure Portal, a kliknutím na **zásady sdíleného přístupu** vygenerujete nový řetězec connecection specifické pro entitu.

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

## <a name="set-up-storage-queues-as-a-destination"></a>Nastavení front úložiště jako cíle

Chcete-li publikovat do fronty `endpointType` `storageQueue` úložiště, nastavte to to a zadejte:

* název fronty úložiště, do které publikujete.
* ConnectionString: Připojovací řetězec pro účet úložiště, ve které se nachází fronta úložiště.

    >[!NOTE]
    > Unline centra událostí, fronty služby Service Bus a témata sběrnice, připojovací řetězec používaný pro fronty úložiště není specifický pro entitu. Místo toho musí, ale připojovací řetězec pro účet úložiště.

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