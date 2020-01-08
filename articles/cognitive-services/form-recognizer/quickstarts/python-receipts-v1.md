---
title: 'Rychlý Start: extrakce příjmových dat pomocí nástroje pro rozpoznávání formulářů v Pythonu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete REST API pro rozpoznávání formulářů s Pythonem k extrakci dat z imagí prodejních příjmů.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 12/18/2019
ms.author: pafarley
ms.openlocfilehash: 5d95527fa5333b113fb53840fd687313a139413a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473875"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-python"></a>Rychlý Start: extrakce údajů o příjemcích pomocí REST API pro rozpoznávání formulářů v Pythonu

V tomto rychlém startu použijete nástroj pro rozpoznávání formulářů Azure REST API s Pythonem k extrakci a identifikaci relevantních informací v prodejních příjemkách.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

> [!IMPORTANT]
> V tomto rychlém startu se používá rozhraní API pro rozpoznávání formulářů v 1.0. Pokud je vaše předplatné ve `West US 2` nebo `West Europe` oblasti, měli byste místo toho postupovat podle pokynů v části[rychlý Start pro rozhraní API v 2,0](./python-train-extract.md) .

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto rychlého startu musíte mít:
- Přístup k nástroji pro rozpoznávání přístupu s omezeným přístupem ve formě přístupu Chcete-li získat přístup k verzi Preview, vyplňte a odešlete formulář [žádosti o přístup pro rozpoznávání formulářů](https://aka.ms/FormRecognizerRequestAccess) .
- Je nainstalovaný [Python](https://www.python.org/downloads/) (Pokud chcete spustit ukázku místně).
- Adresa URL obrázku účtenky Pro tento rychlý Start můžete použít [ukázkový obrázek](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/contoso-receipt.png?raw=true) .

## <a name="create-a-form-recognizer-resource"></a>Vytvoření prostředku pro rozpoznávání formulářů

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Analýza účtenky

Chcete-li zahájit analýzu účtenky, zavolejte rozhraní API **analýzy pro příjem** pomocí níže uvedeného skriptu Pythonu. Před spuštěním skriptu proveďte tyto změny:

1. Nahraďte `<Endpoint>` koncovým bodem, který jste získali v rámci předplatného pro rozpoznávání vašeho formuláře.
1. Nahraďte `<your receipt URL>` adresou URL obrázku účtenky.
1. Nahraďte `<subscription key>` klíčem předplatného, který jste zkopírovali z předchozího kroku.

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

1. Uložte kód do souboru s příponou. py. Například *Form-Recognizer-Receipts.py*.
1. Otevřete okno příkazového řádku.
1. Ke spuštění ukázky na příkazovém řádku použijte příkaz `python`. Například, `python form-recognizer-receipts.py`.

Dostanete odpověď na `202 (Success)`, která obsahuje hlavičku **operace – umístění** , kterou skript vytiskne do konzoly. Tato hlavička obsahuje ID operace, pomocí které můžete zadat dotaz na stav operace a získat výsledky analýzy. V následujícím příkladu hodnoty je řetězec po `operations/` ID operace.

```console
https://cognitiveservice/formrecognizer/v1.0-preview/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>Získání výsledků pro příjem

Po volání funkce **analyzovat příjem** rozhraní API zavolejte rozhraní API **získat výsledek příjemky** , abyste získali stav operace a extrahovaná data. Do dolní části skriptu Pythonu přidejte následující kód. Tím se extrahuje hodnota ID operace a předá ji novému volání rozhraní API. Operace je asynchronní, takže tento skript volá rozhraní API v pravidelných intervalech, dokud nebudou k dispozici výsledky. Doporučujeme interval jednoho sekundy nebo více.

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
1. Znovu použijte příkaz `python` ke spuštění ukázky. Například, `python form-recognize-analyze.py`.

### <a name="examine-the-response"></a>Prozkoumání odpovědi

Skript vytiskne odpovědi na konzolu, dokud se nedokončí operace analyzovat. Pak budou ve formátu JSON vytištěna extrahovaná textová data. Pole `"recognitionResults"` obsahuje všechny řádky textu, které byly extrahovány z účtenky, a pole `"understandingResults"` obsahuje informace o klíč/hodnota pro nejrelevantnější části příjmu.

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

V tomto rychlém startu jste použili nástroj pro rozpoznávání formulářů REST API s Pythonem k vytvoření výukového modelu a jeho spuštění ve vzorovém scénáři. Dále si přečtěte referenční dokumentaci a prozkoumejte rozhraní API pro rozpoznávání formulářů ve větší hloubkě.

> [!div class="nextstepaction"]
> [Referenční dokumentace REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeReceipt)