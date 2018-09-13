---
title: Zjišťování typy obrázků
titleSuffix: Computer Vision - Cognitive Services - Azure
description: Koncepty související se zjišťováním typy obrázků pomocí služeb Azure Cognitive Services pro počítačové zpracování obrazu.
services: cognitive-services
author: deken
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 6c0280959e82eaa2da4927b48af7ebc28db25a7d
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44725372"
---
# <a name="detecting-image-types"></a>Zjišťování typy obrázků

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