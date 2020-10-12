---
title: Pomocí rozhraní příkazového řádku můžete vytvářet filtry s Azure Media Services | Microsoft Docs
description: V tomto článku se dozvíte, jak pomocí rozhraní příkazového řádku vytvořit filtry s Azure Media Services V3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 5ceff9eba0363ea2784c48e026807c53447f1e32
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89296897"
---
# <a name="creating-filters-with-cli"></a>Vytváření filtrů pomocí rozhraní příkazového řádku

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Při doručování obsahu zákazníkům (streamování živých událostí nebo videa na vyžádání) může váš klient potřebovat větší flexibilitu než to, co je popsáno v souboru manifestu výchozího prostředku. Azure Media Services vám umožní definovat filtry účtu a filtry assetů pro váš obsah. 

Podrobný popis této funkce a scénářů, kde se používá, najdete v tématu [dynamické manifesty](filters-dynamic-manifest-overview.md) a [filtry](filters-concept.md).

V tomto tématu se dozvíte, jak nakonfigurovat filtr pro Asset na vyžádání a pomocí rozhraní příkazového řádku Media Services V3 vytvořit [filtry účtu](/cli/azure/ams/account-filter?view=azure-cli-latest) a [filtry assetů](/cli/azure/ams/asset-filter?view=azure-cli-latest). 

> [!NOTE]
> Nezapomeňte zkontrolovat [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Požadavky 

- [Vytvořte účet Media Services](./create-account-howto.md). Nezapomeňte si pamatovat název skupiny prostředků a název účtu Media Services. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>Definovat filtr 

Následující příklad definuje podmínky pro výběr sledování, které jsou přidány do konečného manifestu. Tento filtr zahrnuje všechny zvukové stopy, které jsou EC-3, a všechny videosoubory, které mají přenosovou rychlost v rozsahu 0-1000000.

> [!TIP]
> Pokud plánujete definovat **filtry** v REST, Všimněte si, že je nutné zahrnout objekt JSON s obálkou Properties (vlastnosti).  

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

## <a name="create-account-filters"></a>Vytváření filtrů účtů

Následující příkaz [AZ AMS Account-Filter](/cli/azure/ams/account-filter?view=azure-cli-latest) vytvoří filtr účtu s vybraným filtrem sledovat [výše definované výše](#define-a-filter). 

Příkaz umožňuje předat volitelný `--tracks` parametr, který obsahuje JSON, reprezentující výběry sledování.  K načtení JSON ze souboru použijte @ {File}. Pokud používáte Azure CLI místně, zadejte celou cestu k souboru:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Podívejte se také na [Příklady JSON pro filtry](/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Vytváření filtrů assetů

Následující příkaz [AZ AMS Asset-Filter](/cli/azure/ams/asset-filter?view=azure-cli-latest) vytvoří filtr assetů s vybraným filtrem sledovat výběry, které byly [definované dříve](#define-a-filter). 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Podívejte se také na [Příklady JSON pro filtry](/rest/api/media/assetfilters/createorupdate#create-an-asset-filter).

## <a name="associate-filters-with-streaming-locator"></a>Přidružit filtry k lokátoru streamování

Můžete určit seznam filtrů Asset nebo Account, které se vztahují na Lokátor streamování. [Dynamický balíček (koncový bod streamování)](dynamic-packaging-overview.md) používá tento seznam filtrů společně s nastavením, které klient ZADÁ v adrese URL. Tato kombinace generuje [dynamický manifest](filters-dynamic-manifest-overview.md), který je založen na filtrech v URL + filtry, které zadáte na lokátoru streamování. Tuto funkci doporučujeme používat, pokud chcete použít filtry, ale nechcete vystavit názvy filtrů v adrese URL.

Následující kód CLI ukazuje, jak vytvořit Lokátor streamování a zadat `filters` . Toto je volitelná vlastnost, která přijímá seznam názvů filtrů assetů oddělených mezerami nebo názvy filtrů účtů.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>Streamování pomocí filtrů

Po definování filtrů je můžou klienti používat v adrese URL streamování. Filtry mohou být aplikovány na protokoly streamování s adaptivní přenosovou rychlostí: Apple HTTP Live Streaming (HLS), MPEG-POMLČKa a Smooth Streaming.

V následující tabulce jsou uvedeny některé příklady adres URL s filtry:

|Protokol|Příklad|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Technologie Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>Další krok

[Streamování videí](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Viz také

[Azure CLI](/cli/azure/ams?view=azure-cli-latest)
