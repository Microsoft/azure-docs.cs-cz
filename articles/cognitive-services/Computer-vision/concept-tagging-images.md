---
title: Značky obsahu - Počítačové vidění
titleSuffix: Azure Cognitive Services
description: Seznamte se s koncepty souvisejícími s funkcí označování obrázků rozhraním Api pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 887f6d9163f800ecbe8a5ab9a0715fd171f1d3d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244507"
---
# <a name="applying-content-tags-to-images"></a>Používání značek obsahu pro obrázky

Počítačové vidění vrací značky založené na tisících rozpoznatelných objektů, živých bytostí, scenérie a akcí. Pokud jsou značky nejasné nebo nejsou obecně známé, nabídne API v odpovědi nápovědu, která objasní význam značky v kontextu známého prostředí. Značky nejsou uspořádané podle taxonomie a neexistují hierarchie dědění. Shromážděné značky obsahu tvoří základ „popisu“ obrázku, který se zobrazí v čitelném jazyce formátovaném do celých vět. V tuto chvíli je jediným podporovaným jazykem používaným k popisu obrázku angličtina.

Po načtení obrázku nebo zadání jeho adresy URL zpracuje algoritmus počítačového zpracování obrazu výstupní značky na základě identifikovaných objektů, živých bytostí a akcí. Značky se neomezují na hlavní předmět, například postavu v popředí, ale zahrnují také prostředí (interiér nebo exteriér), nábytek, nástroje, rostliny, zvířata, příslušenství, pomůcky atd.

## <a name="image-tagging-example"></a>Příklad označování obrázků

Následující odpověď JSON ilustruje, co počítačová vize vrátí při označování vizuálních funkcí zjištěných v ukázkovém obrázku.

![Modrý dům a předzahrádka](./Images/house_yard.png).

```json
{
    "tags": [
        {
            "name": "grass",
            "confidence": 0.9999995231628418
        },
        {
            "name": "outdoor",
            "confidence": 0.99992108345031738
        },
        {
            "name": "house",
            "confidence": 0.99685388803482056
        },
        {
            "name": "sky",
            "confidence": 0.99532157182693481
        },
        {
            "name": "building",
            "confidence": 0.99436837434768677
        },
        {
            "name": "tree",
            "confidence": 0.98880356550216675
        },
        {
            "name": "lawn",
            "confidence": 0.788884699344635
        },
        {
            "name": "green",
            "confidence": 0.71250593662261963
        },
        {
            "name": "residential",
            "confidence": 0.70859086513519287
        },
        {
            "name": "grassy",
            "confidence": 0.46624681353569031
        }
    ],
    "requestId": "06f39352-e445-42dc-96fb-0a1288ad9cf1",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>Použití rozhraní API

Funkce označování je součástí rozhraní Analyzovat rozhraní API [pro obrázky.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Toto rozhraní API můžete volat prostřednictvím nativní sady SDK nebo prostřednictvím volání REST. Zahrnout `Tags` do parametru dotazu **visualFeatures.** Potom, když získáte úplnou odpověď JSON, jednoduše analyzovat řetězec `"tags"` pro obsah oddílu.

* [Úvodní příručka: Počítačové zpracování počítače .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Úvodní příručka: Analýza obrázku (REST API)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>Další kroky

Naučte se související pojmy [kategorizace obrázků](concept-categorizing-images.md) a [popisu obrázků](concept-describing-images.md).
