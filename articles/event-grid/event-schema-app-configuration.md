---
title: Schéma událostí Azure konfiguraci aplikací Azure mřížka událostí
description: Popisuje vlastnosti, které jsou k dispozici pro události konfigurace aplikace Azure pomocí služby Azure Event Grid
services: event-grid
author: jimmyca
ms.service: event-grid
ms.topic: reference
ms.date: 05/30/2019
ms.author: jimmyca
ms.openlocfilehash: fe0274f723692eea3cfd25cc0e9e146b35dce2ae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735778"
---
# <a name="azure-event-grid-event-schema-for-azure-app-configuration"></a>Schéma událostí Azure Event Grid pro konfiguraci aplikací Azure

Tento článek obsahuje vlastnosti a schéma pro události konfigurace aplikace Azure. Úvod do schémata událostí, naleznete v tématu [schéma událostí služby Azure Event Grid](event-schema.md).

Seznam ukázkových skriptů a kurzy, naleznete v tématu [zdroj událostí konfigurace aplikace pro Azure](event-sources.md#app-configuration).

## <a name="available-event-types"></a>Typy událostí k dispozici

Konfigurace aplikace pro Azure generuje následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyValueModified | Vyvoláno, když je vytvořen nebo výměny páru klíč hodnota. |
| Microsoft.AppConfiguration.KeyValueDeleted | Vyvoláno, když se odstraní páru klíč hodnota. |

## <a name="example-event"></a>Příklad události

Následující příklad ukazuje schématu upravené události klíč hodnota: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Schéma pro odstraněnou událost klíč hodnota se podobá: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueDeleted",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```
 
## <a name="event-properties"></a>Vlastnosti události

Událost má následující dat nejvyšší úrovně:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| topic | string | Úplné prostředků cesta ke zdroji události. Toto pole není zapisovatelná. Event gridu poskytuje tuto hodnotu. |
| subject | string | Vydavatel definované cesta předmět události. |
| eventType | string | Jeden z typů registrované události pro tento zdroj událostí. |
| eventTime | string | Vygenerování události podle času UTC poskytovatele. |
| id | string | Jedinečný identifikátor pro událost. |
| data | objekt | Data událostí konfigurace aplikace. |
| dataVersion | string | Verze schématu datového objektu Vydavatel Určuje verzi schématu. |
| metadataVersion | string | Verze schématu metadat události Event Grid definuje schéma vlastnosti nejvyšší úrovně. Event gridu poskytuje tuto hodnotu. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| key | string | Klíč klíč hodnota, která byla změněna nebo odstraněna. |
| label | string | Popisek, pokud je libovolná z klíč hodnota, která byla změněna nebo odstraněna. |
| etag | string | Pro `KeyValueModified` etag nový klíč hodnota. Pro `KeyValueDeleted` etag klíč hodnota, která byla odstraněna. |
 
## <a name="next-steps"></a>Další postup

* Úvod do služby Azure Event Grid najdete v tématu [novinky služby Event Grid?](overview.md)
* Další informace o vytváření předplatného služby Azure Event Grid najdete v tématu [schéma předplatného služby Event Grid](subscription-creation-schema.md).
* Úvod k práci s událostmi konfigurace aplikace pro Azure, najdete v článku [trasy konfiguraci aplikací Azure události – Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 