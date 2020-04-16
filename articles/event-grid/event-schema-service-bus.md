---
title: Azure Service Bus jako zdroj gridu událostí
description: Popisuje vlastnosti, které jsou k dispozici pro události service bus s Azure Event Grid
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: 141a0e96071014dc3705d30f72b1a9257737298a
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393242"
---
# <a name="azure-service-bus-as-an-event-grid-source"></a>Azure Service Bus jako zdroj gridu událostí

Tento článek obsahuje vlastnosti a schéma událostí service bus.Úvod do schémat událostí najdete v [tématu schéma událostí služby Azure Event Grid](event-schema.md).

## <a name="event-grid-event-schema"></a>Schéma události Mřížka událostí

### <a name="available-event-types"></a>Dostupné typy událostí

Service Bus vydává následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesKavailableWithNoListeners | Je aktivována, pokud jsou aktivní zprávy ve frontě nebo odběr a žádné příjemce naslouchání. |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | Je aktivována, pokud jsou aktivní zprávy ve frontě nedoručených zpráv a žádné aktivní naslouchací procesy. |

### <a name="example-event"></a>Příklad události

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

### <a name="event-properties"></a>Vlastnosti události

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

## <a name="tutorials-and-how-tos"></a>Kurzy a postupy
|Nadpis  |Popis  |
|---------|---------|
| [Kurz: Příklady integrace Azure Service Bus do Azure Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid odesílá zprávy z tématu Service Bus do funkční aplikace a aplikace logiky. |
| [Integrace Azure Service Bus do gridu událostí](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Přehled integrace sběrnice Service Bus s událostí Grid. |

## <a name="next-steps"></a>Další kroky

* Úvod do Služby Azure Event Grid najdete v tématu [Co je event grid?](overview.md)
* Další informace o vytvoření předplatného Služby Azure Event Grid najdete v [tématu schéma předplatného služby Event Grid](subscription-creation-schema.md).
* Podrobnosti o používání Azure Event Grid s Service Bus najdete v tématu [Service Bus to Event Grid přehled integrace](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md).
* Zkuste [přijímat události service bus s funkcemi nebo logic apps](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).
