---
title: Popisy obrázků - Počítačové vidění
titleSuffix: Azure Cognitive Services
description: Koncepty související s funkcí popisu obrázku rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 7bf95a2b49608ef1f031a3b443db92b42cdae624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244711"
---
# <a name="describe-images-with-human-readable-language"></a>Popis obrázků pomocí jazyka čitelného pro člověka

Počítačové vidění může analyzovat obraz a generovat lidsky čitelnou větu, která popisuje jeho obsah. Algoritmus ve skutečnosti vrátí několik popisů na základě různých vizuálních funkcí a každý popis je uveden skóre spolehlivosti. Konečný výstup je seznam popisů seřazených od nejvyšší po nejnižší spolehlivost.

## <a name="image-description-example"></a>Příklad popisu obrázku

Následující odpověď JSON ilustruje, co počítačové vidění vrátí při popisu ukázkový obrázek na základě jeho vizuální funkce.

![Černobílý obraz budov na Manhattanu](./Images/bw_buildings.png)

```json
{
    "description": {
        "tags": ["outdoor", "building", "photo", "city", "white", "black", "large", "sitting", "old", "water", "skyscraper", "many", "boat", "river", "group", "street", "people", "field", "tall", "bird", "standing"],
        "captions": [
            {
                "text": "a black and white photo of a city",
                "confidence": 0.95301952483304808
            },
            {
                "text": "a black and white photo of a large city",
                "confidence": 0.94085190563213816
            },
            {
                "text": "a large white building in a city",
                "confidence": 0.93108362931954824
            }
        ]
    },
    "requestId": "b20bfc83-fb25-4b8d-a3f8-b2a1f084b159",
    "metadata": {
        "height": 300,
        "width": 239,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>Použití rozhraní API

Funkce popisu obrázku je součástí rozhraní Analyzovat rozhraní API [pro obrázky.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Toto rozhraní API můžete volat prostřednictvím nativní sady SDK nebo prostřednictvím volání REST. Zahrnout `Description` do parametru dotazu **visualFeatures.** Potom, když získáte úplnou odpověď JSON, jednoduše analyzovat řetězec `"description"` pro obsah oddílu.

* [Úvodní příručka: Počítačové zpracování počítače .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Úvodní příručka: Analýza obrázku (REST API)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>Další kroky

Naučte se související koncepty [označování obrázků](concept-tagging-images.md) a [kategorizace obrázků](concept-categorizing-images.md).
