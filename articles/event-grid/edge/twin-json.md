---
title: Modul s dvojitou Azure Event Grid IoT Edge | Microsoft Docs
description: Konfigurace prostřednictvím modulu je nevlákenná.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: f39d22fe58d4375b3b68bacd237c1b200328c4b1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171325"
---
# <a name="module-twin-json-schema-azure-event-grid"></a>Modul s dvojím zápisem JSON (Azure Event Grid)

Event Grid v IoT Edge integruje s IoT Edge ekosystémem a podporuje vytváření témat a odběrů prostřednictvím nevlákenných modulů. Oznamuje také aktuální stav všech témat a odběrů událostí pro hlášené vlastnosti v modulu s dvojitou platností.

> [!WARNING]
> Z důvodu omezení v IoT Edge ekosystému jsou všechny prvky pole v následujícím příkladu JSON zakódované jako řetězce JSON. Podívejte `EventSubscription.Filter.EventTypes` `EventSubscription.Filter.AdvancedFilters` se na klíče a v následujícím příkladu.

## <a name="desired-properties-json"></a>Formát JSON požadovaných vlastností

* Hodnota každé dvojice klíč-hodnota v části témata má přesně stejné schéma JSON, které se používá pro `Topic.Properties` rozhraní API při vytváření témat.
* Hodnota každé dvojice klíč-hodnota v oddílu **EventSubscriptions** má přesně stejné schéma JSON, které se používá pro `EventSubscription.Properties` rozhraní API při vytváření témat.
* Chcete-li odstranit téma, nastavte jeho hodnotu na `null` v požadovaných vlastnostech.
* Odstranění odběrů událostí prostřednictvím požadovaných vlastností není podporováno.

```json
{
    "topics": {
        "twinTopic1": {},
        "twinTopic2": {
            "inputSchema": "customEventSchema"
        }
    },
    "eventSubscriptions": {
        "twinTopic1WebhookSub": {
            "topic": "twinTopic1",
            "retryPolicy": {
                "eventExpiryInMinutes": 120,
                "maxDeliveryAttempts": 30
            },
            "destination": {
                "endpointType": "WebHook",
                "properties": {
                    "endpointUrl": "https://localhost:4438"
                }
            },
            "filter": {
                "subjectBeginsWith": "^",
                "subjectEndsWith": "$",
                "isSubjectCaseSensitive": false,
                "includedEventTypes": "[\"et1\",\"et2\"]",
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"data.b\"},{\"values\":[\"\\\"\",\"c\"],    \"operatorType\":\"StringContains\",\"key\":\"data.s\"}]"
            }
        },
        "twinTopic2EdgeHubSub": {
            "topic": "twinTopic2",
            "deliveryPolicy": {
                "approxBatchSizeInBytes": 200000,
                "maxEventsPerBatch": 25
            },
            "destination": {
                "endpointType": "EdgeHub",
                "properties": {
                    "outputName": "twinTopic2EdgeHubSub"
                }
            },
            "filter": {
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"dAt\\\"A.a\"},{\"values\":[\"\\\"\",    \"c\"],\"operatorType\":\"StringContains\",\"key\":\"dAt\\\"A.a\"}]"
            }
        }
    }
}
```

## <a name="reported-properties-json"></a>KÓD JSON pro hlášené vlastnosti

Oddíl nahlášených vlastností v modulu s dvojitou platností obsahuje následující informace:

* Sada témat a předplatných, která existují v obchodě s modulem
* Jakékoli chyby, ke kterým došlo při vytváření požadovaných témat nebo odběrů událostí
* Jakékoli chyby při spuštění (například analýza JSON požadovaných vlastností se nezdařila)

```json
{
    "topics": {
        "twinTopic1": {},
        "twinTopic2": {
            "inputSchema": "customEventSchema"
        }
    },
    "eventSubscriptions": {
        "twinTopic1WebhookSub": {
            "topic": "twinTopic1",
            "retryPolicy": {
                "eventExpiryInMinutes": 120,
                "maxDeliveryAttempts": 30
            },
            "destination": {
                "endpointType": "WebHook",
                "properties": {
                    "endpointUrl": "https://localhost:4438"
                }
            },
            "filter": {
                "subjectBeginsWith": "^",
                "subjectEndsWith": "$",
                "isSubjectCaseSensitive": false,
                "includedEventTypes": "[\"et1\",\"et2\"]",
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"data.b\"},{\"values\":[\"\\\"\",\"c\"],    \"operatorType\":\"StringContains\",\"key\":\"data.s\"}]"
            }
        },
        "twinTopic2EdgeHubSub": {
            "topic": "twinTopic2",
            "deliveryPolicy": {
                "approxBatchSizeInBytes": 200000,
                "maxEventsPerBatch": 25
            },
            "destination": {
                "endpointType": "EdgeHub",
                "properties": {
                    "outputName": "twinTopic2EdgeHubSub"
                }
            },
            "filter": {
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"dAt\\\"A.a\"},{\"values\":[\"\\\"\",    \"c\"],\"operatorType\":\"StringContains\",\"key\":\"dAt\\\"A.a\"}]"
            }
        }
    },
    "errors": {
        "bootupMessage": "",
        "bootupException": "",
        "topics": {},
        "eventSubscriptions": {
            "twinTopic1EventGridUserTopicSub": "HttpStatusCode='BadRequest' ErrorCode='InvalidDestination' Message='EventSubscription.Properties.Destination failed validation. Reason: EndpointUrl must target the /api/events API of Azure Event Grid in the cloud..'"
        }
    }
}
```
