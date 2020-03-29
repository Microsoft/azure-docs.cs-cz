---
title: Schéma předplatného Azure Event Grid
description: Tento článek popisuje vlastnosti pro přihlášení k odběru události pomocí Azure Event Grid. Schéma předplatného programu Event Grid.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: reference
ms.date: 01/23/2020
ms.author: babanisa
ms.openlocfilehash: 4bb04d22b762f31a02515549b698030a5267e4cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720754"
---
# <a name="event-grid-subscription-schema"></a>Schéma předplatného programu Event Grid

Chcete-li vytvořit odběr mřížky událostí, odešlete požadavek na operaci vytvořit událost předplatného. Použijte tento formát:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Chcete-li například vytvořit odběr událostí `examplestorage` pro účet úložiště `examplegroup`s názvem ve skupině prostředků s názvem , použijte následující formát:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Název odběru události musí mít délku 3-64 znaků a může obsahovat pouze a-z, A-Z, 0-9 a "-". Článek popisuje vlastnosti a schéma pro tělo požadavku.
 
## <a name="event-subscription-properties"></a>Vlastnosti odběru událostí

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| destination | objekt | Objekt, který definuje koncový bod. |
| filtrování | objekt | Volitelné pole pro filtrování typů událostí. |

### <a name="destination-object"></a>cílový objekt

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| endpointType | řetězec | Typ koncového bodu pro odběr (webhook/HTTP, Event Hub nebo fronty). | 
| adresa koncového boduUrl | řetězec | Cílová adresa URL událostí v tomto předplatném události. | 

### <a name="filter-object"></a>objekt filtru

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| includedEventTypes | pole | Shoda, pokud typ události ve zprávě události je přesná shoda s jedním z těchto názvů typů událostí. Vyvolá chybu, když název události neodpovídá názvům typu registrované události pro zdroj události. Výchozí odpovídá všem typům událostí. |
| subjectBeginsWith | řetězec | Filtr shody předpony s polem předmětu ve zprávě události. Výchozí nebo prázdný řetězec odpovídá všem. | 
| subjectEndsWith | řetězec | Filtr shody přípony s polem předmětu ve zprávě události. Výchozí nebo prázdný řetězec odpovídá všem. |
| isSubjectCaseSensitive | řetězec | Řídí porovnávání filtrů rozlišujícímalá písmena. |


## <a name="example-subscription-schema"></a>Příklad schématu předplatného

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
      "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "isSubjectCaseSensitive ": "true"
    }
  }
}
```

## <a name="next-steps"></a>Další kroky

* Úvod do mřížky událostí najdete v tématu [Co je event grid?](overview.md)
