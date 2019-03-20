---
title: Zjišťování typy obrázků – pro počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty související s funkcí zjišťování typ bitové kopie z rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 37cdac16a51a30bdaf1ba0266bab7fdd1f2990f0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57895443"
---
# <a name="detecting-image-types-with-computer-vision"></a>Zjišťování typy obrázků s pro počítačové zpracování obrazu

S [analyzovat Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) rozhraní API, pro počítačové zpracování obrazu můžete analyzovat typ obsahu, obrázků, určující, zda je bitové klipart nebo perokresba.

## <a name="detecting-clip-art"></a>Zjišťování klipart

Počítačové zpracování obrazu analyzuje bitovou kopii a vyhodnotí pravděpodobnost, že image klipart na škále od 0 do 3, jak je popsáno v následující tabulce.

| Hodnota | Význam |
|-------|---------|
| 0 | Není klipart |
| 1 | Nejednoznačný |
| 2 | Normální klipart |
| 3 | Good-clip-art |

### <a name="clip-art-detection-examples"></a>Oříznout příklady detekce obrázky

Následující odpověďmi ve formátu JSON ukazuje, co pro počítačové zpracování obrazu vrací při hodnocení pravděpodobnost, že Image příklad se klipart.

![Klipartu určitý řez produkci minipivovarů ve wisconsinu](./Images/cheese_clipart.png)

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

![Modré house a front-yard](./Images/house_yard.png)

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

![Perokresba snímek lion](./Images/lion_drawing.png)

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

![Bílé květinu zeleným pozadím](./Images/flower.png)

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

Zobrazit [analyzovat Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) referenční dokumentace se naučíte zjistit tyto typy obrázků.
