---
title: Schématu centra událostí Azure událostí mřížky události
description: Popisuje vlastnosti, které jsou k dispozici pro události centra událostí s Azure událostí mřížky
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 6fac70a8837245e6d60a5a61db8a22ab718d4b49
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="azure-event-grid-event-schema-for-event-hubs"></a>Azure schématu události událostí mřížky pro event hubs

Tento článek poskytuje vlastnosti a schématu pro události centra událostí. Úvod do schémata událostí, naleznete v části [schématu události mřížky událostí Azure](event-schema.md).

### <a name="available-event-types"></a>Typy událostí k dispozici

Služba Event Hubs vysílá **Microsoft.EventHub.CaptureFileCreated** typ události při vytváření souboru zachycení.

## <a name="example-event"></a>Příklad událostí

Tato ukázka událost ukazuje schéma událost centra událostí vyvolá, když funkci zachycení ukládá do souboru: 

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
| Téma | řetězec | Úplné prostředků cesta ke zdroji událostí. Toto pole není možné zapisovat. Událost mřížky poskytuje tuto hodnotu. |
| Předmět | řetězec | Cesta definované vydavatele události předmět. |
| Typ události | řetězec | Jeden z typů událostí registrovaných pro tento zdroj událostí. |
| eventTime | řetězec | Čas, který se vygeneruje událost založené na čas UTC poskytovatele. |
| id | řetězec | Jedinečný identifikátor pro událost. |
| data | objekt | Data události centra událostí. |
| dataVersion | řetězec | Verze schématu datového objektu Vydavatel definuje verze schématu. |
| metadataVersion | řetězec | Verze schématu metadat události Událost mřížky definuje schéma vlastnosti nejvyšší úrovně. Událost mřížky poskytuje tuto hodnotu. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| fileUrl | řetězec | Cesta k souboru zachycení. |
| Typ souboru | řetězec | Typ souboru zachycení. |
| ID oddílu | řetězec | ID horizontálního oddílu. |
| sizeInBytes | integer | Velikost souboru. |
| eventCount | integer | Počet událostí v souboru. |
| firstSequenceNumber | integer | Nejnižší pořadové číslo z fronty. |
| lastSequenceNumber | integer | Poslední pořadové číslo z fronty. |
| firstEnqueueTime | řetězec | Při prvním z fronty. |
| lastEnqueueTime | řetězec | Čas poslední z fronty. |

## <a name="next-steps"></a>Další postup

* Úvod do Azure událostí mřížky, najdete v části [co je mřížky událostí?](overview.md)
* Další informace o vytváření předplatného služby Azure událostí mřížky, najdete v části [schématu odběru událostí mřížky](subscription-creation-schema.md).
* Informace o zpracování událostí event hubs naleznete v tématu [Stream velkých objemů dat do datového skladu](event-grid-event-hubs-integration.md).