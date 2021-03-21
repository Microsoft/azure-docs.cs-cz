---
title: Získat všechny rozhraní API datových sad
description: Pomocí tohoto rozhraní API získáte všechny dostupné datové sady pro komerční analýzu na webu Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 23aac2c94ffd909ca132cbc481998b9eda317156
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583728"
---
# <a name="get-all-datasets-api"></a>Získat všechny rozhraní API datových sad

Rozhraní API získat všechny datové sady získá všechny dostupné datové sady. Datové sady vypíše tabulky, sloupce, metriky a časové rozsahy.

**Syntaxe žádosti**

| **Metoda** | **Identifikátor URI žádosti** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset?datasetName={Dataset Name}` |

**Hlavička žádosti**

| **Hlavička** | **Typ** | **Popis** |
| --- | --- | --- |
| Autorizace | řetězec | Povinná hodnota. Přístupový token Azure Active Directory (Azure AD) ve formuláři `Bearer <token>` |
| Typ obsahu | řetězec | `Application/JSON` |

**Parametr cesty**

Žádné

**Parametr dotazu**

| **Název parametru** | **Typ** | **Povinné** | **Popis** |
| --- | --- | --- | --- |
| `datasetName` | řetězec | No | Filtr, aby se získaly podrobnosti jenom pro jednu datovou sadu |

**Datová část požadavku**

Žádné

**Glosář**

Žádné

**Response** (Odpověď)

Datová část odpovědi je strukturována takto:

Kód odpovědi: 200, 400, 401, 403, 404, 500

Příklad datové části odpovědi:

```json
{
   "Value":[
      {
         "DatasetName ":"string",
         "SelectableColumns":[
            "string"
         ],
         "AvailableMetrics":[
            "string"
         ],
         "AvailableDateRanges ":[
            "string"
         ]
      }
   ],
   "TotalCount":int,
   "Message":"<Error Message>",
   "StatusCode": int
}
```

**Glosář**

Tato tabulka definuje klíčové prvky v odpovědi:

| **Parametr** | **Popis** |
| --- | --- |
| `DatasetName` | Název datové sady, kterou tento objekt pole definuje |
| `SelectableColumns` | Nezpracované sloupce, které se dají zadat ve sloupcích pro výběr |
| `AvailableMetrics` | Názvy sloupců agregace a metriky, které lze zadat ve sloupcích výběr |
| `AvailableDateRanges` | Rozsah dat, který lze použít v dotazech sestavy pro datovou sadu |
| `NextLink` | Odkaz na další stránku, pokud jsou data na stránkování |
| `TotalCount` | Počet datových sad v poli hodnot |
| `StatusCode` | Kód výsledku. Možné hodnoty jsou 200, 400, 401, 403, 500 |
