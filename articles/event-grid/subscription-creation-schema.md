---
title: Schéma předplatného Azure Event Grid
description: Tento článek popisuje vlastnosti pro přihlášení k odběru události s Azure Event Grid. Schéma předplatného Event Grid.
ms.topic: reference
ms.date: 07/07/2020
ms.openlocfilehash: f6e33171cbba65cfeaca49ab6a8954be8bb89acb
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199947"
---
# <a name="event-grid-subscription-schema"></a>Schéma předplatného Event Grid

Pokud chcete vytvořit předplatné Event Grid, odešlete požadavek na operaci vytvoření odběru události. Použijte tento formát:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Pokud například chcete vytvořit odběr událostí pro účet úložiště s názvem `examplestorage` ve skupině prostředků s názvem `examplegroup` , použijte následující formát:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Název odběru události musí mít délku 3-64 znaků a může obsahovat jenom znaky a – z, A – Z, 0-9 a-. Článek popisuje vlastnosti a schéma pro tělo žádosti.
 
## <a name="event-subscription-properties"></a>Vlastnosti odběru událostí

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| destination | object | Objekt, který definuje koncový bod. |
| filter | object | Volitelné pole pro filtrování typů událostí. |

### <a name="destination-object"></a>cílový objekt

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| endpointType | řetězec | Typ koncového bodu pro předplatné (Webhook/HTTP, centrum událostí nebo fronta). | 
| endpointUrl | řetězec | Cílová adresa URL pro události v tomto předplatném události | 

### <a name="filter-object"></a>Filter – objekt

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| includedEventTypes | array | Odpovídá, pokud typ události ve zprávě události přesně odpovídá jednomu z těchto názvů typu události. Vyvolá chybu, pokud název události neodpovídá registrovaným názvům typů události pro daný zdroj události. Výchozí hodnota odpovídá všem typům událostí. |
| subjectBeginsWith | řetězec | Filtr se shodou předpony do pole předmětu ve zprávě události. Výchozí nebo prázdný řetězec odpovídá všem. | 
| subjectEndsWith | řetězec | Filtr shody přípon k poli předmětu ve zprávě události. Výchozí nebo prázdný řetězec odpovídá všem. |
| isSubjectCaseSensitive | řetězec | Ovládá porovnávání s rozlišováním velkých a malých písmen pro filtry. |
| enableAdvancedFilteringOnArrays | boolean | Povoluje použití polí pro klíče v rozšířeném filtrování. Další informace najdete v tématu [Rozšířené filtrování](event-filtering.md#advanced-filtering). |


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

* Úvod do Event Grid najdete v tématu [co je Event Grid?](overview.md)
