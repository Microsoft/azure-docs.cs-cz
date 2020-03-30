---
title: Schéma událostí služby Azure Event Grid Service Bus
description: Popisuje vlastnosti, které jsou k dispozici pro události service bus s Azure Event Grid
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: babanisa
ms.openlocfilehash: f44d2c1c5be6ac895b6f5ea9feca29c0f8ed09f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561757"
---
# <a name="azure-event-grid-event-schema-for-service-bus"></a>Schéma událostí sítě Azure pro service bus

Tento článek obsahuje vlastnosti a schéma událostí service bus.Úvod do schémat událostí najdete v [tématu schéma událostí služby Azure Event Grid](event-schema.md).

Seznam ukázkových skriptů a kurzů naleznete v tématu [Service Bus event source](event-sources.md#service-bus).

## <a name="available-event-types"></a>Dostupné typy událostí

Service Bus vydává následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesKavailableWithNoListeners | Je aktivována, pokud jsou aktivní zprávy ve frontě nebo odběr a žádné příjemce naslouchání. |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | Je aktivována, pokud jsou aktivní zprávy ve frontě nedoručených zpráv a žádné aktivní naslouchací procesy. |

## <a name="example-event"></a>Příklad události

Následující příklad ukazuje schéma aktivních zpráv bez události posluchačů:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Schéma události fronty nedoručených zpráv je podobné:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Vlastnosti události

Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| téma | řetězec | Úplná cesta k prostředku ke zdroji události. Toto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid. |
| Předmět | řetězec | Cesta k předmětu události, kterou definuje vydavatel. |
| Eventtype | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| eventTime | řetězec | Čas, kdy je událost generována na základě času UTC zprostředkovatele. |
| id | řetězec | Jedinečný identifikátor události |
| data | objekt | Data událostí úložiště objektů blob. |
| dataVersion | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| Namespacename | řetězec | Obor názvů služby Service Bus, ve kterých prostředek existuje. |
| Requesturi | řetězec | Identifikátor URI pro konkrétní frontu nebo odběr vyzařující událost. |
| entityTyp | řetězec | Typ entity Service Bus vyzařující události (fronty nebo odběr). |
| název_fronty | řetězec | Fronta s aktivními zprávami, pokud se přihlásíte k odběru fronty. Hodnota null, pokud používáte témata / odběry. |
| topicName | řetězec | Téma, do které patří předplatné service bus u aktivních zpráv. Hodnota null při použití fronty. |
| subscriptionName | řetězec | Předplatné service bus s aktivními zprávami. Hodnota null při použití fronty. |

## <a name="next-steps"></a>Další kroky

* Úvod do Služby Azure Event Grid najdete v tématu [Co je event grid?](overview.md)
* Další informace o vytvoření předplatného Služby Azure Event Grid najdete v [tématu schéma předplatného služby Event Grid](subscription-creation-schema.md).
* Podrobnosti o používání Azure Event Grid s Service Bus najdete v tématu [Service Bus to Event Grid přehled integrace](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md).
* Zkuste [přijímat události service bus s funkcemi nebo logic apps](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).
