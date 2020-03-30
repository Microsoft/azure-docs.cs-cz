---
title: Schéma událostí centra event gridu Azure
description: Popisuje vlastnosti, které jsou k dispozici pro události centra událostí s Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 9c0113687d27bf43375f298057129a5594ec0a06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561824"
---
# <a name="azure-event-grid-event-schema-for-event-hubs"></a>Schéma událostí Azure Event Grid pro centra událostí

Tento článek obsahuje vlastnosti a schéma pro události centra událostí.Úvod do schémat událostí najdete v [tématu schéma událostí služby Azure Event Grid](event-schema.md).

Seznam ukázkových skriptů a kurzů naleznete v tématu [Zdroj událostí Centra událostí](event-sources.md#event-hubs).

### <a name="available-event-types"></a>Dostupné typy událostí

Centra událostí vyzařují typ události **Microsoft.EventHub.CaptureFileCreated** při vytvoření souboru sběru.

## <a name="example-event"></a>Příklad události

Tato ukázková událost zobrazuje schéma události centra událostí, která byla vyvolána, když funkce sběru ukládá soubor: 

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        },
        "dataVersion": "",
        "metadataVersion": "1"
    }
]
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
| data | objekt | Data událostí centra událostí. |
| dataVersion | řetězec | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | řetězec | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| adresa souboru | řetězec | Cesta k souboru sběru. |
| Filetype | řetězec | Typ souboru sběru. |
| Partitionid | řetězec | ID úlomek. |
| sizeInBytes | celé číslo | Velikost souboru. |
| eventCount | celé číslo | Počet událostí v souboru. |
| prvníSequenceNumber | celé číslo | Nejmenší pořadové číslo z fronty. |
| lastSequenceNumber | celé číslo | Poslední pořadové číslo z fronty. |
| firstEnqueueTime | řetězec | Poprvé z fronty. |
| lastEnqueueTime | řetězec | Poslední čas z fronty. |

## <a name="next-steps"></a>Další kroky

* Úvod do Služby Azure Event Grid najdete v tématu [Co je event grid?](overview.md)
* Další informace o vytvoření předplatného Služby Azure Event Grid najdete v [tématu schéma předplatného služby Event Grid](subscription-creation-schema.md).
* Informace o zpracování událostí centra událostí najdete v [tématu streamování velkých objemů dat do datového skladu](event-grid-event-hubs-integration.md).