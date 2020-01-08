---
title: 'Rychlý Start: extrakce příjmových dat pomocí funkce pro rozpoznávání složeného formuláře'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete REST API pro rozpoznávání formulářů s kudrlinkou k extrakci dat z imagí prodejních příjmů.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 12/18/2019
ms.author: pafarley
ms.openlocfilehash: 5dd459adec95eb764cf3458d1eceb455b36fd965
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473910"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-curl"></a>Rychlý Start: extrakce údajů o příjemcích pomocí REST API pro rozpoznávání formulářů s kudrlinkou

V tomto rychlém startu použijete REST API pro rozpoznávání formulářů Azure s kudrlinkou k extrakci a identifikaci relevantních informací v prodejních příjemkách.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

> [!IMPORTANT]
> V tomto rychlém startu se používá rozhraní API pro rozpoznávání formulářů v 1.0. Pokud je vaše předplatné ve `West US 2` nebo `West Europe` oblasti, měli byste místo toho postupovat podle pokynů v části[rychlý Start pro rozhraní API v 2,0](./python-train-extract.md) .

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto rychlého startu musíte mít:
- Přístup k nástroji pro rozpoznávání přístupu s omezeným přístupem ve formě přístupu Chcete-li získat přístup k verzi Preview, vyplňte a odešlete formulář [žádosti o přístup pro rozpoznávání formulářů](https://aka.ms/FormRecognizerRequestAccess) .
- byl nainstalován [oblý](https://curl.haxx.se/windows/) .
- Adresa URL obrázku účtenky Pro tento rychlý Start můžete použít [ukázkový obrázek](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true) .

## <a name="create-a-form-recognizer-resource"></a>Vytvoření prostředku pro rozpoznávání formulářů

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Analýza účtenky

Chcete-li zahájit analýzu účtenky, zavolejte rozhraní API **analýzy pro příjem** pomocí příkazu složeného níže. Před spuštěním příkazu proveďte tyto změny:

1. Nahraďte `<Endpoint>` koncovým bodem, který jste získali v rámci předplatného pro rozpoznávání vašeho formuláře.
1. Nahraďte `<your receipt URL>` adresou URL obrázku účtenky.
1. Nahraďte `<subscription key>` klíčem předplatného, který jste zkopírovali z předchozího kroku.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/prebuilt/receipt/asyncBatchAnalyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"url\": \"<your receipt URL>\"}"
```

Obdržíte odpověď `202 (Success)`, která obsahuje hlavičku **umístění operace** . Hodnota této hlavičky obsahuje ID operace, kterou můžete použít k dotazování na stav operace a získání výsledků. V následujícím příkladu je řetězec po `operations/` ID operace.

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>Získání výsledků pro příjem

Po volání funkce **analyzovat příjem** rozhraní API zavolejte rozhraní API **získat výsledek příjemky** , abyste získali stav operace a extrahovaná data.

1. Nahraďte `<operationId>` IDENTIFIKÁTORem operace z předchozího kroku.
1. Místo `<subscription key>` použijte váš klíč předplatného.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v1.0-preview/prebuilt/receipt/operations/<operationId>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>Prozkoumání odpovědi

Dostanete odpověď `200 (Success)` s výstupem JSON. První pole `"status"`určuje stav operace. Pokud je operace dokončena, pole `"recognitionResults"` obsahuje všechny řádky textu, které byly extrahovány z účtenky, a pole `"understandingResults"` obsahuje informace o klíč/hodnota pro nejrelevantnější části příjmu. Pokud operace není dokončená, hodnota `"status"` bude `"Running"` nebo `"NotStarted"`a později byste měli volat rozhraní API, a to buď ručně, nebo prostřednictvím skriptu. Pro mezi voláními doporučujeme interval o délce jedné sekundy nebo více.

Podívejte se na následující obrázek účtenky a příslušný výstup JSON. Výstup byl zkrácen z důvodu čitelnosti.

![Účtenka z obchodu contoso](../media/contoso-receipt.png)

Uzel `"recognitionResults"` obsahuje veškerý rozpoznaný text. Text je uspořádán podle stránky, potom podle řádku, podle jednotlivých slov. Uzel `"understandingResults"` obsahuje hodnoty specifické pro příjem, které model zjistil. Tady najdete užitečné páry klíč/hodnota, jako je daň, celková, obchodní adresa atd.

```json
{
  "status": "Succeeded",
  "recognitionResults": [{
    "page": 1,
    "clockwiseOrientation": 0.36,
    "width": 1688,
    "height": 3000,
    "unit": "pixel",
    "lines": [{
      "boundingBox": [616, 291, 1050, 278, 1053, 384, 620, 397],
      "text": "Contoso",
      "words": [{
        "boundingBox": [619, 292, 1051, 284, 1052, 382, 620, 398],
        "text": "Contoso"
      }]
    }, {
      "boundingBox": [322, 588, 501, 600, 497, 655, 318, 643],
      "text": "Contoso",
      "words": [{
        "boundingBox": [330, 590, 501, 602, 499, 654, 326, 644],
        "text": "Contoso"
      }]
    },
    ...
    ]
  }],
  "understandingResults": [{
    "pages": [1],
    "fields": {
      "Subtotal": {
        "valueType": "numberValue",
        "value": 1098.99,
        "text": "1098.99",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/14/words/1"
        }]
      },
      "Total": {
        "valueType": "numberValue",
        "value": 1203.39,
        "text": "1203.39",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/18/words/1"
        }]
      },
      "Tax": {
        "valueType": "numberValue",
        "value": 104.4,
        "text": "$104.40",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/16/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/16/words/1"
        }]
      },
      "MerchantAddress": {
        "valueType": "stringValue",
        "value": "123 Main Street Redmond, WA 98052",
        "text": "123 Main Street Redmond, WA 98052",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/2/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/2/words/1"
        }, {
          "$ref": "#/recognitionResults/0/lines/2/words/2"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/0"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/1"
        }, {
          "$ref": "#/recognitionResults/0/lines/3/words/2"
        }]
      },
      "MerchantName": {
        "valueType": "stringValue",
        "value": "Contoso",
        "text": "Contoso",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/1/words/0"
        }]
      },
      "MerchantPhoneNumber": {
        "valueType": "stringValue",
        "value": null,
        "text": "123-456-7890",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/4/words/0"
        }]
      },
      "TransactionDate": {
        "valueType": "stringValue",
        "value": "2019-06-10",
        "text": "6/10/2019",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/5/words/0"
        }]
      },
      "TransactionTime": {
        "valueType": "stringValue",
        "value": "13:59:00",
        "text": "13:59",
        "elements": [{
          "$ref": "#/recognitionResults/0/lines/5/words/1"
        }]
      }
    }
  }]
}
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste k extrakci obsahu prodejní účtenky použili REST API pro rozpoznávání formuláře s kudrlinkou. Dále si přečtěte referenční dokumentaci a prozkoumejte rozhraní API pro rozpoznávání formulářů ve větší hloubkě.

> [!div class="nextstepaction"]
> [Referenční dokumentace REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)