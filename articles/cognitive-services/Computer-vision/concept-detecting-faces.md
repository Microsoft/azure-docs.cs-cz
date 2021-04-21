---
title: Detekce tváře – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Přečtěte si o konceptech souvisejících s funkcí detekce obličeje rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: bfb352c68b910a114e13041da4e8e86529e52040
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778836"
---
# <a name="face-detection-with-computer-vision"></a>Rozpoznávání tváře pomocí Počítačové zpracování obrazu

Počítačové zpracování obrazu dokáže detekovat lidské obličeje v rámci obrázku a generovat věk, pohlaví a obdélník pro každou zjištěnou plochu. 

> [!NOTE]
> Tuto funkci nabízí i služba Azure [Face](../face/index.yml) . Viz Tato alternativa pro podrobnější analýzu obličeje, včetně identifikace obličeje a detekce pozice. 

## <a name="face-detection-examples"></a>Příklady detekce obličeje

Následující příklad ukazuje odpověď JSON vrácenou Počítačové zpracování obrazu pro obrázek obsahující jednu lidskou plochu.

![Vizuální analýza ženy na střeše](./Images/woman_roof_face.png)

```json
{
    "faces": [
        {
            "age": 23,
            "gender": "Female",
            "faceRectangle": {
                "top": 45,
                "left": 194,
                "width": 44,
                "height": 44
            }
        }
    ],
    "requestId": "8439ba87-de65-441b-a0f1-c85913157ecd",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Png"
    }
}
```

Následující příklad ukazuje odpověď JSON vrácenou pro obrázek obsahující více lidských plošek.

![Fotografická analýza rodiny](./Images/family_photo_face.png)

```json
{
    "faces": [
        {
            "age": 11,
            "gender": "Male",
            "faceRectangle": {
                "top": 62,
                "left": 22,
                "width": 45,
                "height": 45
            }
        },
        {
            "age": 11,
            "gender": "Female",
            "faceRectangle": {
                "top": 127,
                "left": 240,
                "width": 42,
                "height": 42
            }
        },
        {
            "age": 37,
            "gender": "Female",
            "faceRectangle": {
                "top": 55,
                "left": 200,
                "width": 41,
                "height": 41
            }
        },
        {
            "age": 41,
            "gender": "Male",
            "faceRectangle": {
                "top": 45,
                "left": 103,
                "width": 39,
                "height": 39
            }
        }
    ],
    "requestId": "3a383cbe-1a05-4104-9ce7-1b5cf352b239",
    "metadata": {
        "height": 230,
        "width": 300,
        "format": "Png"
    }
}
```

## <a name="use-the-api"></a>Použití rozhraní API

Funkce detekce tváře je součástí rozhraní API pro [analýzu obrazu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) . Toto rozhraní API můžete zavolat prostřednictvím nativní sady SDK nebo prostřednictvím volání REST. `Faces`Do parametru dotazu **visualFeatures** zahrňte. Až získáte úplnou odpověď ve formátu JSON, stačí analyzovat řetězec pro obsah `"faces"` oddílu.

* [Rychlý Start: Počítačové zpracování obrazu REST API nebo klientské knihovny](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
