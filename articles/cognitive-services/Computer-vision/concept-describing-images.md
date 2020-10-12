---
title: Popisy obrázků – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty související s funkcí Popis obrázku rozhraní API pro počítačové zpracování obrazu.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80244711"
---
# <a name="describe-images-with-human-readable-language"></a>Popište Image pomocí uživatelsky čitelného jazyka.

Počítačové zpracování obrazu může analyzovat obrázek a generovat větu umožňující čtení, která popisuje jeho obsah. Algoritmus ve skutečnosti vrátí několik popisů založených na různých vizuálních funkcích a každý popis je dán skóre spolehlivosti. Konečný výstup je seznam popisů seřazených od nejvyšších po nejnižší spolehlivost.

## <a name="image-description-example"></a>Příklad popisu obrázku

Následující odpověď JSON ukazuje, co Počítačové zpracování obrazu vrátí, když popisují příklad obrázku na základě jeho vizuálních funkcí.

![Černý a bílý obrázek budov v Manhattan](./Images/bw_buildings.png)

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

Funkce Popis obrázku je součástí rozhraní API pro [analýzu obrazu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) . Toto rozhraní API můžete zavolat prostřednictvím nativní sady SDK nebo prostřednictvím volání REST. `Description`Do parametru dotazu **visualFeatures** zahrňte. Až získáte úplnou odpověď ve formátu JSON, stačí analyzovat řetězec pro obsah `"description"` oddílu.

* [Rychlý Start: Počítačové zpracování obrazu .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Rychlý Start: analýza obrázku (REST API)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si o souvisejících konceptech [označování obrázků](concept-tagging-images.md) a [kategorizaci imagí](concept-categorizing-images.md).
