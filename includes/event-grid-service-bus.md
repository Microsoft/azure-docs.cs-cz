---
title: zahrnout soubor
description: zahrnout soubor
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 02/04/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 49e35687820679a1c06cf19e7a26b3b04a1e1318
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100363236"
---
## <a name="available-event-types"></a>Dostupné typy událostí

Service Bus emituje následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft. ServiceBus. ActiveMessagesAvailableWithNoListeners | Je aktivována, když ve frontě nebo předplatném jsou aktivní zprávy a neposlouchá žádný přijímač. |
| Microsoft. ServiceBus. DeadletterMessagesAvailableWithNoListener | Je aktivována, když jsou aktivní zprávy ve frontě nedoručených zpráv a žádné aktivní naslouchací procesy. |
| Microsoft. ServiceBus. ActiveMessagesAvailablePeriodicNotifications | Je aktivována pravidelně, pokud jsou aktivní zprávy ve frontě nebo předplatném, a to i v případě, že existují aktivní naslouchací procesy v dané konkrétní frontě nebo předplatném |
| Microsoft. ServiceBus. DeadletterMessagesAvailablePeriodicNotifications | Je pravidelně aktivována, pokud se v entitě nebo předplatném nedoručené společnosti nacházejí zprávy, a to i v případě, že existují aktivní naslouchací procesy pro nedoručenou entitu dané konkrétní fronty nebo předplatného. | 

## <a name="example-event"></a>Příklad události

# <a name="event-grid-event-schema"></a>[Schéma událostí služby Event Grid](#tab/event-grid-event-schema)

### <a name="active-messages-available-with-no-listeners"></a>Aktivní zprávy dostupné bez naslouchacího procesu
Tato událost se generuje v případě, že ve frontě nebo odběru máte aktivní zprávy, ale žádný příjemce nenaslouchá.

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

Schéma pro událost fronty nedoručených zpráv je podobné. Získáte alespoň jednu událost pro frontu nedoručených zpráv, která obsahuje zprávy a žádné aktivní přijímače.

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
Tato událost se generuje pravidelně, pokud máte aktivní zprávy o konkrétní frontě nebo předplatném, a to i v případě, že v dané konkrétní frontě nebo předplatném existují aktivní naslouchací procesy.

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
Tato událost se generuje pravidelně, pokud máte zprávy nedoručené pošty v konkrétní frontě nebo předplatném, a to i v případě, že existují aktivní naslouchací procesy pro entitu nedoručených zpráv dané konkrétní frontě nebo

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

# <a name="cloud-event-schema"></a>[Schéma cloudových událostí](#tab/cloud-event-schema)

### <a name="active-messages-available-with-no-listeners"></a>Aktivní zprávy dostupné bez naslouchacího procesu
Tato událost se generuje v případě, že ve frontě nebo odběru máte aktivní zprávy, ale žádný příjemce nenaslouchá.

```json
[{
  "source": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "type": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "time": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "specversion": "1.0"
}]
```

#### <a name="deadletter-messages-available-with-no-listener"></a>Nedoručené zprávy bez naslouchacího procesu

Schéma pro událost fronty nedoručených zpráv je podobné. Získáte alespoň jednu událost pro frontu nedoručených zpráv, která obsahuje zprávy a žádné aktivní přijímače.

```json
[{
  "source": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "type": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "time": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "specversion": "1.0"
}]
```

#### <a name="active-messages-available-periodic-notifications"></a>Aktivní zprávy jsou k dispozici periodická oznámení
Tato událost se generuje pravidelně, pokud máte aktivní zprávy o konkrétní frontě nebo předplatném, a to i v případě, že v dané konkrétní frontě nebo předplatném existují aktivní naslouchací procesy.

```json
[{
  "source": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "type": "Microsoft.ServiceBus.ActiveMessagesAvailablePeriodicNotifications",
  "time": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "specversion": "1.0"
}]
```

#### <a name="deadletter-messages-available-periodic-notifications"></a>Zprávy o nedoručených zprávách k dispozici pravidelné oznámení
Tato událost se generuje pravidelně, pokud máte zprávy nedoručené pošty v konkrétní frontě nebo předplatném, a to i v případě, že existují aktivní naslouchací procesy pro entitu nedoručených zpráv dané konkrétní frontě nebo

```json
[{
  "source": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "type": "Microsoft.ServiceBus.DeadletterMessagesAvailablePeriodicNotifications",
  "time": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "specversion": "1.0"
}]
```

---



### <a name="event-properties"></a>Vlastnosti události

# <a name="event-grid-event-schema"></a>[Schéma událostí služby Event Grid](#tab/event-grid-event-schema)
Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| `topic` | řetězec | Úplná cesta prostředku ke zdroji událostí. Do tohoto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid. |
| `subject` | řetězec | Cesta k předmětu události, kterou definuje vydavatel. |
| `eventType` | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| `eventTime` | řetězec | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| `id` | řetězec | Jedinečný identifikátor události |
| `data` | object | Data události služby Blob Storage. |
| `dataVersion` | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| `metadataVersion` | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

# <a name="cloud-event-schema"></a>[Schéma cloudových událostí](#tab/cloud-event-schema)

Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| `source` | řetězec | Úplná cesta prostředku ke zdroji událostí. Do tohoto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid. |
| `subject` | řetězec | Cesta k předmětu události, kterou definuje vydavatel. |
| `type` | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| `time` | řetězec | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| `id` | řetězec | Jedinečný identifikátor události |
| `data` | object | Data události služby Blob Storage. |
| `specversion` | řetězec | Verze specifikace schématu CloudEvents |

---

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| `namespaceName` | řetězec | Obor názvů Service Bus prostředek existuje v. |
| `requestUri` | řetězec | Identifikátor URI pro konkrétní frontu nebo odběr, který vyvolal událost. |
| `entityType` | řetězec | Typ Service Bus událostí generování entit (front nebo odběr). |
| `queueName` | řetězec | Fronta s aktivními zprávami při přihlášení k odběru fronty. Hodnota null, pokud používáte témata nebo odběry. |
| `topicName` | řetězec | Téma, ke kterému patří Service Bus předplatné s aktivními zprávami. Pokud používáte frontu, hodnota null. |
| `subscriptionName` | řetězec | Předplatné Service Bus s aktivními zprávami. Pokud používáte frontu, hodnota null. |