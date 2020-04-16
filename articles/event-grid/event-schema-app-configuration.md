---
title: Konfigurace aplikace Azure jako zdroj mřížky událostí
description: Tento článek popisuje, jak používat Azure App Configuration jako zdroj událostí Grid. Poskytuje schéma a odkazy na výukové programy a články s postupy.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: adb548ef8531698a2cb075fbc742bb20a02a434b
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393430"
---
# <a name="azure-app-configuration-as-an-event-grid-source"></a>Konfigurace aplikace Azure jako zdroj mřížky událostí
Tento článek obsahuje vlastnosti a schéma pro události konfigurace aplikací Azure. Úvod do schémat událostí najdete v [tématu schéma událostí služby Azure Event Grid](event-schema.md). Poskytuje také seznam rychlých spuštění a kurzů pro použití Azure App Configuration jako zdroj událostí.

## <a name="event-grid-event-schema"></a>Schéma události Mřížka událostí

### <a name="available-event-types"></a>Dostupné typy událostí

Azure App Configuration vydává následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.AppConfiguration.KeyHodnotaZměněno | Je aktivována při vytvoření nebo nahrazení hodnoty klíče. |
| Microsoft.AppConfiguration.KeyValueDeleted | Je aktivována při odstranění hodnoty klíče. |

### <a name="example-event"></a>Příklad události

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
 
### <a name="event-properties"></a>Vlastnosti události

Událost má následující data nejvyšší úrovně:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| téma | řetězec | Úplná cesta k prostředku ke zdroji události. Toto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid. |
| Předmět | řetězec | Cesta k předmětu události, kterou definuje vydavatel. |
| Eventtype | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| eventTime | řetězec | Čas, kdy je událost generována na základě času UTC zprostředkovatele. |
| ID | řetězec | Jedinečný identifikátor události |
| data | objekt | Data událostí konfigurace aplikace. |
| dataVersion | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| key | řetězec | Klíč hodnoty klíče, která byla změněna nebo odstraněna. |
| label | řetězec | Popisek, pokud existuje, hodnoty klíče, která byla změněna nebo odstraněna. |
| Etag | řetězec | Pro `KeyValueModified` etag nové hodnoty klíče. Pro `KeyValueDeleted` etag klíč-hodnota, která byla odstraněna. |

## <a name="tutorials-and-how-tos"></a>Kurzy a postupy

|Nadpis | Popis |
|---------|---------|
| [Reakce na události konfigurace aplikace Azure pomocí gridu událostí](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Přehled integrace konfigurace aplikací Azure s událostí gridu. |
| [Úvodní příručka: směrování událostí konfigurace aplikací Azure do vlastního webového koncového bodu pomocí azure cli](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ukazuje, jak pomocí azure cli k odeslání událostí konfigurace aplikací Azure na WebHook. |

## <a name="next-steps"></a>Další kroky

* Úvod do Služby Azure Event Grid najdete v tématu [Co je event grid?](overview.md)
* Další informace o vytvoření předplatného Služby Azure Event Grid najdete v [tématu schéma předplatného služby Event Grid](subscription-creation-schema.md).
* Úvod k práci s událostmi konfigurace aplikací Azure najdete v tématu [Route Azure App Configuration events – Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 