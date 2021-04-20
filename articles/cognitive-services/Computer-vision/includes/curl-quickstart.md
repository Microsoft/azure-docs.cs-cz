---
title: 'Rychlý Start: optické rozpoznávání znaků REST API'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu začněte s REST API optického rozpoznávání znaků.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 04/19/2021
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 2f01b1d222470c49505638be64180948b6f7e046
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728246"
---
Použijte REST API optického rozpoznávání znaků pro čtení vytištěného a rukopisného textu.

> [!NOTE]
> V tomto rychlém startu se pomocí oblé příkazy zavolá REST API. REST API můžete volat také pomocí programovacího jazyka. Příklady najdete v ukázkách v [jazycích C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/REST), [Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/ComputerVision/REST), [Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/ComputerVision/REST), [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/ComputerVision/REST)a [Přejít](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/ComputerVision/REST)na ukázky GitHubu.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [můžete ho vytvořit zdarma](https://azure.microsoft.com/free/cognitive-services/) . 
* Jakmile budete mít předplatné Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" vytvořte prostředek počítačové zpracování obrazu vytvoření prostředku "  target="_blank"> Počítačové zpracování obrazu </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
  * K připojení aplikace k Počítačové zpracování obrazu službě budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
  * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.
* nainstalovaná [složená](https://curl.haxx.se/) závorka



## <a name="read-printed-and-handwritten-text"></a>Číst vytištěné a ručně psaný text

Služba OCR může číst viditelný text v obrázku a převést jej na datový proud znaků. Další informace o rozpoznávání textu naleznete v tématu [Přehled optického rozpoznávání znaků (OCR)](../overview-ocr.md).

### <a name="call-the-read-api"></a>Volání rozhraní API pro čtení

Pokud chcete vytvořit a spustit ukázku, postupujte takto:

1. Zkopírujte do textového editoru následující příkaz.
1. Proveďte v příkazu na příslušných místech následující změny:
    1. Hodnotu `<subscriptionKey>` nahraďte klíčem předplatného.
    1. Nahraďte první část adresy URL požadavku ( `westcentralus` ) textem ve vaší vlastní adrese URL koncového bodu.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Volitelně můžete změnit adresu URL obrázku v textu požadavku (`https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\`) na adresu URL jiného obrázku, který se má analyzovat.
1. Otevřete okno příkazového řádku.
1. Vložte příkaz z textového editoru do okna příkazového řádku a pak příkaz spusťte.

```bash
curl -v -X POST "https://westcentralus.api.cognitive.microsoft.com/vision/v3.2/read/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\"}"
```

Odpověď bude obsahovat `Operation-Location` hlavičku, jejíž hodnota je jedinečná adresa URL. Tuto adresu URL použijete k dotazování na výsledky operace čtení. Platnost adresy URL vyprší během 48 hodin.

### <a name="get-read-results"></a>Získat výsledky čtení

1. Zkopírujte následující příkaz do textového editoru.
1. Adresu URL nahraďte `Operation-Location` hodnotou, kterou jste zkopírovali v předchozím kroku.
1. Proveďte v příkazu na příslušných místech následující změny:
    1. Hodnotu `<subscriptionKey>` nahraďte klíčem předplatného.
1. Otevřete okno příkazového řádku.
1. Vložte příkaz z textového editoru do okna příkazového řádku a pak příkaz spusťte.

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/vision/v3.2/read/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{body}" 
```

### <a name="examine-the-response"></a>Prozkoumání odpovědi

Úspěšná odpověď se vrátí ve formátu JSON. Ukázková aplikace provede analýzu a zobrazí úspěšnou odpověď v okně příkazového řádku, podobně jako v následujícím příkladu:

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-04-08T21:56:17.6819115+00:00",
  "lastUpdatedDateTime": "2021-04-08T21:56:18.4161316+00:00",
  "analyzeResult": {
    "version": "3.2",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 338,
        "height": 479,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              25,
              14,
              318,
              14,
              318,
              59,
              25,
              59
            ],
            "text": "NOTHING",
            "appearance": {
              "style": {
                "name": "other",
                "confidence": 0.971
              }
            },
            "words": [
              {
                "boundingBox": [
                  27,
                  15,
                  294,
                  15,
                  294,
                  60,
                  27,
                  60
                ],
                "text": "NOTHING",
                "confidence": 0.994
              }
            ]
          }
        ]
      }
    ]
  }
}

```



## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak volat REST API pro čtení. V dalším kroku se dozvíte víc o funkcích rozhraní API pro čtení.

> [!div class="nextstepaction"]
>[Volání rozhraní API pro čtení](../Vision-API-How-to-Topics/call-read-api.md)

* [Přehled rozpoznávání OCR](../overview-ocr.md)
