---
title: Schéma událostí služby Azure Event Grid
description: Popisuje vlastnosti, které jsou k dispozici pro události pomocí služby Azure Event Grid
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 07/06/2018
ms.author: babanisa
ms.openlocfilehash: 266ddced5f1949fa72508d914f76953101a7aac6
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902221"
---
# <a name="azure-event-grid-event-schema"></a>Schéma událostí služby Azure Event Grid

Tento článek popisuje vlastnosti a schéma, které jsou k dispozici pro všechny události. Události obsahují sadu pěti řetězec požadované vlastnosti a objekt požadovaná data. Vlastnosti jsou společné pro všechny události z libovolného vydavatele. Datový objekt obsahuje vlastnosti, které jsou specifické pro každý vydavatele. Témata systému tyto vlastnosti jsou specifické pro poskytovatele prostředků, jako je například Azure Storage nebo Azure Event Hubs.

Zdroje událostí v poli, který může mít několik objektů událostí odesílání událostí do služby Azure Event Grid. Při odesílání událostí do téma event gridu, může pole mít celková velikost až 1 MB. Každá událost v poli je omezena na 64 KB. Pokud událost nebo pole je větší než omezení velikosti, obdržíte odpověď **413 datové části příliš velké**.

Event Grid odesílá události odběratelům pole, které má jednu událost. Toto chování se může v budoucnu změnit.

Můžete najít schéma JSON pro události Event gridu a každý Azure vydavatele datová část [schématu události úložiště](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane).

## <a name="event-schema"></a>Schéma událostí

Následující příklad ukazuje vlastnosti, které jsou používány všechny zdroje událostí:

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

Schéma publikována pro událost Azure Blob storage je například:

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

Všechny události mají stejné nejvyšší úrovně následující data:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| téma | řetězec | Úplné prostředků cesta ke zdroji události. Toto pole není zapisovatelná. Event gridu poskytuje tuto hodnotu. |
| předmět | řetězec | Vydavatel definované cesta předmět události. |
| Typ události | řetězec | Jeden z typů registrované události pro tento zdroj událostí. |
| čas události | řetězec | Vygenerování události podle času UTC poskytovatele. |
| id | řetězec | Jedinečný identifikátor pro událost. |
| data | objekt | Data události specifické pro poskytovatele prostředků. |
| dataVersion | řetězec | Verze schématu datového objektu Vydavatel Určuje verzi schématu. |
| verze metadataVersion | řetězec | Verze schématu metadat události Event Grid definuje schéma vlastnosti nejvyšší úrovně. Event gridu poskytuje tuto hodnotu. |

Další informace o vlastnosti datového objektu, naleznete v tématu zdroje událostí:

* [Předplatná Azure (operace správy)](event-schema-subscriptions.md)
* [Blob Storage](event-schema-blob-storage.md)
* [Event Hubs](event-schema-event-hubs.md)
* [IoT Hub](event-schema-iot-hub.md)
* [Media Services](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [Skupiny prostředků (operace správy)](event-schema-resource-groups.md)
* [Service Bus](event-schema-service-bus.md)

Zdroj události pro vlastní témata, určuje datový objekt. Nejvyšší úrovně dat by měl mít stejné pole jako standardní událostí definovaných prostředků.

Při publikování událostí do vlastní témata, vytvořte předměty pro události, které usnadňují předplatitelům vědět, jestli máte zájem o události. Předplatitelé využít subjektu k filtrování a směrování událostí. Vezměte v úvahu zajišťující cesta kde k události došlo, takže předplatitelé můžete filtrovat podle segmentů cesty. Cesta umožňuje předplatitelům úzce nebo široce filtrovat události. Například, pokud zadáváte cestu tři segmentu jako `/A/B/C` v předmětu, můžete filtrovat předplatitele první segment `/A` zobrazíte široké škály události. Tyto předplatitelé získají události s popsány oblasti jako `/A/B/C` nebo `/A/D/E`. Ostatní účastníky můžete filtrovat podle `/A/B` zobrazíte užší sadu událostí.

Někdy vašich vztahuje potřebuje více podrobností o co se stalo. Například **účty úložiště** vydavatele poskytuje předmět `/blobServices/default/containers/<container-name>/blobs/<file>` při přidání souboru do kontejneru. Může předplatitel filtrovat podle cesty `/blobServices/default/containers/testcontainer` zobrazíte všechny události pro tohoto kontejneru, ale ne další kontejnery v účtu úložiště. Může také filtrovat předplatitele nebo trasy podle přípony `.txt` pracovat pouze s textovými soubory.

## <a name="next-steps"></a>Další postup

* Úvod do služby Azure Event Grid najdete v tématu [novinky služby Event Grid?](overview.md)
* Další informace o vytváření předplatného služby Azure Event Grid najdete v tématu [schéma předplatného služby Event Grid](subscription-creation-schema.md).
