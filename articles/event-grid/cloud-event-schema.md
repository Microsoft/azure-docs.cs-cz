---
title: Schéma CloudEvents v 1.0 s Azure Event Grid
description: V této části najdete popis postupu použití schématu CloudEvents v 1.0 pro události v Azure Event Grid. Služba podporuje události v implementaci JSON cloudových událostí.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: d17e92c28784ca31f3c9809c93e885b22c6a38d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324174"
---
# <a name="cloudevents-v10-schema-with-azure-event-grid"></a>Schéma CloudEvents v 1.0 s Azure Event Grid

Kromě [výchozího schématu událostí](event-schema.md)Azure Event Grid nativně podporuje události v [implementaci JSON pro vazby protokolu CloudEvents v 1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) a [http](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) je [otevřená specifikace](https://github.com/cloudevents/spec/blob/v1.0/spec.md) popisující data události.

CloudEvents zjednodušuje interoperabilitu tím, že poskytuje společné schéma událostí pro publikování a spotřebovávání cloudových událostí. Toto schéma umožňuje jednotným nástrojům, standardním způsobům směrování &ch událostí a univerzálním způsobům deserializace vnějšího schématu událostí. Pomocí společného schématu můžete snadněji integrovat práci na různých platformách.

CloudEvents se sestavuje několika [spolupracovníky](https://github.com/cloudevents/spec/blob/master/community/contributors.md), včetně Microsoftu, prostřednictvím [základu cloud computingu Native](https://www.cncf.io/). Je aktuálně k dispozici jako verze 1,0.

Tento článek popisuje CloudEvents schéma pomocí Event Grid.

## <a name="sample-event-using-cloudevents-schema"></a>Ukázková událost pomocí schématu CloudEvents

Tady je příklad události Azure Blob Storage ve formátu CloudEvents:

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

Podrobný popis dostupných polí, jejich typy a definice v CloudEvents v 1.0 jsou [k dispozici zde](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Hodnoty hlaviček pro události doručené ve schématu CloudEvents a schématu Event Grid jsou stejné s výjimkou `content-type` . V případě schématu CloudEvents je hodnota hlavičky `"content-type":"application/cloudevents+json; charset=utf-8"` . Pro Event Grid schéma je hodnota hlavičky `"content-type":"application/json; charset=utf-8"` .

## <a name="event-grid-for-cloudevents"></a>Event Grid pro CloudEvents

Event Grid můžete použít pro vstupní i výstupní události ve schématu CloudEvents. CloudEvents můžete použít pro systémové události, jako jsou události Blob Storage a události IoT Hub a vlastní události. Tyto události může také transformovat na základě spojení a zpátky.


| Vstupní schéma       | Výstupní schéma
|--------------------|---------------------
| Formát CloudEvents | Formát CloudEvents
| Formát Event Grid  | Formát CloudEvents
| Formát Event Grid  | Formát Event Grid

Pro všechna schémata událostí Event Grid vyžaduje ověření při publikování do služby Event Grid a při vytváření odběru události. Další informace najdete v tématu [Event Grid zabezpečení a ověřování](security-authentication.md).

## <a name="next-steps"></a>Další kroky
Podívejte [se, jak používat schéma CloudEvents v 1.0 s Event Grid](cloudevents-schema.md).  
