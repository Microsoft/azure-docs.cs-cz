---
title: Škálování média rezervované jednotky – Azure | Dokumentace Microsoftu
description: V tomto tématu je uveden přehled škálování zpracování médií pomocí Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: juliako
ms.openlocfilehash: 9099429097efb17629e88318430f004f0f763cc5
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52336785"
---
# <a name="creating-filters-with-cli"></a>Vytváření filtrů pomocí rozhraní příkazového řádku 

Při doručování obsahu zákazníkům (streamování živých událostí a videa na vyžádání), váš klient může být nutné více flexibility než co je popsána v souboru manifestu výchozí asset. Azure Media Services umožňuje definovat účtu filtry a filtry asset pro obsah. Další informace najdete v tématu [filtrů a dynamických manifestů](filters-dynamic-manifest-overview.md).

Toto téma ukazuje, jak konfigurovat filtr pro prostředek videa na vyžádání a vytvářejte pomocí rozhraní příkazového řádku pro Media Services v3 [filtrů účtů](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) a [Asset filtry](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

## <a name="prerequisites"></a>Požadavky 

- Nainstalovat a používat rozhraní příkazového řádku místně, musíte mít Azure CLI verze 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli). 

    V současné době všechny [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) příkazy fungují ve službě Azure Cloud Shell. Doporučujeme používat rozhraní příkazového řádku místně.
- [Vytvoření účtu Media Services](create-account-cli-how-to.md). Ujistěte se, že si pamatovat název skupiny prostředků a název účtu Media Services. 
- Kontrola [filtrů a dynamických manifestů](filters-dynamic-manifest-overview.md).

## <a name="define-a-filter"></a>Definujte filtr 

Následující příklad definuje podmínky výběr sledování, které jsou přidány do konečné manifestu. Tento filtr obsahuje všechny zvukové stopy, které jsou anglické s ES-3 a jakékoli video sledují, které mají s přenosovou rychlostí v 0 – 1 000 000 rozsahu.

Filtry definované v klidu, obsahovat objekt JSON "Properties" obálky.  

```json
[
    {
        "trackSelections": [
            {
                "property": "Type",
                "value": "Audio",
                "operation": "Equal"
            },
            {
                "property": "Language",
                "value": "en",
                "operation": "Equal"
            },
            {
                "property": "FourCC",
                "value": "EC-3",
                "operation": "NotEqual"
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
```

## <a name="create-account-filters"></a>Vytvoření účtu filtrů

Následující [účtu ams az-filtr](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) příkaz vytvoří filtr účet s filtrem sledování výběry, které byly [definovali dříve](#define-a-filter). 

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @C:\tracks.json
```

Viz také [příklady JSON pro filtry](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Vytvoření assetu filtrů

Následující [az ams asset filtrování](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) příkaz vytvoří filtr asset s filtrem sledování výběry, které byly [definovali dříve](#define-a-filter). 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @C:\tracks.json
```

Viz také [příklady JSON pro filtry](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter).

## <a name="next-step"></a>Další krok

[Stream videa](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Další informace najdete v tématech

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
