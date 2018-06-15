---
title: Azure schématu události událostí mřížky Service Bus
description: Popisuje vlastnosti, které jsou k dispozici pro události služby Service Bus s Azure událostí mřížky
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: reference
ms.date: 02/21/2018
ms.author: babanisa
ms.openlocfilehash: 991679eeb0f7c98606133750b193a5895f39178f
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34303313"
---
# <a name="azure-event-grid-event-schema-for-service-bus"></a>Azure schématu události událostí mřížky pro Service Bus

Tento článek poskytuje vlastnosti a schématu pro události služby Service Bus. Úvod do schémata událostí, naleznete v části [schématu události mřížky událostí Azure](event-schema.md).

## <a name="available-event-types"></a>Typy událostí k dispozici

Service Bus vysílá následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners | Vyvolá, když existují aktivní zprávy fronty nebo předplatného a žádné příjemci naslouchá. |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | Vyvolá, když existují aktivní zprávy do fronty nedoručených zpráv a žádné aktivní naslouchací procesy. |

## <a name="example-event"></a>Příklad událostí

Následující příklad ukazuje schéma active zprávy s žádné naslouchací procesy událostí:

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

Schéma pro událost fronty nedoručených zpráv je podobný:

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
| Téma | řetězec | Úplné prostředků cesta ke zdroji událostí. Toto pole není možné zapisovat. Událost mřížky poskytuje tuto hodnotu. |
| Předmět | řetězec | Cesta definované vydavatele události předmět. |
| Typ události | řetězec | Jeden z typů událostí registrovaných pro tento zdroj událostí. |
| eventTime | řetězec | Čas, který se vygeneruje událost založené na čas UTC poskytovatele. |
| id | řetězec | Jedinečný identifikátor pro událost. |
| data | objekt | Data události úložiště objektů BLOB. |
| dataVersion | řetězec | Verze schématu datového objektu Vydavatel definuje verze schématu. |
| metadataVersion | řetězec | Verze schématu metadat události Událost mřížky definuje schéma vlastnosti nejvyšší úrovně. Událost mřížky poskytuje tuto hodnotu. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| nameSpaceName | řetězec | Obor názvů sběrnice prostředku v existuje. |
| requestUri | řetězec | Identifikátor URI do konkrétní fronty nebo generování událost odběru. |
| Typ entity | řetězec | Typ entity služby sběrnice generování událostí (fronty nebo předplatné). |
| queueName | řetězec | Fronta s active zprávy, pokud se přihlášení k odběru do fronty. Hodnotu null, pokud pomocí témata nebo předplatných. |
| TopicName | řetězec | Téma patří předplatné služby Service Bus s active zprávy. Hodnota null, pokud pomocí fronty. |
| Název_předplatného | řetězec | Předplatné služby Service Bus s active zprávy. Hodnota null, pokud pomocí fronty. |

## <a name="next-steps"></a>Další postup

* Úvod do Azure událostí mřížky, najdete v části [co je mřížky událostí?](overview.md)
* Další informace o vytváření předplatného služby Azure událostí mřížky, najdete v části [schématu odběru událostí mřížky](subscription-creation-schema.md).
* Podrobnosti o používání mřížky událostí Azure se službou Service Bus najdete v tématu [služba Service Bus Přehled integrace událostí mřížky](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md).
* Zkuste [příjem událostí služby Service Bus pomocí funkcí nebo Logic Apps](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).
