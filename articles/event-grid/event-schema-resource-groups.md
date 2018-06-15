---
title: Azure schématu události skupiny prostředků událostí mřížky
description: Popisuje vlastnosti, které jsou k dispozici pro události skupinu prostředků s Azure událostí mřížky
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 163c32bdb8a3fdc278404b9e26fdc3097797d16c
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34301946"
---
# <a name="azure-event-grid-event-schema-for-resource-groups"></a>Azure schématu události událostí mřížky pro skupiny prostředků

Tento článek poskytuje vlastnosti a schématu pro události skupiny prostředků. Úvod do schémata událostí, naleznete v části [schématu události mřížky událostí Azure](event-schema.md).

Skupiny prostředků a předplatná Azure emitování stejné typy událostí. Typy událostí se vztahují na změny v prostředky. Základní rozdíl je, že skupiny prostředků emitování události pro prostředky ve skupině prostředků a předplatná Azure emitování napříč předplatného události pro prostředky. 

## <a name="available-event-types"></a>Typy událostí k dispozici

Skupiny prostředků emitování události správy z Azure Resource Manager, například při vytvoření virtuálního počítače nebo je odstranit účet úložiště.

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.Resources.ResourceWriteSuccess | Vyvolá, když prostředek vytvořit nebo aktualizovat operace úspěšná. |
| Microsoft.Resources.ResourceWriteFailure | Vyvolá, když vytvoření prostředku nebo operace aktualizace se nezdaří. |
| Microsoft.Resources.ResourceWriteCancel | Vyvolá, když prostředek vytvořit nebo aktualizovat operace je zrušená. |
| Microsoft.Resources.ResourceDeleteSuccess | Vyvolá, když se podaří operaci odstranění prostředků. |
| Microsoft.Resources.ResourceDeleteFailure | Vyvolá, když se nezdaří operace odstranění prostředků. |
| Microsoft.Resources.ResourceDeleteCancel | Vyvolá, když je operaci odstranění prostředku došlo ke zrušení. Tato událost se stane, když nasazení šablony se zruší. |

## <a name="example-event"></a>Příklad událostí

Následující příklad ukazuje schéma prostředku vytvořit událost: 

```json
[
  {
    "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"{request-operation}",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
]
```

Schéma pro prostředek odstranit události je podobný:

```json
[{
  "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicApp0ecd6c02-2296-4d7c-9865-01532dc99c93",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2017-11-07T21:24:19.6959483Z",
  "id": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
  "data": {
    "authorization": "{azure_resource_manager_authorizations}",
    "claims": "{azure_resource_manager_claims}",
    "correlationId": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
    "httpRequest": "{request-operation}",
    "resourceProvider": "Microsoft.EventGrid",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "operationName": "Microsoft.EventGrid/eventSubscriptions/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47"
  },
  "dataVersion": "",
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
| data | objekt | Data události skupiny prostředků. |
| dataVersion | řetězec | Verze schématu datového objektu Vydavatel definuje verze schématu. |
| metadataVersion | řetězec | Verze schématu metadat události Událost mřížky definuje schéma vlastnosti nejvyšší úrovně. Událost mřížky poskytuje tuto hodnotu. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| Autorizace | řetězec | Požadovaná oprávnění pro operaci. |
| Deklarace identity | řetězec | Vlastnosti deklarace identity. |
| correlationId | řetězec | ID operace odstraňování potíží. |
| httpRequest | řetězec | Podrobnosti o operaci. |
| resourceProvider | řetězec | Zprostředkovatel prostředků provádění této operace. |
| resourceUri | řetězec | Identifikátor URI prostředku v operaci. |
| operationName | řetězec | Operace, která byla provedena. |
| status | řetězec | Stav operace. |
| subscriptionId | řetězec | ID předplatného prostředku. |
| TenantId | řetězec | ID klienta prostředku. |

## <a name="next-steps"></a>Další postup

* Úvod do Azure událostí mřížky, najdete v části [co je mřížky událostí?](overview.md)
* Další informace o vytváření předplatného služby Azure událostí mřížky, najdete v části [schématu odběru událostí mřížky](subscription-creation-schema.md).
