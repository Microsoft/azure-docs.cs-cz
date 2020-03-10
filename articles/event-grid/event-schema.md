---
title: Azure Event Grid schéma událostí
description: Popisuje vlastnosti a schéma, které jsou k dispozici pro všechny události. Události se skládají ze sady pěti požadovaných vlastností řetězce a požadovaného datového objektu.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 35cea2e6df311d2f4071686c21c8e4c36477abc1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370559"
---
# <a name="azure-event-grid-event-schema"></a>Azure Event Grid schéma událostí

Tento článek popisuje vlastnosti a schéma, které jsou k dispozici pro všechny události. Události se skládají ze sady pěti požadovaných vlastností řetězce a požadovaného datového objektu. Vlastnosti jsou společné pro všechny události od libovolného vydavatele. Datový objekt má vlastnosti, které jsou specifické pro každého vydavatele. Pro systémová témata jsou tyto vlastnosti specifické pro poskytovatele prostředků, jako je Azure Storage nebo Azure Event Hubs.

Zdroje událostí odesílají události do Azure Event Grid v poli, které mohou mít několik objektů událostí. Při odesílání událostí do tématu Event gridu může mít pole celkovou velikost až 1 MB. Každá událost v poli je omezená na 64 KB (Obecná dostupnost) nebo 1 MB (Preview). Pokud je událost nebo pole větší než omezení velikosti, obdržíte **příliš velkou datovou část odpovědi 413**.

> [!NOTE]
> K události velikosti až 64 KB se vztahuje Obecná dostupnost (GA) smlouva SLA (SLA). Podpora pro událost velikosti až 1 MB je v současnosti ve verzi Preview. Události větší než 64 KB se účtují v přírůstcích po 64 až KB. 

Event Grid odesílá události předplatitelům v poli, které obsahuje jednu událost. Toto chování se může v budoucnu změnit.

Můžete najít schéma JSON pro událost Event Grid a každou datovou část dat vydavatele Azure v [úložišti schématu událostí](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane).

## <a name="event-schema"></a>Schéma událostí

Následující příklad ukazuje vlastnosti, které jsou používány všemi vydavateli událostí:

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

Například schéma publikované pro událost úložiště objektů BLOB v Azure je:

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

Všechny události mají stejné následující data nejvyšší úrovně:

| Vlastnost | Typ | Požadováno | Popis |
| -------- | ---- | -------- | ----------- |
| téma | string | Ne, ale v případě zahrnutí, se musí přesně shodovat s Event Gridým tématem Azure Resource Manager ID. Pokud není zahrnutý, Event Grid na událost zařadí razítko. | Úplná cesta prostředku ke zdroji událostí. Do tohoto pole nejde zapisovat. Tuto hodnotu poskytuje Event Grid. |
| subject | string | Ano | Cesta definovaná vydavatelem k předmětu události |
| eventType | string | Ano | Jeden z registrovaných typů událostí pro tento zdroj události. |
| eventTime | string | Ano | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| id | string | Ano | Jedinečný identifikátor události |
| data | objekt | Ne | Data události specifická pro poskytovatele prostředků. |
| dataVersion | string | Ne, ale bude označena prázdnou hodnotou. | Verze schématu datového objektu. Vydavatel definuje verzi schématu. |
| metadataVersion | string | Nepožaduje se, ale pokud je zahrnutý, musí odpovídat schématu Event Grid `metadataVersion` přesně (v současné době pouze `1`). Pokud není zahrnutý, Event Grid na událost zařadí razítko. | Verze schématu metadat události. Event Grid definuje schéma vlastností nejvyšší úrovně. Tuto hodnotu poskytuje Event Grid. |

Další informace o vlastnostech v datovém objektu najdete v tématu zdroj události:

* [Předplatná Azure (operace správy)](event-schema-subscriptions.md)
* [Container Registry](event-schema-container-registry.md)
* [Blob Storage](event-schema-blob-storage.md)
* [Event Hubs](event-schema-event-hubs.md)
* [IoT Hub](event-schema-iot-hub.md)
* [Media Services](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [Skupiny prostředků (operace správy)](event-schema-resource-groups.md)
* [Service Bus](event-schema-service-bus.md)
* [Signál Azure](event-schema-azure-signalr.md)
* [Azure Machine Learning](event-schema-machine-learning.md)

Pro vlastní témata Vydavatel události Určuje datový objekt. Data nejvyšší úrovně by měla mít stejná pole jako standardní události definované prostředky.

Při publikování událostí pro vlastní témata vytvořte předměty pro události, které předplatitelům umožní snadno zjistit, jestli se o událost zajímá. Předplatitelé používají subjekt k filtrování a směrování událostí. Zvažte zadání cesty, kde došlo k události, takže předplatitelé mohou filtrovat segmenty této cesty. Cesta umožňuje předplatitelům zúžit nebo široce filtrovat události. Pokud například zadáte cestu se třemi segmenty, například `/A/B/C` v předmětu, mohou předplatitelé filtrovat podle prvního segmentu `/A` a získat tak širokou škálu událostí. Tyto předplatitelé získávají události s předměty, jako je `/A/B/C` nebo `/A/D/E`. Jiní předplatitelé mohou filtrovat podle `/A/B` a získat tak užší sadu událostí.

Někdy váš předmět potřebuje více podrobností o tom, co se stalo. Například Vydavatel **účtů úložiště** poskytuje předmět `/blobServices/default/containers/<container-name>/blobs/<file>`, když se do kontejneru přidá soubor. Předplatitel může filtrovat podle `/blobServices/default/containers/testcontainer` cesty, aby získal všechny události pro tento kontejner, ale ne jiné kontejnery v účtu úložiště. Předplatitel může také filtrovat nebo směrovat podle přípony `.txt` jenom pro práci s textovými soubory.

## <a name="next-steps"></a>Další kroky

* Úvod do Azure Event Grid najdete v tématu [co je Event Grid?](overview.md)
* Další informace o vytváření předplatného Azure Event Grid najdete v tématu [schéma předplatného Event Grid](subscription-creation-schema.md).
