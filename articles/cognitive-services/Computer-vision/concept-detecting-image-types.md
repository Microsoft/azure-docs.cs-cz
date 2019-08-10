---
title: Detekce typu obrázku – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty týkající se funkce detekce typu obrázku rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: e89fd977129113fa88af1acccd6b05f0bbe90243
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945217"
---
# <a name="detecting-image-types-with-computer-vision"></a>Detekce typů obrázků pomocí Počítačové zpracování obrazu

Pomocí rozhraní API pro [analýzu imagí](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) počítačové zpracování obrazu může analyzovat typ obsahu imagí, který označuje, jestli je obrázek Klipart nebo kreslení čáry.

## <a name="detecting-clip-art"></a>Detekce klipartu

Počítačové zpracování obrazu analyzuje obrázek a hodnotí pravděpodobnost obrázku, který je klipartem, na škále od 0 do 3, jak je popsáno v následující tabulce.

| Value | Význam |
|-------|---------|
| 0 | Není klipart |
| 1 | Jednoznačn |
| 2 | Normální – kliparty |
| 3 | Good-clip-art |

### <a name="clip-art-detection-examples"></a>Příklady Detekce klipartů

Následující odpovědi JSON znázorňují, co Počítačové zpracování obrazu vrátí při hodnocení pravděpodobnosti, že se v ukázkových obrázcích vystřihují kliparty.

![Klipartový obrázek řezu sýra](./Images/cheese_clipart.png)

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

![Modrý dům a přední yard](./Images/house_yard.png)

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

## <a name="detecting-line-drawings"></a>Zjišťování kreseb čar

Počítačové zpracování obrazu analyzuje obrázek a vrátí logickou hodnotu, která označuje, zda se jedná o spojnicové vykreslování.

### <a name="line-drawing-detection-examples"></a>Příklady detekce kreslení čáry

Následující odpovědi JSON znázorňují, co Počítačové zpracování obrazu vrátí, když značí, jestli jsou ukázkové obrázky řádky čáry.

![Obrázek čáry vykreslování Lion](./Images/lion_drawing.png)

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

![Bílá květina se zeleným pozadím](./Images/flower.png)

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

Podívejte se na referenční dokumentaci k [analýze imagí](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) , kde se dozvíte, jak detekovat typy obrázků.
