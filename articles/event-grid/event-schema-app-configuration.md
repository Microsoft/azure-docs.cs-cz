---
title: Konfigurace aplikace Azure jako zdroj Event Grid
description: Tento článek popisuje, jak používat konfiguraci aplikací Azure jako zdroj události Event Grid. Poskytuje schéma a odkazy na články týkající se kurzu a postupů.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: bdd077c291bd1e1c441217740daf39c8bcaad732
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86116994"
---
# <a name="azure-app-configuration-as-an-event-grid-source"></a>Konfigurace aplikace Azure jako zdroj Event Grid
Tento článek popisuje vlastnosti a schéma pro události konfigurace aplikací Azure. Úvod do schémat událostí najdete v tématu [Azure Event Grid schéma událostí](event-schema.md). Nabízí také seznam rychlých startů a kurzů pro použití konfigurace aplikací Azure jako zdroje událostí.

## <a name="event-grid-event-schema"></a>Schéma událostí služby Event Grid

### <a name="available-event-types"></a>Dostupné typy událostí

Konfigurace aplikace Azure emituje následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft. AppConfiguration. KeyValueModified | Je aktivována, když je vytvořena nebo nahrazena klíčová hodnota. |
| Microsoft. AppConfiguration. KeyValueDeleted | Vyvolá se, když se odstraní hodnota klíče. |

### <a name="example-event"></a>Příklad události

Následující příklad ukazuje schéma události upravované hodnoty klíč-hodnota: 

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

Schéma pro událost odstranění klíč-hodnota je podobné: 

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

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| téma | řetězec | Úplná cesta prostředku ke zdroji událostí. Do tohoto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid. |
| subject | řetězec | Cesta k předmětu události, kterou definuje vydavatel. |
| eventType | řetězec | Jeden z registrovaných typů události pro tento zdroj události. |
| eventTime | řetězec | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| ID | řetězec | Jedinečný identifikátor události |
| data | object | Data události konfigurace aplikace |
| dataVersion | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Description |
| -------- | ---- | ----------- |
| key | řetězec | Klíč hodnoty klíč-hodnota, která byla upravena nebo odstraněna. |
| label | řetězec | Popisek (pokud existuje) hodnoty klíč-hodnota, která byla upravena nebo odstraněna. |
| značk | řetězec | Pro `KeyValueModified` ETag nového klíč-hodnota. Pro `KeyValueDeleted` značku ETag hodnoty klíč-hodnota, která byla odstraněna. |

## <a name="tutorials-and-how-tos"></a>Kurzy a postupy

|Nadpis | Popis |
|---------|---------|
| [Reakce na události konfigurace aplikace Azure pomocí Event Grid](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Přehled integrace konfigurace aplikace Azure s Event Grid. |
| [Rychlý Start: směrování událostí konfigurace aplikací Azure do vlastního webového koncového bodu pomocí Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Ukazuje, jak používat Azure CLI k posílání událostí konfigurace aplikace Azure do Webhooku. |

## <a name="next-steps"></a>Další kroky

* Úvod do Azure Event Grid najdete v tématu [co je Event Grid?](overview.md)
* Další informace o vytváření předplatného Azure Event Grid najdete v tématu [schéma předplatného Event Grid](subscription-creation-schema.md).
* Úvod k práci s událostmi konfigurace aplikací Azure najdete v tématu [Směrování událostí konfigurace aplikací Azure – Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json). 