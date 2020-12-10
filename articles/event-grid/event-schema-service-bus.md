---
title: Azure Service Bus jako zdroj Event Grid
description: Popisuje vlastnosti, které jsou k dispozici pro Service Bus události s Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 34c6990c4e6e87304c457a5b2ca6459c404c8d9a
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97008108"
---
# <a name="azure-service-bus-as-an-event-grid-source"></a>Azure Service Bus jako zdroj Event Grid

Tento článek poskytuje vlastnosti a schéma pro události Service Bus. Úvod do schémat událostí najdete v tématu [Azure Event Grid schéma událostí](event-schema.md).

## <a name="event-grid-event-schema"></a>Schéma událostí služby Event Grid

### <a name="available-event-types"></a>Dostupné typy událostí

Service Bus emituje následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft. ServiceBus. ActiveMessagesAvailableWithNoListeners | Je aktivována, když ve frontě nebo předplatném jsou aktivní zprávy a neposlouchá žádný přijímač. |
| Microsoft. ServiceBus. DeadletterMessagesAvailableWithNoListener | Je aktivována, když jsou aktivní zprávy ve frontě nedoručených zpráv a žádné aktivní naslouchací procesy. |
| Microsoft. ServiceBus. ActiveMessagesAvailablePeriodicNotifications | Je aktivována pravidelně, pokud jsou aktivní zprávy ve frontě nebo předplatném, a to i v případě, že existují aktivní naslouchací procesy v dané konkrétní frontě nebo předplatném |
| Microsoft. ServiceBus. DeadletterMessagesAvailablePeriodicNotifications | Je pravidelně aktivována, pokud jsou v entitě nebo předplatném zprávy se zprávami o nedoručených zprávách, a to i v případě, že existují aktivní naslouchací procesy pro entitu nedoručených zpráv příslušné konkrétní fronty | 

### <a name="example-event"></a>Příklad události

#### <a name="active-messages-available-with-no-listeners"></a>Aktivní zprávy dostupné bez naslouchacího procesu

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

#### <a name="deadletter-messages-available-with-no-listener"></a>Nedoručené zprávy bez naslouchacího procesu

Schéma pro událost fronty nedoručených zpráv je podobné:

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

#### <a name="active-messages-available-periodic-notifications"></a>Aktivní zprávy jsou k dispozici periodická oznámení

```json
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailablePeriodicNotifications",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

#### <a name="deadletter-messages-available-periodic-notifications"></a>Zprávy o nedoručených zprávách k dispozici pravidelné oznámení

```json
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailablePeriodicNotifications",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
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
| téma | řetězec | Úplná cesta prostředku ke zdroji událostí. Do tohoto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid. |
| subject | řetězec | Cesta k předmětu události, kterou definuje vydavatel. |
| eventType | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| eventTime | řetězec | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| id | řetězec | Jedinečný identifikátor události |
| data | object | Data události služby Blob Storage. |
| dataVersion | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| namespaceName | řetězec | Obor názvů Service Bus prostředek existuje v. |
| requestUri | řetězec | Identifikátor URI pro konkrétní frontu nebo odběr, který vyvolal událost. |
| Objektu | řetězec | Typ Service Bus událostí generování entit (front nebo odběr). |
| Proměnné QueueName | řetězec | Fronta s aktivními zprávami při přihlášení k odběru fronty. Hodnota null, pokud používáte témata nebo odběry. |
| téma | řetězec | Téma, ke kterému patří Service Bus předplatné s aktivními zprávami. Pokud používáte frontu, hodnota null. |
| subscriptionName | řetězec | Předplatné Service Bus s aktivními zprávami. Pokud používáte frontu, hodnota null. |

## <a name="tutorials-and-how-tos"></a>Kurzy a postupy
|Nadpis  |Popis  |
|---------|---------|
| [Kurz: Azure Service Bus pro Azure Event Grid příklady integrace](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid posílá zprávy z Service Bus tématu do aplikace Function App a Logic Apps. |
| [Azure Service Bus pro Event Grid integraci](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Přehled integrace Service Bus s Event Grid |

## <a name="next-steps"></a>Další kroky

* Úvod do Azure Event Grid najdete v tématu [co je Event Grid?](overview.md)
* Další informace o vytváření předplatného Azure Event Grid najdete v tématu [schéma předplatného Event Grid](subscription-creation-schema.md).
* Podrobnosti o použití Azure Event Grid s Service Bus najdete v tématu [Přehled integrace Service Bus do Event Grid](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md).
* Zkuste [přijmout Service Bus události pomocí funkcí nebo Logic Apps](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).
