---
title: 'Rychlý Start: extrakce příjmových dat pomocí nástroje pro rozpoznávání formulářů v Pythonu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete REST API pro rozpoznávání formulářů s Pythonem k extrakci dat z imagí prodejních příjmů v USA.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: b897690e08f07f2506aa79c873af03b604f94664
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2020
ms.locfileid: "96009581"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-python"></a>Rychlý Start: extrakce údajů o příjemcích pomocí REST API pro rozpoznávání formulářů v Pythonu

V tomto rychlém startu použijete nástroj pro rozpoznávání formulářů Azure REST API s Pythonem k extrakci a identifikaci relevantních informací v prodejních příjemkách USA.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu musíte mít:
- Je nainstalovaný [Python](https://www.python.org/downloads/) (Pokud chcete spustit ukázku místně).
- Obrázek účtenky. Pro tento rychlý Start můžete použít [ukázkový obrázek](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg) .

> [!NOTE]
> V tomto rychlém startu se používá místní soubor. Chcete-li místo toho použít k používání obrázku účtenky pomocí adresy URL, přečtěte si [referenční dokumentaci](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync).

## <a name="create-a-form-recognizer-resource"></a>Vytvoření prostředku pro rozpoznávání formulářů

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Analýza účtenky

Chcete-li zahájit analýzu účtenky, zavolejte rozhraní API **[analýzy pro příjem](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync)** pomocí níže uvedeného skriptu Pythonu. Před spuštěním skriptu proveďte tyto změny:

1. Nahraďte `<Endpoint>` koncovým bodem, který jste získali v rámci předplatného pro rozpoznávání formulářů.
1. Nahraďte `<path to your receipt>` cestou k místnímu dokumentu formuláře.
1. Nahraďte `<subscription key>` klíčem předplatného, který jste zkopírovali z předchozího kroku.

# <a name="v20"></a>[v2.0](#tab/v2-0)

```python
    ########### Python Form Recognizer Async Receipt #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<subscription key>"
    post_url = endpoint + "/formrecognizer/v2.0/prebuilt/receipt/analyze"
    source = r"<path to your receipt>"
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    
    params = {
        "includeTextDetails": True
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
```
    
# <a name="v21-preview2"></a>[v 2.1 – Preview. 2](#tab/v2-1)    
```python
    ########### Python Form Recognizer Async Receipt #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<subscription key>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.2/prebuilt/receipt/analyze"
    source = r"<path to your receipt>"
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    
    params = {
        "includeTextDetails": True
        "locale": "en-US"
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
```

> [!NOTE]
> **Vstup jazyka** 
>
> Operace vydání Analzye Receipt 2,1 obsahuje volitelný parametr požadavku pro jazyk, národní prostředí účtenky. Mezi podporovaná národní prostředí patří: EN-AU, en-CA, en-GB, en-IN, en-US. 

---

1. Uložte kód do souboru s příponou. py. Například *Form-Recognizer-Receipts.py*.
1. Otevřete okno příkazového řádku.
1. Ke spuštění ukázky na příkazovém řádku použijte příkaz `python`. Například, `python form-recognizer-receipts.py`.

Dostanete `202 (Success)` odpověď, která obsahuje hlavičku **umístění operace** , kterou skript vytiskne do konzoly. Tato hlavička obsahuje ID operace, pomocí které můžete zadat dotaz na stav asynchronní operace a získat výsledky. V následujícím příkladu hodnoty řetězec následuje `operations/` ID operace.

# <a name="v20"></a>[v2.0](#tab/v2-0)    
```console
https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```
# <a name="v21-preview1"></a>[v 2.1 – Preview. 1](#tab/v2-1)    
```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```
---

## <a name="get-the-receipt-results"></a>Získání výsledků pro příjem

Po volání funkce **analyzovat příjem** rozhraní API zavoláte rozhraní API pro **[získání výsledku analýzy](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeReceiptResult)** pro získání stavu operace a extrahovaná data. Do dolní části skriptu Pythonu přidejte následující kód. Použije se hodnota ID operace v novém volání rozhraní API. Tento skript volá rozhraní API v pravidelných intervalech, dokud nebudou k dispozici výsledky. Doporučujeme interval jednoho sekundy nebo více.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Receipt results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Receipt Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. Uložte skript.
1. Znovu použijte `python` příkaz ke spuštění ukázky. Například, `python form-recognizer-receipts.py`.

### <a name="examine-the-response"></a>Prozkoumání odpovědi

Skript vytiskne odpovědi na konzolu, dokud se nedokončí operace **analyzovat příjem** . Pak budou ve formátu JSON vytištěna extrahovaná textová data. `"readResults"`Pole obsahuje každý řádek textu, který byl extrahován z účtenky, a `"documentResults"` pole obsahuje informace o klíč/hodnotě pro nejrelevantnější části účtenky.

Podívejte se na následující obrázek účtenky a příslušný výstup JSON. Výstup byl zkrácen z důvodu čitelnosti.

![Účtenka z obchodu contoso](../media/contoso-allinone.jpg)

`"readResults"`Uzel obsahuje veškerý rozpoznaný text. Text je uspořádán podle stránky, potom podle řádku, podle jednotlivých slov. `"documentResults"`Uzel obsahuje hodnoty specifické pro příjem, které model zjistil. Tady najdete užitečné páry klíč/hodnota, jako je daň, celková, obchodní adresa atd.

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{ 
    "version":"2.0.0",
    "readResults":[ 
      { 
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[ 
          { 
            "text":"Contoso",
            "boundingBox":[ 
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[ 
              { 
                "text":"Contoso",
                "boundingBox":[ 
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[ 
      { 
        "docType":"prebuilt:receipt",
        "pageRange":[ 
          1,
          1
        ],
        "fields":{ 
          "ReceiptType":{ 
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{ 
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[ 
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[ 
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{ 
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[ 
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{ 
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[ 
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{ 
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[ 
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{ 
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[ 
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[ 
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{ 
            "type":"array",
            "valueArray":[ 
              { 
                "type":"object",
                "valueObject":{ 
                  "Quantity":{ 
                    "type":"number",
                    "text":"1",
                    "boundingBox":[ 
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{ 
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[ 
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{ 
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[ 
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[ 
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{ 
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[ 
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[ 
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{ 
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[ 
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[ 
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{ 
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[ 
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[ 
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{ 
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[ 
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[ 
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste k extrakci obsahu prodejní příjemky použili REST API pro rozpoznávání formuláře pomocí Pythonu. Dále si přečtěte referenční dokumentaci a prozkoumejte rozhraní API pro rozpoznávání formulářů ve větší hloubkě.

> [!div class="nextstepaction"]
> [Referenční dokumentace REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync)
