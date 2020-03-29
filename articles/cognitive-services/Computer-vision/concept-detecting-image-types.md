---
title: Detekce typu obrazu - Počítačové vidění
titleSuffix: Azure Cognitive Services
description: Koncepty související s funkcí detekce typu obrazu rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4e6c2db5333962d7ae43534998ffc1c48b0dba45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244558"
---
# <a name="detecting-image-types-with-computer-vision"></a>Detekce typů obrazů pomocí počítačového vidění

Pomocí [rozhraní Analyzovat rozhraní API pro obrázky](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) může počítačové vidění analyzovat typ obsahu obrázků, což ukazuje, zda se jedná o klipart nebo kresbu čar.

## <a name="detecting-clip-art"></a>Detekce klipartu

Počítačové vidění analyzuje obraz a hodnotí pravděpodobnost, že obraz je klipartem na stupnici od 0 do 3, jak je popsáno v následující tabulce.

| Hodnota | Význam |
|-------|---------|
| 0 | Není klipart |
| 1 | Nejednoznačné |
| 2 | Normální klipart |
| 3 | Dobrý klip-art |

### <a name="clip-art-detection-examples"></a>Příklady detekce klipartů

Následující odpovědi JSON znázorňují, co počítačová vize vrací při hodnocení pravděpodobnosti ukázkových obrázků, které jsou klipartem.

![Klipartový obrázek plátku sýra](./Images/cheese_clipart.png)

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

![Modrý dům a předzahrádka](./Images/house_yard.png)

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

## <a name="detecting-line-drawings"></a>Detekce čárových výkresů

Počítačové vidění analyzuje obraz a vrátí logickou hodnotu označující, zda se jedná o kresbu čáry.

### <a name="line-drawing-detection-examples"></a>Příklady detekce kreslení čar

Následující odpovědi JSON znázorňují, co se vrátí počítačové vidění, když označuje, zda jsou ukázkové obrázky čárové kresby.

![Obrázek čáry lva](./Images/lion_drawing.png)

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

![Bílý květ se zeleným pozadím](./Images/flower.png)

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

## <a name="use-the-api"></a>Použití rozhraní API

Funkce detekce typu obrázku je součástí rozhraní Analyzovat rozhraní API [pro obrázky.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Toto rozhraní API můžete volat prostřednictvím nativní sady SDK nebo prostřednictvím volání REST. Zahrnout `ImageType` do parametru dotazu **visualFeatures.** Potom, když získáte úplnou odpověď JSON, jednoduše analyzovat řetězec `"imageType"` pro obsah oddílu.

* [Úvodní příručka: Počítačové zpracování počítače .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Úvodní příručka: Analýza obrázku (REST API)](./quickstarts/csharp-analyze.md)
