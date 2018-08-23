---
title: Schéma událostí služby Azure Event Grid služby Service Bus
description: Popisuje vlastnosti, které jsou k dispozici pro události služby Service Bus pomocí služby Azure Event Grid
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: reference
ms.date: 08/17/2018
ms.author: babanisa
ms.openlocfilehash: afb85f20c49821ca98e078791730a3376198e9e5
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/17/2018
ms.locfileid: "42054025"
---
# <a name="azure-event-grid-event-schema-for-service-bus"></a>Schéma událostí Azure Event Grid pro Service Bus

Tento článek obsahuje vlastnosti a schéma pro události služby Service Bus. Úvod do schémata událostí, naleznete v tématu [schéma událostí služby Azure Event Grid](event-schema.md).

Seznam ukázkových skriptů a kurzy, naleznete v tématu [zdroj události služby Service Bus](event-sources.md#service-bus).

## <a name="available-event-types"></a>Typy událostí k dispozici

Služba Service Bus vysílá následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners | Vyvoláno, když existují aktivní zprávy ve frontě nebo odběru, ale žádný příjemce nenaslouchá. |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | Vyvoláno, když existují aktivní zprávy do fronty nedoručených zpráv a žádné aktivní naslouchací procesy. |

## <a name="example-event"></a>Příklad události

Následující příklad ukazuje schématu aktivní zprávy s žádné naslouchacích procesů událostí:

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

Schéma pro událost fronty nedoručených zpráv se podobá:

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

Událost má následující dat nejvyšší úrovně:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| téma | řetězec | Úplné prostředků cesta ke zdroji události. Toto pole není zapisovatelná. Event gridu poskytuje tuto hodnotu. |
| Předmět | řetězec | Vydavatel definované cesta předmět události. |
| Typ události | řetězec | Jeden z typů registrované události pro tento zdroj událostí. |
| čas události | řetězec | Vygenerování události podle času UTC poskytovatele. |
| id | řetězec | Jedinečný identifikátor pro událost. |
| data | objekt | Data události úložiště objektů BLOB. |
| dataVersion | řetězec | Verze schématu datového objektu Vydavatel Určuje verzi schématu. |
| verze metadataVersion | řetězec | Verze schématu metadat události Event Grid definuje schéma vlastnosti nejvyšší úrovně. Event gridu poskytuje tuto hodnotu. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| namespaceName | řetězec | V existuje prostředek oboru názvů Service Bus. |
| requestUri | řetězec | Identifikátor URI, do konkrétní fronty nebo předplatné, které vysílá události. |
| Typ entity | řetězec | Typ entity služby Service Bus generování událostí (fronty nebo odběru). |
| queueName | řetězec | Frontu s aktivní zprávy, pokud se přihlášení k odběru do fronty. Hodnota null, pokud používáte témata a odběry. |
| topicName | řetězec | V tématu odběr služby Service Bus s aktivní zprávy patří. Hodnota null, pokud používáte fronty. |
| subscriptionName | řetězec | Odběr služby Service Bus s aktivní zprávy. Hodnota null, pokud používáte fronty. |

## <a name="next-steps"></a>Další postup

* Úvod do služby Azure Event Grid najdete v tématu [novinky služby Event Grid?](overview.md)
* Další informace o vytváření předplatného služby Azure Event Grid najdete v tématu [schéma předplatného služby Event Grid](subscription-creation-schema.md).
* Podrobnosti o používání služby Azure Event Grid se službou Service Bus, najdete v článku [Service Bus do služby Event Grid Přehled integrace](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md).
* Zkuste [přijímat události služby Service Bus s Functions nebo Logic Apps](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).
