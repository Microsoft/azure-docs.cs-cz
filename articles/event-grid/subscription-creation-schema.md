---
title: Schéma předplatné Azure událostí mřížky
description: Popisuje vlastnosti odběru pro událost s událostí mřížky Azure.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 05/02/2018
ms.author: babanisa
ms.openlocfilehash: cfb4dabea12f2988108d24b025e324cf05afb325
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34301715"
---
# <a name="event-grid-subscription-schema"></a>Schématu předplatné mřížky události

Pokud chcete vytvořit předplatné událostí mřížky, odeslat požadavek na operaci vytvoření události odběru. Použijte následující formát:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Můžete například vytvořit odběr událostí pro účet úložiště s názvem `examplestorage` ve skupině prostředků s názvem `examplegroup`, použijte následující formát:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Článek popisuje vlastnosti a schéma pro text žádosti.
 
## <a name="event-subscription-properties"></a>Vlastnosti odběru událostí

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| Cílový | objekt | Objekt, který definuje koncový bod. |
| Filtr | objekt | Volitelné pole pro filtrování typy událostí. |

### <a name="destination-object"></a>cílový objekt

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| endpointType | řetězec | Typ koncového bodu pro předplatné (webhooku nebo HTTP, centra událostí nebo fronty). | 
| Adresa URL koncového bodu | řetězec | Cílová adresa URL pro události se v tomto předplatném událostí. | 

### <a name="filter-object"></a>objekt filtru

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| includedEventTypes | pole | Shoda, když typ události ve zprávě události je přesnou shodou jeden z těchto názvů typu události. Vyvolá chybu, pokud název události neodpovídá typu názvy registrovaných událostí pro zdroj události. Výchozí odpovídá všechny typy událostí. |
| subjectBeginsWith | řetězec | Shoda předpony filtrovat pole předmět události zprávy. Výchozí nebo prázdný řetězec odpovídá všem. | 
| subjectEndsWith | řetězec | Přípona match filtrovat pole předmět události zprávy. Výchozí nebo prázdný řetězec odpovídá všem. |
| isSubjectCaseSensitive | řetězec | Ovládací prvky malá a velká písmena odpovídající pro filtry. |


## <a name="example-subscription-schema"></a>Příklad předplatné schématu

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "Microsoft.Storage.BlobCreated", "Microsoft.Storage.BlobDeleted" ],
      "subjectBeginsWith": "blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "isSubjectCaseSensitive ": "true"
    }
  }
}
```

## <a name="next-steps"></a>Další postup

* Úvod k mřížce událostí, naleznete v části [co je mřížky událostí?](overview.md)