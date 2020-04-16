---
title: Použití Azure Event Grid s událostmi ve schématu CloudEvents
description: Popisuje, jak používat schéma CloudEvents pro události v Azure Event Grid. Služba podporuje události v implementaci JSON cloudových událostí.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 127095bef2c67a93097bf90bea54ca1b44b16c58
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394381"
---
# <a name="cloudevents-v10-schema-with-event-grid"></a>Schéma CloudEvents v1.0 s mřížkou událostí

Kromě [výchozího schématu událostí](event-schema.md)azure event grid nativně podporuje události v [implementaci JSON cloudevents v1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) a [http protokolu vazby](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md). [CloudEvents](https://cloudevents.io/) je [otevřená specifikace](https://github.com/cloudevents/spec/blob/v1.0/spec.md) pro popis dat událostí.

CloudEvents zjednodušuje interoperabilitu tím, že poskytuje společné schéma událostí pro publikování a využívání cloudových událostí. Toto schéma umožňuje jednotné nástroje, standardní způsoby směrování & zpracování událostí a univerzální způsoby deserializace schématu vnější události. Díky běžnému schématu můžete snadněji integrovat práci napříč platformami.

CloudEvents je budován několika [spolupracovníky](https://github.com/cloudevents/spec/blob/master/community/contributors.md), včetně Microsoftu, prostřednictvím [Cloud Native Computing Foundation](https://www.cncf.io/). V současné době je k dispozici jako verze 1.0.

Tento článek popisuje schéma CloudEvents s mřížkou událostí.

## <a name="sample-event-using-cloudevents-schema"></a>Ukázková událost pomocí schématu CloudEvents

Tady je příklad události azure blob storage ve formátu CloudEvents:

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

Podrobný popis dostupných polí, jejich typů a definic v CloudEvents v1.0 je [k dispozici zde](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes).

Hodnoty záhlaví pro události dodané ve schématu CloudEvents a schéma mřížky událostí `content-type`jsou stejné s výjimkou . Pro schéma CloudEvents je `"content-type":"application/cloudevents+json; charset=utf-8"`tato hodnota záhlaví . Pro schéma mřížky událostí je `"content-type":"application/json; charset=utf-8"`tato hodnota záhlaví .

## <a name="event-grid-for-cloudevents"></a>Mřížka událostí pro cloudové události

Event Grid můžete použít pro vstup i výstup událostí ve schématu CloudEvents. CloudEvents můžete použít pro systémové události, jako jsou události úložiště objektů blob a události služby IoT Hub a vlastní události. Může také transformovat tyto události na drátu tam a zpět.


| Vstupní schéma       | Výstupní schéma
|--------------------|---------------------
| Formát CloudEvents | Formát CloudEvents
| Formát mřížky událostí  | Formát CloudEvents
| Formát mřížky událostí  | Formát mřížky událostí

Pro všechna schémata událostí událost grid vyžaduje ověření při publikování na téma mřížky událostí a při vytváření odběr událostí. Další informace naleznete v [tématu Zabezpečení a ověřování mřížky událostí](security-authentication.md).

## <a name="next-steps"></a>Další kroky
Viz [Jak používat CloudEvents v1.0 schéma s Event Grid](cloudevents-schema.md).  
