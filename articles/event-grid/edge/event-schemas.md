---
title: Schémata událostí – Azure Event Grid IoT Edge | Microsoft Docs
description: Schémata událostí v Event Grid IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: ea36c40f2038d016afb0c45944a98d4d90df6240
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171563"
---
# <a name="event-schemas"></a>Schémata událostí

Event Grid modul přijímá a doručuje události ve formátu JSON. V současné době existují tři schémata podporovaná Event Grid:-

* **EventGridSchema**
* **CustomSchema**
* **CloudEventSchema**

Můžete nakonfigurovat schéma, které musí vydavatel splňovat při vytváření tématu. Pokud tento parametr nezadáte, použije se výchozí hodnota **EventGridSchema**. Události, které neodpovídají očekávanému schématu, budou odmítnuty.

Předplatitelé také můžou nakonfigurovat schéma, ve kterém chtějí události doručovat. Je-li tento parametr zadán, výchozí hodnota je schéma tématu.
Schéma doručení v současné době musí odpovídat schématu vstupu v tématu. 

## <a name="eventgrid-schema"></a>EventGrid schéma

Schéma EventGrid se skládá ze sady požadovaných vlastností, které musí entita publikování splňovat. Každý vydavatel musí vyplnit pole nejvyšší úrovně.

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

| Vlastnost | Typ | Vyžadováno | Popis |
| -------- | ---- | ----------- |-----------
| téma | řetězec | No | By se mělo shodovat s tématem, na kterém je publikovaná. Event Grid ji naplní názvem tématu, na kterém je publikovaná, pokud není zadaný. |
| závislosti | řetězec | Yes | Cesta k předmětu události, kterou definuje vydavatel. |
| eventType | řetězec | Yes | Typ události pro tento zdroj události, například BlobCreated. |
| eventTime | řetězec | Yes | Čas, kdy se událost generuje na základě času UTC poskytovatele. |
| ID | řetězec | No | Jedinečný identifikátor události |
| data | object | No | Slouží k zaznamenání dat události, která jsou specifická pro entitu publikování. |
| dataVersion | řetězec | Yes | Verze schématu datového objektu. Verzi schématu definuje vydavatel. |
| metadataVersion | řetězec | No | Verze schématu metadat události. Schéma vlastností nejvyšší úrovně definuje Event Grid. Tuto hodnotu poskytuje Event Grid. |

### <a name="example--eventgrid-schema-event"></a>Příklad: událost schématu EventGrid

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

## <a name="customevent-schema"></a>CustomEvent schéma

Ve vlastním schématu neexistují žádné povinné vlastnosti, které jsou vynutily jako EventGrid schéma. Publikování entity může zcela řídit schéma událostí. Poskytuje maximální flexibilitu a umožňuje scénáře, ve kterých už je systém založený na událostech, a chce znovu použít existující události a/nebo nechtít být svázané s konkrétním schématem.

### <a name="custom-schema-properties"></a>Vlastní vlastnosti schématu

Žádné povinné vlastnosti. Datovou část je možné určit až po publikování entity.

### <a name="example--custom-schema-event"></a>Příklad: vlastní událost schématu

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

## <a name="cloudevent-schema"></a>CloudEvent schéma

Kromě výše uvedených schémat Event Grid nativně podporuje události ve [schématu JSON pro CloudEvents](https://github.com/cloudevents/spec/blob/master/json-format.md). CloudEvents je otevřená specifikace popisující data události. Zjednodušuje interoperabilitu tím, že poskytuje společné schéma událostí pro publikování a využívání událostí. Je součástí [CNCF](https://www.cncf.io/) a aktuálně dostupné verze je 1,0-RC1.

### <a name="cloudevent-schema-properties"></a>Vlastnosti schématu CloudEvent

V povinných vlastnostech obálky najdete [CloudEvents Specification](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope) .

### <a name="example--cloud-event"></a>Příklad: cloudová událost
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
