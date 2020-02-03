---
title: Schéma předplatného služby Azure Event Grid
description: Tento článek popisuje vlastnosti pro přihlášení k odběru události s Azure Event Grid. Schéma předplatného Event Grid.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: reference
ms.date: 01/23/2020
ms.author: babanisa
ms.openlocfilehash: 4bb04d22b762f31a02515549b698030a5267e4cd
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720754"
---
# <a name="event-grid-subscription-schema"></a>Schéma předplatného Event Grid

Pokud chcete vytvořit odběr Event gridu, odeslat požadavek na operaci předplatného vytvořit událost. Použijte následující formát:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Pokud například chcete vytvořit odběr událostí pro účet úložiště s názvem `examplestorage` ve skupině prostředků s názvem `examplegroup`, použijte následující formát:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Název odběru události musí být dlouhý 3 až 64 znaků a může obsahovat pouze a – z, A-Z, 0-9, a "-". Tento článek popisuje vlastnosti a schéma pro text žádosti.
 
## <a name="event-subscription-properties"></a>Vlastnosti odběru událostí

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| cíl | objekt | Objekt, který definuje koncový bod. |
| Filter | objekt | Volitelné pole k filtrování typů událostí. |

### <a name="destination-object"></a>cílový objekt

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| endpointType | řetězec | Typ koncového bodu pro předplatné (webhook/HTTP, Centrum událostí nebo fronty). | 
| endpointUrl | řetězec | Cílová adresa URL pro události v tento odběr události. | 

### <a name="filter-object"></a>objekt filtru

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| includedEventTypes | pole | Shoda, když typ události ve zprávě události je přesnou shodu na jednu z těchto názvů typu event. Vyvolá chybu, pokud název události se neshoduje s názvy typů registrované události pro zdroje událostí. Výchozí odpovídá všechny typy událostí. |
| subjectBeginsWith | řetězec | Shodu předpony filtr na pole Předmět v případě zprávy. Výchozí nebo prázdný řetězec odpovídá všem. | 
| subjectEndsWith | řetězec | Přípona match filtr na pole Předmět v případě zprávy. Výchozí nebo prázdný řetězec odpovídá všem. |
| isSubjectCaseSensitive | řetězec | Ovládací prvky malá a velká písmena odpovídající pro filtry. |


## <a name="example-subscription-schema"></a>Příklad schéma předplatného

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

* Úvod do Event Grid najdete v tématu [co je Event Grid?](overview.md)
