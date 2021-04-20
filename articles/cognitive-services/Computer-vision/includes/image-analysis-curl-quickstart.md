---
title: 'Rychlý Start: REST API analýzy obrázků'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu začněte s REST API analýzy obrázků.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 04/19/2021
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 7a2e8613aab61beec3720cadaa20eb008386b43b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728160"
---
Použijte REST API analýzy obrázků k těmto akcím:

* Analyzujte obrázek pro značky, text Description, obličeje, obsah pro dospělé a další.
* Vygenerovat miniaturu pomocí inteligentního oříznutí

> [!NOTE]
> V tomto rychlém startu se pomocí oblé příkazy zavolá REST API. REST API můžete volat také pomocí programovacího jazyka. Příklady najdete v ukázkách v [jazycích C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/REST), [Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/ComputerVision/REST), [Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/ComputerVision/REST), [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/ComputerVision/REST)a [Přejít](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/ComputerVision/REST)na ukázky GitHubu.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/cognitive-services/) . 
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" vytvořte prostředek počítačové zpracování obrazu vytvoření prostředku "  target="_blank"> Počítačové zpracování obrazu </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
  * K připojení aplikace k Počítačové zpracování obrazu službě budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
  * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.
* nainstalovaná [složená](https://curl.haxx.se/) závorka

## <a name="analyze-an-image"></a>Analýza obrázku

Chcete-li analyzovat obrázek pro celou řadu vizuálních funkcí, proveďte následující kroky:

1. Zkopírujte do textového editoru následující příkaz.
1. Proveďte v příkazu na příslušných místech následující změny:
    1. Hodnotu `<subscriptionKey>` nahraďte klíčem předplatného.
    1. Nahraďte první část adresy URL požadavku ( `westcentralus` ) textem ve vaší vlastní adrese URL koncového bodu.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Volitelně můžete změnit adresu URL obrázku v textu požadavku (`http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\`) na adresu URL jiného obrázku, který se má analyzovat.
1. Otevřete okno příkazového řádku.
1. Vložte příkaz z textového editoru do okna příkazového řádku a pak příkaz spusťte.

```bash
curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v3.1/analyze?visualFeatures=Categories,Description&details=Landmarks" -d "{\"url\":\"http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\"}"
```

### <a name="examine-the-response"></a>Prozkoumání odpovědi

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


## <a name="generate-a-thumbnail"></a>Vytvoření miniatury

Pomocí analýzy obrázků můžete vygenerovat miniaturu pomocí inteligentního oříznutí. Určete požadovanou výšku a šířku, která se může lišit v poměru stran od vstupní image. Analýza obrázků používá inteligentní ořezávání k inteligentně identifikaci oblasti zájmu a k vygenerování souřadnic oříznutí kolem této oblasti.
 
Pokud chcete vytvořit a spustit ukázku, postupujte takto:

1. Zkopírujte do textového editoru následující příkaz.
1. Proveďte v příkazu na příslušných místech následující změny:
    1. Hodnotu `<subscriptionKey>` nahraďte klíčem předplatného.
    1. Hodnotu nahraďte `<thumbnailFile>` cestou a názvem souboru, do kterého chcete uložit vrácenou miniaturu.
    1. Nahraďte první část adresy URL požadavku ( `westcentralus` ) textem ve vaší vlastní adrese URL koncového bodu.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Volitelně můžete změnit adresu URL obrázku v textu požadavku (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) na adresu URL jiného obrázku, ze kterého se má generovat miniatura.
1. Otevřete okno příkazového řádku.
1. Vložte příkaz z textového editoru do okna příkazového řádku.
1. Stiskněte klávesu ENTER a program se spustí.

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westus.api.cognitive.microsoft.com/vision/v3.1/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

### <a name="examine-the-response"></a>Prozkoumání odpovědi

Úspěšná odpověď zapíše obrázek miniatury do souboru určeného v `<thumbnailFile>`. Pokud požadavek selže, bude odpověď obsahovat chybový kód a zprávu, která vám pomůže určit, co se nepovedlo. Pokud se žádost zdá být úspěšná, ale vytvořená Miniatura není platným souborem obrázku, může to být tím, že váš klíč předplatného není platný.


## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak nainstalovat základní volání analýzy obrázků pomocí REST API. V dalším kroku se dozvíte víc o funkcích rozhraní API pro analýzu.

> [!div class="nextstepaction"]
>[Volání rozhraní API pro analýzu](../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Přehled analýzy obrázků](../overview-image-analysis.md)