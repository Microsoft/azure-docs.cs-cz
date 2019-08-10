---
title: Značky obsahu – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Přečtěte si o konceptech souvisejících s funkcí označování imagí v rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ad01751f7f7c573352c8fd15cc0f8cc2ebbbe700
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945107"
---
# <a name="applying-content-tags-to-images"></a>Používání značek obsahu pro obrázky

Počítačové zpracování obrazu vrací značky založené na tisících rozpoznatelných objektů, živých místech, krajin a akcí. Pokud jsou značky nejasné nebo nejsou obecně známé, nabídne API v odpovědi nápovědu, která objasní význam značky v kontextu známého prostředí. Značky nejsou uspořádané podle taxonomie a neexistují hierarchie dědění. Shromážděné značky obsahu tvoří základ „popisu“ obrázku, který se zobrazí v čitelném jazyce formátovaném do celých vět. V tuto chvíli je jediným podporovaným jazykem používaným k popisu obrázku angličtina.

Po nahrání obrázku nebo zadání adresy URL obrázku Počítačové zpracování obrazu výstupní značky algoritmů na základě objektů, živých a akcí identifikovaných v imagi. Značky se neomezují na hlavní předmět, například postavu v popředí, ale zahrnují také prostředí (interiér nebo exteriér), nábytek, nástroje, rostliny, zvířata, příslušenství, pomůcky atd.

## <a name="image-tagging-example"></a>Příklad označení obrázku

Následující odpověď JSON ukazuje, co Počítačové zpracování obrazu vrátí při označování vizuálních funkcí zjištěných v příkladu obrázku.

![Modrý dům a přední yard](./Images/house_yard.png).

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

## <a name="next-steps"></a>Další postup

Přečtěte si o konceptech [kategorizace obrázků](concept-categorizing-images.md) a [popisů obrázků](concept-describing-images.md).
