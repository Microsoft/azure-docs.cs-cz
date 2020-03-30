---
title: Schéma události konfigurace aplikace Azure Event Grid Azure
description: Popisuje vlastnosti, které jsou k dispozici pro události konfigurace aplikací Azure s Azure Event Grid
services: event-grid
author: jimmyca
ms.service: event-grid
ms.topic: reference
ms.date: 05/30/2019
ms.author: jimmyca
ms.openlocfilehash: fe0274f723692eea3cfd25cc0e9e146b35dce2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66735778"
---
# <a name="azure-event-grid-event-schema-for-azure-app-configuration"></a>Schéma událostí Azure Event Grid pro konfiguraci aplikací Azure

Tento článek obsahuje vlastnosti a schéma pro události konfigurace aplikací Azure. Úvod do schémat událostí najdete v [tématu schéma událostí služby Azure Event Grid](event-schema.md).

Seznam ukázkových skriptů a kurzů najdete v tématu [Zdroj událostí konfigurace aplikace Azure](event-sources.md#app-configuration).

## <a name="available-event-types"></a>Dostupné typy událostí

Azure App Configuration vydává následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyHodnotaZměněno | Je aktivována při vytvoření nebo nahrazení hodnoty klíče. |
| Microsoft.AppConfiguration.KeyValueDeleted | Je aktivována při odstranění hodnoty klíče. |

## <a name="example-event"></a>Příklad události

Následující příklad ukazuje schéma události upravené hodnotou klíče: 

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

Schéma odstraněné události s hodnotou klíče je podobné: 

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

Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| téma | řetězec | Úplná cesta k prostředku ke zdroji události. Toto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid. |
| Předmět | řetězec | Cesta k předmětu události, kterou definuje vydavatel. |
| Eventtype | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| eventTime | řetězec | Čas, kdy je událost generována na základě času UTC zprostředkovatele. |
| id | řetězec | Jedinečný identifikátor události |
| data | objekt | Data událostí konfigurace aplikace. |
| dataVersion | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| key | řetězec | Klíč hodnoty klíče, která byla změněna nebo odstraněna. |
| label | řetězec | Popisek, pokud existuje, hodnoty klíče, která byla změněna nebo odstraněna. |
| Etag | řetězec | Pro `KeyValueModified` etag nové hodnoty klíče. Pro `KeyValueDeleted` etag klíč-hodnota, která byla odstraněna. |
 
## <a name="next-steps"></a>Další kroky

* Úvod do Služby Azure Event Grid najdete v tématu [Co je event grid?](overview.md)
* Další informace o vytvoření předplatného Služby Azure Event Grid najdete v [tématu schéma předplatného služby Event Grid](subscription-creation-schema.md).
* Úvod k práci s událostmi konfigurace aplikací Azure najdete v tématu [Route Azure App Configuration events – Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 