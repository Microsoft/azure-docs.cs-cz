---
title: Vytváření filtrů pomocí Azure Media Services – rozhraní REST API | Dokumentace Microsoftu
description: Toto téma popisuje, jak vytvářet filtry, takže klient může používat na určité části datový proud stream. Služba Media Services vytvoří dynamických manifestů k selektivní Streamovat.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 12/17/2018
ms.author: juliako
ms.openlocfilehash: 32b9664d12d6fe3a44329665c730dbc8709430f2
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2018
ms.locfileid: "53650837"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Vytváření filtrů pomocí REST API služby Media Services

Při doručování obsahu zákazníkům (streamování živých událostí a videa na vyžádání) vašeho klienta může být nutné více flexibility než co je popsána v souboru manifestu výchozí asset. Azure Media Services umožňuje definovat účtu filtry a filtry asset pro obsah. Další informace najdete v tématu [filtrů a dynamických manifestů](filters-dynamic-manifest-overview.md).

Toto téma ukazuje, jak definovat filtr pro Video na vyžádání assetu a vytvořit pomocí rozhraní REST API [filtrů účtů](https://docs.microsoft.com/rest/api/media/accountfilters) a [Asset filtry](https://docs.microsoft.com/rest/api/media/assetfilters). 

## <a name="prerequisites"></a>Požadavky 

K dokončení kroků popsaných v tomto tématu, budete muset:

- Kontrola [filtrů a dynamických manifestů](filters-dynamic-manifest-overview.md).
- [Konfigurace nástroje Postman pro volání REST API služby Azure Media Services](media-rest-apis-with-postman.md).

    Ujistěte se, že chcete postupovat podle poslední krok v tomto tématu [získat Azure AD na Token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-filter"></a>Definujte filtr  

Tady je **text žádosti** příklad, který definuje podmínky výběr sledování, které jsou přidány do manifestu. Tento filtr obsahuje všechny zvukové stopy, které jsou ES-3 a jakékoli video sledují, které mají s přenosovou rychlostí v 0 – 1 000 000 rozsahu.

```json
{
    "properties": {
        "tracks": [
          {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Audio",
                        "operation": "Equal"
                    },
                    {
                        "property": "FourCC",
                        "value": "EC-3",
                        "operation": "Equal"
                    }
                ]
            },
            {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Video",
                        "operation": "Equal"
                    },
                    {
                        "property": "Bitrate",
                        "value": "0-1000000",
                        "operation": "Equal"
                    }
                ]
            }
        ]
     }
}
```

## <a name="create-account-filters"></a>Vytvoření účtu filtrů

Postman collection, který jste stáhli, vyberte **filtrů účtů**->**vytvořit nebo aktualizovat filtr účet**.

**UMÍSTIT** metoda požadavku HTTP je podobný:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Vyberte **tělo** kartu a vložte kód json [definovali dříve](#define-a-filter).

Vyberte **Poslat**. 

Filtr se vytvořil.

Další informace najdete v tématu [vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate). Viz také [příklady JSON pro filtry](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Vytvoření assetu filtrů  

V kolekci Postman "Služby Media Services v3", který jste stáhli, vyberte **prostředky**->**vytvořit nebo aktualizovat filtr Asset**.

**UMÍSTIT** metoda požadavku HTTP je podobný:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Vyberte **tělo** kartu a vložte kód json [definovali dříve](#define-a-filter).

Vyberte **Poslat**. 

Filtr prostředků se vytvořila.

Podrobnosti o tom, jak vytvořit nebo aktualizovat prostředek filtry najdete v tématu [vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate). Viz také [příklady JSON pro filtry](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter). 

## <a name="next-steps"></a>Další postup

[Stream videa](stream-files-tutorial-with-rest.md) 
