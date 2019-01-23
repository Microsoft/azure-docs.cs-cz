---
title: Schéma událostí události rozbočovače Azure Event Grid
description: Popisuje vlastnosti, které jsou k dispozici pro události centra událostí pomocí služby Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 9c0113687d27bf43375f298057129a5594ec0a06
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54475403"
---
# <a name="azure-event-grid-event-schema-for-event-hubs"></a>Schéma událostí Azure Event Grid pro službu event hubs

Tento článek obsahuje vlastnosti a schéma pro události event hubs. Úvod do schémata událostí, naleznete v tématu [schéma událostí služby Azure Event Grid](event-schema.md).

Seznam ukázkových skriptů a kurzy, naleznete v tématu [zdroj událostí služby Event Hubs](event-sources.md#event-hubs).

### <a name="available-event-types"></a>Typy událostí k dispozici

Generuje Služba Event Hubs **Microsoft.EventHub.CaptureFileCreated** typ události, když se vytvoří zachytávací soubor.

## <a name="example-event"></a>Příklad události

Událost vzorku pro tento ukazuje schématu události event hubs vyvolá, když funkci capture uloží soubor: 

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

Událost má následující dat nejvyšší úrovně:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| téma | řetězec | Úplné prostředků cesta ke zdroji události. Toto pole není zapisovatelná. Event gridu poskytuje tuto hodnotu. |
| předmět | řetězec | Vydavatel definované cesta předmět události. |
| eventType | řetězec | Jeden z typů registrované události pro tento zdroj událostí. |
| čas události | řetězec | Vygenerování události podle času UTC poskytovatele. |
| id | řetězec | Jedinečný identifikátor pro událost. |
| data | objekt | Data události centra událostí. |
| dataVersion | řetězec | Verze schématu datového objektu Vydavatel Určuje verzi schématu. |
| metadataVersion | řetězec | Verze schématu metadat události Event Grid definuje schéma vlastnosti nejvyšší úrovně. Event gridu poskytuje tuto hodnotu. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| fileUrl | řetězec | Cesta k zachytávací soubor. |
| fileType | řetězec | Typ souboru zachytávací soubor. |
| ID oddílu | řetězec | ID horizontálního oddílu. |
| sizeInBytes | integer | Velikost souboru. |
| eventCount | integer | Počet událostí v souboru. |
| firstSequenceNumber | integer | Nejmenší pořadové číslo z fronty. |
| lastSequenceNumber | integer | Poslední pořadové číslo z fronty. |
| firstEnqueueTime | řetězec | Při prvním z fronty. |
| lastEnqueueTime | řetězec | Čas posledního z fronty. |

## <a name="next-steps"></a>Další postup

* Úvod do služby Azure Event Grid najdete v tématu [novinky služby Event Grid?](overview.md)
* Další informace o vytváření předplatného služby Azure Event Grid najdete v tématu [schéma předplatného služby Event Grid](subscription-creation-schema.md).
* Informace o zpracování událostí event hubs najdete v tématu [Stream velkých objemů dat do datového skladu](event-grid-event-hubs-integration.md).