---
title: Zjišťování typy obrázků – pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty související se zjišťováním typy obrázků pomocí rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: ecdbdd60af41ee14070f2f45dfe50875e38ac3b3
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341993"
---
# <a name="detecting-image-types"></a>Rozpoznávání typů obrázků

Počítačové zpracování obrazu můžete analyzovat typ obsahu imagí podle určující, zda je bitové klipart, hodnocení pravděpodobnost, že na škálovací nebo perokresba.

## <a name="detecting-clip-art"></a>Zjišťování klipart

Počítačové zpracování obrazu analyzuje bitovou kopii a vyhodnotí pravděpodobnost, že image klipart na škále od 0 do 3, jak je popsáno v následující tabulce.

| Hodnota | Význam |
|-------|---------|
| 0 | Bez klipart |
| 1 | Nejednoznačný |
| 2 | Normální klipart |
| 3 | Dobré klipart |

### <a name="clip-art-detection-examples"></a>Oříznout příklady detekce obrázky

Následující odpověďmi ve formátu JSON ukazuje, co pro počítačové zpracování obrazu vrací při hodnocení pravděpodobnost, že Image příklad se klipart.

![Pro zpracování obrazu analyzovat produkci minipivovarů ve Wisconsinu klipart](./Images/cheese_clipart.png)

```json
{
    "imageType": {
        "clipArtType": 3,
        "lineDrawingType": 0
    },
    "requestId": "88c48d8c-80f3-449f-878f-6947f3b35a27",
    "metadata": {
        "height": 225,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Pro zpracování obrazu analyzovat House Yard](./Images/house_yard.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "a9c8490a-2740-4e04-923b-e8f4830d0e47",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="detecting-line-drawings"></a>Zjišťování kresby

Počítačové zpracování obrazu analyzuje bitovou kopii a vrátí hodnotu typu boolean označující, jestli obrázek je Perokresba.

### <a name="line-drawing-detection-examples"></a>Příklady detekce Perokresba

Následující odpověďmi ve formátu JSON ukazuje, co pro počítačové zpracování obrazu vrací při určování, zda jsou obrázky příklad kreslení čáry.

![Pro zpracování obrazu analyzovat Lion kreslení](./Images/lion_drawing.png)

```json
{
    "imageType": {
        "clipArtType": 2,
        "lineDrawingType": 1
    },
    "requestId": "6442dc22-476a-41c4-aa3d-9ceb15172f01",
    "metadata": {
        "height": 268,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Analýza květinu pro zpracování obrazu](./Images/flower.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "98437d65-1b05-4ab7-b439-7098b5dfdcbf",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>Další postup

Seznamte se s koncepty [označování imagí](concept-tagging-images.md) a [kategorizace obrázků](concept-categorizing-images.md).