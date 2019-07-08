---
title: 'Rychlý start: Extrahovat příjmu dat s využitím Pythonu – nástroj pro rozpoznávání formuláře'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete rozhraní REST API pro rozpoznávání formulář s využitím Pythonu extrahovat data z imagí prodejních výnosů.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: 8bd4d441859df6dbb36f594d8423eefd84274ec4
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592549"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-python"></a>Rychlý start: Extrahování příjmu dat pomocí rozhraní REST API pro rozpoznávání formulář s využitím Pythonu

V tomto rychlém startu použijete rozhraní REST API pro rozpoznávání formulář Azure s využitím Pythonu pro extrakci a identifikaci relevantních informací v prodejních výnosů.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
Abyste mohli absolvovat tento rychlý start, musíte mít:
- Přístup k rozpoznávání formuláře omezený přístup ve verzi Preview. Pokud chcete získat přístup k verzi preview, vyplňte a odešlete [žádost o přístup formulář Rozlišovač](https://aka.ms/FormRecognizerRequestAccess) formuláře.
- [Python](https://www.python.org/downloads/) nainstalovaná (Pokud chcete ke spuštění ukázky místně).
- Adresa URL obrázku příjmu. Můžete použít [Ukázkový obrázek](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true) pro tento rychlý start.

## <a name="create-a-form-recognizer-resource"></a>Vytvořit prostředek Rozlišovač formuláře

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Analýza potvrzení

Chcete-li začít analýza potvrzení, zavolejte **analyzovat příjmu** rozhraní API pomocí níže uvedeného skriptu Pythonu. Předtím, než spustíte skript, proveďte následující změny:

1. Nahraďte `<Endpoint>` s koncovým bodem, který jste získali váš klíč předplatného Rozlišovač formuláře. Vyhledejte ji na váš prostředek formuláře Rozlišovač **přehled** kartu.
1. Nahraďte `<your receipt URL>` s adresou URL příjmu bitové kopie.
1. Nahraďte `<subscription key>` s klíči předplatného jste zkopírovali v předchozím kroku.

    ```python
    import http.client, urllib.request, urllib.parse, urllib.error, base64

    source = r"<your receipt URL>"
    body = {"url":source}
    body = json.dumps(body)

    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': '<subscription key>',
    }

    try:
        conn = http.client.HTTPSConnection('<Endpoint>')
        conn.request("POST", "/formrecognizer/v1.0-preview/prebuilt/receipt/asyncBatchAnalyze", body, headers)
        response = conn.getresponse()
        data = response.read()
        operationURL = "" + response.getheader("Operation-Location")
        print ("Operation-Location header: " + operationURL)
        conn.close()
    except Exception as e:
        print(e)
        exit()
    ```

1. Uložte kód do souboru s příponou .py. Například *formuláře. pro rozpoznávání receipts.py*.
1. Otevřete okno příkazového řádku.
1. Ke spuštění ukázky na příkazovém řádku použijte příkaz `python`. Například, `python form-recognizer-receipts.py`.

Zobrazí se `202 (Success)` odpověď, která obsahuje **operace umístění** hlavičky, která skriptu se vypíše do konzoly. Této hlavičky obsahuje ID operace, které můžete použít k dotazování stavu operace a získat výsledky analýzy. Následující příklad hodnotu řetězce po `operations/` je ID operace.

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>Získání výsledků příjmu

Poté, co jste volat **analyzovat příjmu** volání rozhraní API, **získat výsledek přijetí** rozhraní API se získat stav operace a extrahovaná data. Přidejte následující kód k dolnímu okraji váš skript Python. To extrahuje hodnotu ID operace a předá ji do nové volání rozhraní API. Operace je asynchronní, takže tento skript volá rozhraní API v pravidelných intervalech, dokud výsledky jsou k dispozici. Doporučujeme, abyste intervalu nejméně jedné sekundy.

```python
operationId = operationURL.split("operations/")[1]

conn = http.client.HTTPSConnection('<Endpoint>')
while True:
    try:
        conn.request("GET", f"/formrecognizer/v1.0-preview/prebuilt/receipt/operations/{operationId}", "", headers)
        responseString = conn.getresponse().read().decode('utf-8')
        responseDict = json.loads(responseString)
        conn.close()
        print(responseString)
        if 'status' in responseDict and responseDict['status'] not in ['NotStarted','Running']:
            break
        time.sleep(1)
    except Exception as e:
        print(e)
        exit()
```

1. Uložte skript.
1. Znovu použít `python` příkaz ke spuštění ukázky. Například, `python form-recognize-analyze.py`.

### <a name="examine-the-response"></a>Prozkoumání odpovědi

Skript vytiskne odpovědi do konzoly, až do dokončení operace analýzy. Potom vytiskne data byl extrahován text ve formátu JSON. `"recognitionResults"` Pole obsahuje každý jednotlivý řádek textu, který byl získán z příjmu a `"understandingResults"` pole obsahuje informace o klíč/hodnota pro nejdůležitější části jeho přijetí.

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

V tomto rychlém startu jste použili rozhraní REST API pro rozpoznávání formulář s využitím Pythonu ke trénování modelu a jeho spuštění v ukázkovém scénáři. V dalším kroku naleznete v referenční dokumentaci rozhraní API pro rozpoznávání formuláře podrobněji prozkoumat.

> [!div class="nextstepaction"]
> [Dokumentace k rozhraní REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)
