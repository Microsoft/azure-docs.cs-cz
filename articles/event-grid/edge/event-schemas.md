---
title: Schémata událostí – Azure Event Grid IoT Edge | Dokumenty společnosti Microsoft
description: Schémata událostí v mřížce událostí na okraji IoT.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba261aeedf6574f69d3c05f8fd005c912dcc59d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73242463"
---
# <a name="event-schemas"></a>Schémata událostí

Modul Event Grid přijímá a doručuje události ve formátu JSON. V současné době existují tři schémata, která jsou podporována programem Event Grid: -

* **EventGridSchema**
* **VlastníSchema**
* **CloudEventSchema**

Můžete nakonfigurovat schéma, které musí vydavatel splňovat během vytváření tématu. Pokud není zadán, je výchozí **EventGridSchema**. Události, které neodpovídají očekávanému schématu, budou odmítnuty.

Předplatitelé můžete také nakonfigurovat schéma, ve kterém chtějí události doručeny. Pokud není zadán, výchozí je schéma tématu.
V současné době odběratel doručení schéma musí odpovídat jeho téma vstupní schéma. 

## <a name="eventgrid-schema"></a>Schéma EventGrid

Schéma EventGrid se skládá ze sady požadovaných vlastností, kterým musí entita publikování odpovídat. Každý vydavatel musí vyplnit pole nejvyšší úrovně.

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

### <a name="eventgrid-schema-properties"></a>Vlastnosti schématu EventGrid

Všechny události mají následující data nejvyšší úrovně:

| Vlastnost | Typ | Požaduje se | Popis |
| -------- | ---- | ----------- |-----------
| téma | řetězec | Ne | By se měla shodovat s tématem, na kterém je publikován. Event Grid naplní název tématu, na kterém je publikován, pokud není zadán. |
| Předmět | řetězec | Ano | Cesta k předmětu události, kterou definuje vydavatel. |
| Eventtype | řetězec | Ano | Typ události pro tento zdroj událostí, například Objekt blobCreated. |
| eventTime | řetězec | Ano | Čas, kdy je událost generována na základě času UTC zprostředkovatele. |
| ID | řetězec | Ne | Jedinečný identifikátor události |
| data | objekt | Ne | Slouží k zachycení dat událostí, která jsou specifická pro entitu publikování. |
| dataVersion | řetězec | Ano | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | řetězec | Ne | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

### <a name="example--eventgrid-schema-event"></a>Příklad – událost schématu EventGrid

```json
[
  {
    "id": "1807",
    "eventType": "recordInserted",
    "subject": "myapp/vehicles/motorcycles",
    "eventTime": "2017-08-10T21:03:07+00:00",
    "data": {
      "make": "Ducati",
      "model": "Monster"
    },
    "dataVersion": "1.0"
  }
]
```

## <a name="customevent-schema"></a>Schéma CustomEvent

Ve vlastním schématu neexistují žádné povinné vlastnosti, které jsou vynuceny jako schéma EventGrid. Publikování entita může zcela řídit schéma události. Poskytuje maximální flexibilitu a umožňuje scénáře, kde máte systém založený na událostech již na místě a chcete znovu použít existující události a/nebo nechcete být vázáni na konkrétní schéma.

### <a name="custom-schema-properties"></a>Vlastní vlastnosti schématu

Žádné povinné vlastnosti. Je na publikační entitě k určení datové části.

### <a name="example--custom-schema-event"></a>Příklad – událost vlastního schématu

```json
[
  {
    "eventdata": {
      "make": "Ducati",
      "model": "Monster"
    }
  }
]
```

## <a name="cloudevent-schema"></a>Schéma CloudEvent

Kromě výše uvedených schémat nativně aplikace Event Grid nativně podporuje události ve [schématu CloudEvents JSON](https://github.com/cloudevents/spec/blob/master/json-format.md). CloudEvents je otevřená specifikace pro popis dat událostí. Zjednodušuje interoperabilitu tím, že poskytuje společné schéma událostí pro publikování a využívání událostí. Je součástí [CNCF](https://www.cncf.io/) a v současné době je k dispozici verze 1.0-rc1.

### <a name="cloudevent-schema-properties"></a>Vlastnosti schématu CloudEvent

Informace o povinných vlastnostech obálky naleznete ve [specifikaci CloudEvents.](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope)

### <a name="example--cloud-event"></a>Příklad – cloudová událost
```json
[{
       "id": "1807",
       "type": "recordInserted",
       "source": "myapp/vehicles/motorcycles",
       "time": "2017-08-10T21:03:07+00:00",
       "datacontenttype": "application/json",
       "data": {
            "make": "Ducati",
            "model": "Monster"
        },
        "dataVersion": "1.0",
        "specVersion": "1.0-rc1"
}]
```
