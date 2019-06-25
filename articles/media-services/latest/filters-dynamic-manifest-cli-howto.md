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
ms.date: 06/13/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: c6007b66e31996db5c6b043219470968a7b05031
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67114693"
---
# <a name="creating-filters-with-cli"></a>Vytváření filtrů pomocí rozhraní příkazového řádku 

Při doručování obsahu zákazníkům (streamování živých událostí a videa na vyžádání), váš klient může být nutné více flexibility než co je popsána v souboru manifestu výchozí asset. Azure Media Services umožňuje definovat účtu filtry a filtry asset pro obsah. 

Podrobný popis této funkce a scénáře, ve kterém se používá, najdete v části [dynamických manifestů](filters-dynamic-manifest-overview.md) a [filtry](filters-concept.md).

Toto téma ukazuje, jak konfigurovat filtr pro prostředek videa na vyžádání a vytvářejte pomocí rozhraní příkazového řádku pro Media Services v3 [filtrů účtů](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) a [Asset filtry](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

> [!NOTE]
> Přečtěte si [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Požadavky 

- [Vytvoření účtu Media Services](create-account-cli-how-to.md). Ujistěte se, že si pamatovat název skupiny prostředků a název účtu Media Services. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>Definujte filtr 

Následující příklad definuje podmínky výběr sledování, které jsou přidány do konečné manifestu. Tento filtr obsahuje všechny zvukové stopy, které jsou ES-3 a jakékoli video sledují, které mají s přenosovou rychlostí v 0 – 1 000 000 rozsahu.

> [!TIP]
> Pokud budete chtít definovat **filtry** REST, Všimněte si, že je potřeba zahrnout objekt JSON obálky "Properties".  

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

Tento příkaz umožňuje předat volitelně `--tracks` parametr, který obsahuje JSON představující možnosti sledování.  Použijte @{file} načíst ze souboru JSON. Pokud používáte Azure CLI místně, zadejte cestu celý soubor:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Viz také [příklady JSON pro filtry](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Vytvoření assetu filtrů

Následující [az ams asset filtrování](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) příkaz vytvoří filtr asset s filtrem sledování výběry, které byly [definovali dříve](#define-a-filter). 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Viz také [příklady JSON pro filtry](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter).

## <a name="associate-filters-with-streaming-locator"></a>Filtry přidružit Lokátor streamování

Můžete zadat seznam prostředků nebo účet filtrů, které pro vaše Lokátor streamování. [Dynamické Packager (koncový bod streamování)](dynamic-packaging-overview.md) platí tento seznam filtrů společně s ty klientem v adrese URL. Tato kombinace generuje [dynamické Manifest](filters-dynamic-manifest-overview.md), která je založena na filtry v adrese URL a filtry, které jste zadali na Lokátor streamování. Doporučujeme použít tuto funkci, pokud chcete použít filtry, ale nechcete, aby k vystavení filtr názvů v adrese URL.

Rozhraní příkazového řádku následující kód ukazuje, jak vytvořit lokátor streamování a určit `filters`. Toto je volitelná vlastnost, která přebírá místo oddělený seznam filtru názvy datových zdrojů a/nebo názvy účtů filtru.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>Stream pomocí filtrů

Jakmile definujete filtry, klienty je použít v adrese URL streamování. Filtry můžete uplatnit adaptivní přenosové rychlosti streamování protokolů: Apple HTTP Live Streaming (HLS), MPEG-DASH a Smooth Streaming.

V následující tabulce jsou uvedeny příklady adresy URL s filtry:

|Protocol|Příklad:|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Technologie Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>Další krok

[Stream videa](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Další informace najdete v tématech

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
