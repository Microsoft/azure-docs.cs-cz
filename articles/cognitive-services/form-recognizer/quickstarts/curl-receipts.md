---
title: 'Rychlý start: Extrahování příjmu dat pomocí cURL – nástroj pro rozpoznávání formuláře'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete rozhraní REST API pro rozpoznávání formuláře pomocí cURL k extrahování dat z imagí prodejních výnosů.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: c232469886941a2db1ae57437c4b72d7565c123c
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503445"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-curl"></a>Rychlý start: Extrahování příjmu dat pomocí rozhraní REST API pro rozpoznávání formuláře pomocí cURL

V tomto rychlém startu použijete rozhraní REST API pro rozpoznávání formulář Azure pomocí cURL k extrahování a identifikaci relevantních informací v prodejních výnosů.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
Abyste mohli absolvovat tento rychlý start, musíte mít:
- Přístup k rozpoznávání formuláře omezený přístup ve verzi Preview. Pokud chcete získat přístup k verzi preview, vyplňte a odešlete [žádost o přístup formulář Rozlišovač](https://aka.ms/FormRecognizerRequestAccess) formuláře.
- [cURL](https://curl.haxx.se/windows/) nainstalované.
- Adresa URL obrázku příjmu. Můžete použít [Ukázkový obrázek](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true) pro tento rychlý start.

## <a name="create-a-form-recognizer-resource"></a>Vytvořit prostředek Rozlišovač formuláře

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Analýza potvrzení

Chcete-li začít analýza potvrzení, zavolejte **analyzovat příjmu** rozhraní API s využitím následující příkaz cURL. Před spuštěním příkazu, proveďte následující změny:

1. Nahraďte `<Endpoint>` s koncovým bodem, který jste získali váš klíč předplatného Rozlišovač formuláře. Vyhledejte ji na váš prostředek formuláře Rozlišovač **přehled** kartu.
1. Nahraďte `<your receipt URL>` s adresou URL příjmu bitové kopie.
1. Nahraďte `<subscription key>` s klíči předplatného jste zkopírovali v předchozím kroku.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/prebuilt/receipt/asyncBatchAnalyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"url\": \"<your receipt URL>\"}"
```

Zobrazí se `202 (Success)` odpověď, která obsahuje **operace umístění** záhlaví. Hodnotu této hlavičky obsahuje ID operace, které můžete použít k dotazování stavu operace a získat výsledky. V následujícím příkladu, řetězec za `operations/` je ID operace.

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>Získání výsledků příjmu

Poté, co jste volat **analyzovat příjmu** volání rozhraní API, **získat výsledek přijetí** rozhraní API se získat stav operace a extrahovaná data.

1. Nahraďte `<operationId>` s ID operace z předchozího kroku.
1. Místo `<subscription key>` použijte váš klíč předplatného.

```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/prebuilt/receipt/operations/<operationId>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>Prozkoumání odpovědi

Zobrazí se `200 (Success)` odpověď se výstup ve formátu JSON. První pole `"status"`, označuje stav operace. Pokud je operace dokončena, `"recognitionResults"` pole obsahuje každý jednotlivý řádek textu, který byl získán z příjmu a `"understandingResults"` pole obsahuje informace o klíč/hodnota pro nejdůležitější části jeho přijetí. Pokud operace není kompletní, hodnota `"status"` bude `"Running"` nebo `"NotStarted"`, a znovu, měli byste zavolat rozhraní API ručně nebo pomocí skriptu. Doporučujeme, abyste v intervalu jedné sekundy nebo více mezi voláními.

Viz následující obrázek potvrzení a jeho odpovídající JSON výstupu. Ve výstupu byla zkrácena pro lepší čitelnost.

![Potvrzení z obchodu společnosti Contoso](../media/contoso-receipt.png)

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

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste použili rozhraní REST API pro rozpoznávání formuláře pomocí cURL pro extrahování obsahu prodejní přijetí. V dalším kroku naleznete v referenční dokumentaci rozhraní API pro rozpoznávání formuláře podrobněji prozkoumat.

> [!div class="nextstepaction"]
> [Dokumentace k rozhraní REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)
