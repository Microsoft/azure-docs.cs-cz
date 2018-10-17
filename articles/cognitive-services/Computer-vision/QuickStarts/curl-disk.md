---
title: 'Rychlý start: Analýza místního obrázku – REST, cURL – počítačové zpracování obrazu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu budete analyzovat místní obrázek pomocí rozhraní API pro počítačové zpracování obrazu a cURL.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: v-deken
ms.openlocfilehash: 1952d0045550362a242ce1c105c58bd65c815ea6
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2018
ms.locfileid: "45631238"
---
# <a name="quickstart-analyze-a-local-image-using-the-rest-api-and-curl-in-computer-vision"></a>Rychlý start: Analýza místního obrázku pomocí rozhraní REST API a cURL v počítačovém zpracování obrazu

V tomto rychlém startu analyzujete obrázek uložený místně za účelem extrakce vizuálních prvků pomocí rozhraní REST API počítačového zpracování obrazu. Pomocí metody [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) můžete extrahovat vizuální prvky na základě obsahu obrázku.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

- Musíte mít [cURL](https://curl.haxx.se/windows).
- Musíte mít klíč předplatného pro počítačové zpracování obrazu. Abyste získali klíč předplatného, přejděte k tématu [Jak získat klíče předplatného](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="create-and-run-the-sample-command"></a>Vytvoření a spuštění ukázkového příkazu

Pokud chcete vytvořit a spustit ukázku, postupujte takto:

1. Zkopírujte do textového editoru následující příkaz.
1. Proveďte v příkazu na příslušných místech následující změny:
    1. Hodnotu `<subscriptionKey>` nahraďte klíčem předplatného.
    1. Adresu URL požadavku (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze`) nahraďte adresou URL koncového bodu metody [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) z oblasti Azure, kde jste získali klíče předplatného, pokud je to potřeba.
    1. Hodnotu `<localImage>` nahraďte úplnou cestu a názvem souboru obrázku, který chcete analyzovat. Například, `@C:/Pictures/ImageToAnalyze.jpg`.
    1. Volitelně můžete změnit parametr jazyka v adrese URL požadavku (`language=en`) a použít jiný podporovaný jazyk.
1. Otevřete okno příkazového řádku.
1. Vložte příkaz z textového editoru do okna příkazového řádku a pak příkaz spusťte.

```console
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" -H "Content-Type: application/octet-stream" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Categories,Description&details=Landmarks&language=en" --data-binary "<localImage>"
```

## <a name="examine-the-response"></a>Prozkoumání odpovědi

Úspěšná odpověď se vrátí ve formátu JSON. Ukázková aplikace provede analýzu a zobrazí úspěšnou odpověď v okně příkazového řádku, podobně jako v následujícím příkladu:

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

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už tuto ukázku nepotřebujete, zavřete okno příkazového řádku a textový editor.

## <a name="next-steps"></a>Další kroky

Prozkoumejte rozhraní API pro počítačové zpracování obrazu, které se používá pro analýzu obrázku, zjišťování celebrit a památek, vytvoření miniatury a extrahování tištěného a ručně psaného textu. Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Prozkoumat rozhraní API pro počítačové zpracování obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
