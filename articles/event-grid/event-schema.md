---
title: Schéma událostí sítě Azure
description: Popisuje vlastnosti a schéma, které jsou k dispozici pro všechny události.Události se skládají ze sady pěti požadovaných vlastností řetězce a požadovaného datového objektu.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 35cea2e6df311d2f4071686c21c8e4c36477abc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244833"
---
# <a name="azure-event-grid-event-schema"></a>Schéma událostí sítě Azure

Tento článek popisuje vlastnosti a schémata, které jsou k dispozici pro všechny události.Události se skládají ze sady pěti požadovaných vlastností řetězce a požadovaného datového objektu. Vlastnosti jsou společné pro všechny události od libovolného vydavatele. Datový objekt má vlastnosti, které jsou specifické pro každého vydavatele. Pro systémová témata jsou tyto vlastnosti specifické pro poskytovatele prostředků, jako je Azure Storage nebo Azure Event Hubs.

Zdroje událostí odesílají události do Služby Azure Event Grid v poli, které může mít několik objektů událostí. Při zaúčtování událostí do tématu mřížky událostí může mít pole celkovou velikost až 1 MB. Každá událost v poli je omezena na 64 KB (obecná dostupnost) nebo 1 MB (náhled). Pokud je událost nebo pole větší než omezení velikosti, obdržíte odpověď **413 Payload Too Large**.

> [!NOTE]
> Na událost o velikosti až 64 kB se vztahuje smlouva o úrovni služeb obecné dostupnosti (GA). Podpora pro událost o velikosti až 1 MB je aktuálně ve verzi Preview. Události nad 64 KB se účtují v krocích po 64 KB. 

Event Grid odešle události odběratelům v poli, které má jednu událost. Toto chování se může v budoucnu změnit.

Schéma JSON pro událost Event Grid a datovou část každého vydavatele Azure najdete v [úložišti schématu událostí](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane).

## <a name="event-schema"></a>Schéma událostí

Následující příklad ukazuje vlastnosti, které používají všichni vydavatelé událostí:

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

Například schéma publikované pro událost úložiště objektů blob Azure je:

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
]
```

## <a name="event-properties"></a>Vlastnosti události

Všechny události mají stejné následující údaje nejvyšší úrovně:

| Vlastnost | Typ | Požaduje se | Popis |
| -------- | ---- | -------- | ----------- |
| téma | řetězec | Ne, ale pokud je zahrnuto, musí přesně odpovídat tématu Mřížka událostí ID Správce prostředků Azure. Pokud není zahrnuta, event grid bude razítko na událost. | Úplná cesta k prostředku ke zdroji události. Toto pole nelze zapisovat. Tuto hodnotu poskytuje Event Grid. |
| Předmět | řetězec | Ano | Cesta k předmětu události, kterou definuje vydavatel. |
| Eventtype | řetězec | Ano | Jeden z registrovaných typů události pro tento zdroj události. |
| eventTime | řetězec | Ano | Čas, kdy je událost generována na základě času UTC zprostředkovatele. |
| id | řetězec | Ano | Jedinečný identifikátor události |
| data | objekt | Ne | Data událostí specifická pro poskytovatele prostředků. |
| dataVersion | řetězec | Ne, ale bude označeno prázdnou hodnotou. | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | řetězec | Není vyžadováno, ale pokud je zahrnuto, `metadataVersion` musí přesně odpovídat `1`schématu mřížky událostí (v současné době pouze ). Pokud není zahrnuta, event grid bude razítko na událost. | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

Informace o vlastnostech v datovém objektu naleznete ve zdroji události:

* [Předplatná Azure (operace správy)](event-schema-subscriptions.md)
* [Registr kontejnerů](event-schema-container-registry.md)
* [Blob Storage](event-schema-blob-storage.md)
* [Centra událostí](event-schema-event-hubs.md)
* [IoT Hub](event-schema-iot-hub.md)
* [Media Services](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [Skupiny prostředků (operace správy)](event-schema-resource-groups.md)
* [Service Bus](event-schema-service-bus.md)
* [Azure SignalR](event-schema-azure-signalr.md)
* [Azure Machine Learning](event-schema-machine-learning.md)

Pro vlastní témata určuje datový objekt vydavatel události. Data nejvyšší úrovně by měla mít stejná pole jako standardní události definované prostředky.

Při publikování událostí do vlastních témat vytvořte pro události předměty, které předplatitelům usnadní zjistit, zda mají o událost zájem. Odběratelé používají předmět k filtrování a směrování událostí. Zvažte poskytnutí cesty, kde k události došlo, aby odběratelé mohli filtrovat podle segmentů této cesty. Cesta umožňuje odběratelům úzce nebo široce filtrovat události. Pokud například zadáte třícestnou `/A/B/C` cestu segmentu, jako je předmět, `/A` mohou odběratelé filtrovat podle prvního segmentu a získat tak širokou sadu událostí. Tito odběratelé získat události `/A/B/C` `/A/D/E`s předměty, jako je nebo . Ostatní odběratelé mohou `/A/B` filtrovat podle získat užší sadu událostí.

Někdy váš předmět potřebuje více podrobností o tom, co se stalo. Například vydavatel **účtů úložiště** poskytuje `/blobServices/default/containers/<container-name>/blobs/<file>` předmět při přidání souboru do kontejneru. Odběratel může filtrovat `/blobServices/default/containers/testcontainer` podle cesty získat všechny události pro tento kontejner, ale ne jiné kontejnery v účtu úložiště. Odběratel může také filtrovat nebo směrovat `.txt` podle přípony pracovat pouze s textovými soubory.

## <a name="next-steps"></a>Další kroky

* Úvod do Služby Azure Event Grid najdete v tématu [Co je event grid?](overview.md)
* Další informace o vytvoření předplatného Služby Azure Event Grid najdete v [tématu schéma předplatného služby Event Grid](subscription-creation-schema.md).
