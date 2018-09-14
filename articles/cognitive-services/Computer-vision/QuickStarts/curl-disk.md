---
title: Rychlý start k analýze místního obrázku pomocí rozhraní API pro počítačové zpracování obrazu s cURL | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: V tomto rychlém startu analyzujete místní obrázek pomocí počítačového zpracování obrazu s cURL ve službách Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 93ca3ea6eee3743dfd0c25c9514375ae63a531ee
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43769366"
---
# <a name="quickstart-analyze-a-local-image---rest-curl"></a>Rychlý start: Analýza místního obrázku – REST, cURL

V tomto rychlém startu analyzujete místní obrázek za účelem extrakce vizuálních prvků pomocí počítačového zpracování obrazu. Pokud chcete analyzovat vzdálený obrázek, přečtěte si článek o [analýze vzdáleného obrázku s cURL](curl-analyze.md).

## <a name="prerequisites"></a>Požadavky

Abyste mohli počítačové zpracování obrazu použít, potřebujete klíč předplatného. Přečtěte si, [jak získat klíče předplatného](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="analyze-a-local-image"></a>Analýza místního obrázku

Tento příklad je podobný [analýze vzdáleného obrázku s cURL](curl-analyze.md), s tím rozdílem, že analyzovaný obrázek se načítá místně z disku. Je nutné provést tři změny:

- Typ obsahu změňte na `"Content-Type: application/octet-stream"`.
- Přepínač `-d` změňte na `--data-binary`.
- Obrázek, který chcete analyzovat, určete pomocí následující syntaxe: `@C:/Pictures/ImageToAnalyze.jpg`.

Pokud chcete spustit ukázku, postupujte následovně:

1. Zkopírujte do editoru následující kód.
1. Místo `<Subscription Key>` použijte platný klíč předplatného.
1. V případě potřeby změňte adresu URL žádosti (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`), aby se použilo umístění, ze kterého jste získali klíče předplatného.
1. `<Image To Analyze>` nahraďte místním obrázkem, který chcete analyzovat.
1. Volitelně můžete změnit jazyk odpovědi (`language=en`).
1. Na počítači s nainstalovaným cURL otevřete příkazové okno.
1. Kód vložte do tohoto okna a spusťte příkaz.

>[!NOTE]
>Ve volání REST musíte použít stejné umístění, které jste použili k získání klíčů předplatného. Pokud jste například získali klíče předplatného z westus, nahraďte westcentralus v adrese URL uvedené níže umístěním westus.

```json
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" -H "Content-Type: application/octet-stream" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Categories,Description&details=Landmarks&language=en" --data-binary <Image To Analyze>
```

## <a name="analyze-image-response"></a>Odpověď metody Analyze Image

Úspěšná odpověď se vrátí ve formátu JSON, například:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "b6e33879-abb2-43a0-a96e-02cb5ae0b795",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>Další kroky

Prozkoumejte rozhraní API pro počítačové zpracování obrazu používané pro analýzu obrázku, zjišťování celebrit a památek, vytvoření miniatury a extrahování tištěného a ručně psaného textu. Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu rozhraní Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Prozkoumejte rozhraní API pro počítačové zpracování obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
