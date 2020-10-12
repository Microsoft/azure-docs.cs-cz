---
title: Azure Event Grid schéma událostí
description: Popisuje vlastnosti a schéma, které jsou k dispozici pro všechny události.Události se skládají ze sady pěti požadovaných vlastností řetězce a požadovaného datového objektu.
ms.topic: reference
ms.date: 07/07/2020
ms.openlocfilehash: 7ddc7c78c5a9e5ba2a57b21c45fb9fab65056ee9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86105876"
---
# <a name="azure-event-grid-event-schema"></a>Azure Event Grid schéma událostí

Tento článek popisuje vlastnosti a schéma, které jsou k dispozici pro všechny události.Události se skládají ze sady pěti požadovaných vlastností řetězce a požadovaného datového objektu. Vlastnosti jsou společné pro všechny události od libovolného vydavatele. Datový objekt má vlastnosti, které jsou specifické pro každého vydavatele. Pro systémová témata jsou tyto vlastnosti specifické pro poskytovatele prostředků, jako je Azure Storage nebo Azure Event Hubs.

Zdroje událostí odesílají události do Azure Event Grid v poli, které mohou mít několik objektů událostí. Při odesílání událostí do tématu Event gridu může mít pole celkovou velikost až 1 MB. Každá událost v poli je omezená na 1 MB. Pokud je událost nebo pole větší než omezení velikosti, obdržíte **příliš velkou datovou část odpovědi 413**. Operace se účtují v 64 KB, ale i když se to bude zvyšovat. Události, které překročí 64 KB, budou účtovat poplatky za operace, jako by se jednalo o více událostí. Například událost, která je 130 KB, by mohla způsobit operace, jako by se jednalo o 3 samostatné události.

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

| Vlastnost | Typ | Vyžadováno | Popis |
| -------- | ---- | -------- | ----------- |
| téma | řetězec | Ne, ale v případě zahrnutí, se musí přesně shodovat s Event Gridým tématem Azure Resource Manager ID. Pokud není zahrnutý, Event Grid na událost zařadí razítko. | Úplná cesta prostředku ke zdroji událostí. Do tohoto pole nejde zapisovat. Tuto hodnotu poskytuje Event Grid. |
| subject | řetězec | Yes | Cesta k předmětu události, kterou definuje vydavatel. |
| eventType | řetězec | Yes | Jeden z registrovaných typů události pro tento zdroj události. |
| eventTime | řetězec | Yes | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| id | řetězec | Yes | Jedinečný identifikátor události |
| data | object | No | Data události specifická pro poskytovatele prostředků. |
| dataVersion | řetězec | Ne, ale bude označena prázdnou hodnotou. | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | řetězec | Nepožadováno, ale pokud je součástí, musí přesně odpovídat schématu Event Grid `metadataVersion` (aktuálně jenom `1` ). Pokud není zahrnutý, Event Grid na událost zařadí razítko. | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

Další informace o vlastnostech v datovém objektu najdete v tématu zdroj události:

* [Předplatná Azure (operace správy)](event-schema-subscriptions.md)
* [Container Registry](event-schema-container-registry.md)
* [Blob Storage](event-schema-blob-storage.md)
* [Event Hubs](event-schema-event-hubs.md)
* [IoT Hub](event-schema-iot-hub.md)
* [Media Services](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [Skupiny prostředků (operace správy)](event-schema-resource-groups.md)
* [Service Bus](event-schema-service-bus.md)
* [Azure SignalR](event-schema-azure-signalr.md)
* [Azure Machine Learning](event-schema-machine-learning.md)

Pro vlastní témata Vydavatel události Určuje datový objekt. Data nejvyšší úrovně by měla mít stejná pole jako standardní události definované prostředky.

Při publikování událostí pro vlastní témata vytvořte předměty pro události, které předplatitelům umožní snadno zjistit, jestli se o událost zajímá. Předplatitelé používají subjekt k filtrování a směrování událostí. Zvažte zadání cesty, kde došlo k události, takže předplatitelé mohou filtrovat segmenty této cesty. Cesta umožňuje předplatitelům zúžit nebo široce filtrovat události. Pokud například zadáte tři cesty segmentu jako `/A/B/C` v předmětu, můžou předplatitelé filtrovat podle prvního segmentu `/A` a získat tak širokou škálu událostí. Tyto předplatitelé získají události s předměty, jako je `/A/B/C` nebo `/A/D/E` . Jiní předplatitelé mohou filtrovat podle `/A/B` a získat tak užší sadu událostí.

Někdy váš předmět potřebuje více podrobností o tom, co se stalo. Například při přidání souboru do kontejneru poskytne vydavatel **účtů úložiště** předmět `/blobServices/default/containers/<container-name>/blobs/<file>` . Předplatitel může filtrovat podle cesty `/blobServices/default/containers/testcontainer` a získat tak všechny události tohoto kontejneru, ale ne jiné kontejnery v účtu úložiště. Předplatitel může také filtrovat nebo směrovat podle přípony `.txt` tak, aby pracovala pouze s textovými soubory.

## <a name="next-steps"></a>Další kroky

* Úvod do Azure Event Grid najdete v tématu [co je Event Grid?](overview.md)
* Další informace o vytváření předplatného Azure Event Grid najdete v tématu [schéma předplatného Event Grid](subscription-creation-schema.md).
