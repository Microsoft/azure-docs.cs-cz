---
title: Vytváření filtrů pomocí Azure Media Services pomocí rozhraní příkazového řádku | Dokumentace Microsoftu
description: Toto téma ukazuje, jak vytváření filtrů pomocí Media Services pomocí rozhraní příkazového řádku.
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
ms.custom: seodec18
ms.openlocfilehash: 5aa617edf13aee9c5899a59c46aeb729f202719f
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53744203"
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

Následující příklad definuje podmínky výběr sledování, které jsou přidány do konečné manifestu. Tento filtr obsahuje všechny zvukové stopy, které jsou ES-3 a jakékoli video sledují, které mají s přenosovou rychlostí v 0 – 1 000 000 rozsahu.

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

Následující příkaz povolí možnost `--tracks` , který přijme soubor. Pokud používáte Azure CLI místně, zadejte cestu celý soubor:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @c:\tracks.json
```

Pokud používáte Azure Cloud Shell, nahrajte soubor do Cloud Shellu (hledání tlačítka nahrávání a stahování souborů v horní části okna prostředí). Potom můžete odkazovat souboru následujícím způsobem:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Viz také [příklady JSON pro filtry](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Vytvoření assetu filtrů

Následující [az ams asset filtrování](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) příkaz vytvoří filtr asset s filtrem sledování výběry, které byly [definovali dříve](#define-a-filter). 

> [!TIP]
> Zobrazí informace o určení umístění názvu souboru v předchozí části.

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Viz také [příklady JSON pro filtry](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter).

## <a name="next-step"></a>Další krok

[Stream videa](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Další informace najdete v tématech

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
