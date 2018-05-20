---
title: Použití Azure událostí mřížky s událostmi ve schématu CloudEvents
description: Popisuje, jak nastavit CloudEvents schéma pro události v mřížce událostí Azure.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: babanisa
ms.openlocfilehash: 23187fbc230e384984085d330bfbfbc90cc9f945
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="use-cloudevents-schema-with-event-grid"></a>Použití schématu CloudEvents s událostí mřížky

Kromě jeho [výchozí schéma událostí](event-schema.md), mřížky událostí Azure nativně podporuje události v [schématu CloudEvents JSON](https://github.com/cloudevents/spec/blob/master/json-format.md). [CloudEvents](http://cloudevents.io/) je [otevřete standardní specifikace](https://github.com/cloudevents/spec/blob/master/spec.md) popisujících data události v běžným způsobem.

CloudEvents zjednodušuje interoperabilita tím, že poskytuje společné schéma událostí pro publikování a využívání cloud na základě událostí. Toto schéma umožňuje uniform nástrojů, standardní způsoby směrování a zpracování událostí a univerzální způsobů deserializaci schématu vnější události. S společné schéma můžete snadno integrovat pracovní napříč platformami.

CloudEvents se sestavení pomocí několika [spolupracovníci](https://github.com/cloudevents/spec/blob/master/community/contributors.md), včetně společnosti Microsoft, prostřednictvím [cloudu nativní výpočetní Foundation](https://www.cncf.io/). Je aktuálně k dispozici jako verze 0,1.

Tento článek popisuje použití schématu CloudEvents s událostí mřížky.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>CloudEvent schématu

Tady je příklad událost Azure Blob Storage ve formátu CloudEvents:

``` JSON
{
    "cloudEventsVersion" : "0.1",
    "eventType" : "Microsoft.Storage.BlobCreated",
    "eventTypeVersion" : "",
    "source" : "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}#blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "eventID" : "173d9985-401e-0075-2497-de268c06ff25",
    "eventTime" : "2018-04-28T02:18:47.1281675Z",
    "data" : {
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
    }
}
```

CloudEvents v0.1 má k dispozici následující vlastnosti:

| CloudEvents        | Typ     | Příklad hodnota JSON             | Popis                                                        | Mapování mřížky událostí
|--------------------|----------|--------------------------------|--------------------------------------------------------------------|-------------------------
| Typ události          | Řetězec   | "com.example.someevent"          | Typ došlo k výskytu                                   | Typ události
| eventTypeVersion   | Řetězec   | "1.0"                            | Verze eventType (volitelné)                            | dataVersion
| cloudEventsVersion | Řetězec   | "0,1"                            | Verze specifikaci CloudEvents, který používá události        | *předána*
| source             | URI      | "/ mycontext"                     | Popisuje producent událostí                                       | téma #subject
| ID události            | Řetězec   | "1234-1234-1234"                 | ID události                                                    | id
| eventTime          | Časové razítko| "2018-04-05T17:31:00Z"           | Časové razítko kdy k události došlo (volitelné)                    | eventTime
| schemaURL          | URI      | https://myschema.com           | Odkaz na schéma odpovídající atribut dat (volitelné) | *nepoužívá se*
| Typ obsahu        | Řetězec   | "application/json"               | Popisu formátu dat kódování (volitelné)                       | *nepoužívá se*
| Rozšíření         | Mapa      | {"extA": "vA", "extB", "vB"}  | Žádné další metadata (volitelné)                                 | *nepoužívá se*
| data               | Objekt   | {"objA": "vA", "objB", "vB"}  | Datová část události (volitelné)                                       | data

Další informace najdete v tématu [CloudEvents specifikace](https://github.com/cloudevents/spec/blob/master/spec.md#context-attributes).

## <a name="configure-event-grid-for-cloudevents"></a>Konfigurace událostí mřížky pro CloudEvents

V současné době má mřížky událostí Azure preview podpora pro CloudEvents JSON formát vstupu a výstupu v **– Západ střední USA**, **střed USA**, a **Severní Evropa**.

Mřížky událostí můžete použít pro vstupní i výstupní událostí ve schématu CloudEvents. CloudEvents můžete použít pro systémové události, jako úložiště objektů Blob události a události služby IoT Hub a vlastních událostí. Také ho můžete převést tyto události v drátové síti a zpět.


| Vstupní schéma       | Schéma výstupu
|--------------------|---------------------
| Formát CloudEvents | Formát CloudEvents
| Formát mřížky událostí  | Formát CloudEvents
| Formát CloudEvents | Formát mřížky událostí
| Formát mřížky událostí  | Formát mřížky událostí

Pro všechny události schémata událostí mřížky vyžaduje ověření, při publikování do mřížky téma události a při vytváření předplatného služby událostí. Další informace najdete v tématu [mřížky událostí zabezpečení a ověřování](security-authentication.md).

### <a name="input-schema"></a>Vstupní schéma

Pokud chcete nastavit vstupní schéma na téma na vlastní CloudEvents, použijte následující parametr v rozhraní příkazového řádku Azure a když vytvoříte téma `--input-schema cloudeventv01schema`. Vlastní téma nyní očekává, že příchozí události ve formátu v0.1 CloudEvents.

Chcete-li vytvořit téma události mřížky, použijte:

```azurecli
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventv01schema
```

Aktuální verze CloudEvents nepodporuje dávkování událostí. K publikování událostí se schématem CloudEvent do tématu, publikujte všechny události jednotlivě.

### <a name="output-schema"></a>Schéma výstupu

Pokud chcete nastavit výstupního schématu na základě předplatného služby událostí k CloudEvents, použijte tento parametr v rozhraní příkazového řádku Azure a při vytváření předplatného událostí `--event-delivery-schema cloudeventv01schema`. Události pro tento odběr události jsou nyní doručena ve formátu v0.1 CloudEvents.

Chcete-li vytvořit odběr událostí, použijte:

```azurecli
az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --topic-name <topic_name> \
  -g gridResourceGroup \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventv01schema
```

Aktuální verze CloudEvents nepodporuje dávkování událostí. Všechny události obdrží předplatné událostí, který je nakonfigurován pro CloudEvent schématu jednotlivě.

## <a name="next-steps"></a>Další postup

* Informace o sledování událostí dodávky najdete v tématu [doručení zpráv monitorování událostí mřížky](monitor-event-delivery.md).
* Doporučujeme vám otestovat, komentáře, a [přispívat](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md) k CloudEvents.
* Další informace o vytváření předplatného služby Azure událostí mřížky, najdete v části [schématu odběru událostí mřížky](subscription-creation-schema.md).
