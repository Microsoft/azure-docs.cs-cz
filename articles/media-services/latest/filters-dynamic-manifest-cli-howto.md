---
title: Použití příkazového příkazového příkazu k vytváření filtrů pomocí služby Azure Media Services| Dokumenty společnosti Microsoft
description: Tento článek ukazuje, jak pomocí příkazového příkazového příkazu vytvořit filtry pomocí Azure Media Services v3.
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
ms.openlocfilehash: 74516aa921e45917f327a193a1c972b021c9c8ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896072"
---
# <a name="creating-filters-with-cli"></a>Vytváření filtrů pomocí cli 

Při doručování obsahu zákazníkům (streamování živých událostí nebo videa na vyžádání) může váš klient potřebovat větší flexibilitu, než je popsáno ve výchozím souboru manifestu datového zdroje. Azure Media Services umožňuje definovat filtry účtů a filtry datových zdrojů pro váš obsah. 

Podrobný popis této funkce a scénářů, kde se používá, naleznete [v tématu Dynamické manifesty](filters-dynamic-manifest-overview.md) a [filtry](filters-concept.md).

Toto téma ukazuje, jak nakonfigurovat filtr pro datový zdroj Video na vyžádání a použít rozhraní příkazového příkazu pro mediální služby v3 k vytvoření [filtrů účtů](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) a [filtrů datových zdrojů](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

> [!NOTE]
> Ujistěte se, že [zkontrolujte prezentaciTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Požadavky 

- [Vytvořte účet mediálních služeb](create-account-cli-how-to.md). Nezapomeňte si zapamatovat název skupiny prostředků a název účtu Mediální služby. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>Definování filtru 

Následující příklad definuje podmínky výběru stopy, které jsou přidány do konečného manifestu. Tento filtr obsahuje všechny zvukové stopy, které jsou EC-3 a všechny video stopy, které mají přenosový tok v rozsahu 0-1000000.

> [!TIP]
> Pokud máte v plánu definovat **filtry** v REST, všimněte si, že je třeba zahrnout "Vlastnosti" obálka JSON objektu.  

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

## <a name="create-account-filters"></a>Vytvořit filtry účtů

Následující příkaz [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) vytvoří filtr účtu s výběry stop filtrů, které byly [definovány dříve](#define-a-filter). 

Příkaz umožňuje předat volitelný `--tracks` parametr, který obsahuje JSON představující výběr y stopy.  Pomocí @{file} načtěte json ze souboru. Pokud používáte azure CLI místně, zadejte celou cestu k souboru:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Viz také [příklady JSON pro filtry](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Vytvořit filtry datových zdrojů

Následující příkaz [filtru majetku az ams](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) vytvoří filtr majetku s výběry stop filtrů, které byly [definovány dříve](#define-a-filter). 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Viz také [příklady JSON pro filtry](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter).

## <a name="associate-filters-with-streaming-locator"></a>Přidružení filtrů k lokátoru streamování

Můžete zadat seznam datových zdrojů nebo filtrů účtů, který by se vztahoval na lokátor streamování. [Dynamická packager (streaming endpoint)](dynamic-packaging-overview.md) použije tento seznam filtrů spolu s těmi, které váš klient určí v adrese URL. Tato kombinace generuje [dynamický manifest](filters-dynamic-manifest-overview.md), který je založen na filtrech v url + filtrech, které zadáte v lokátoru streamování. Doporučujeme použít tuto funkci, pokud chcete použít filtry, ale nechcete vystavit názvy filtrů v adrese URL.

Následující kód příkazového příkazu ukazuje, jak vytvořit `filters`lokátor streamování a určit . Jedná se o volitelnou vlastnost, která přebírá seznam názvů filtrů majetku a názvů filtrů nebo filtrů účtů oddělených místy.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>Streamování pomocí filtrů

Jakmile definujete filtry, vaši klienti je mohou použít v adrese URL streamování. Filtry lze použít pro adaptivní protokoly datových proudů: Apple HTTP Live Streaming (HLS), MPEG-DASH a Smooth Streaming.

V následující tabulce jsou uvedeny některé příklady adres URL s filtry:

|Protocol (Protokol)|Příklad|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Technologie Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>Další krok

[Streamování videí](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Viz také

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
