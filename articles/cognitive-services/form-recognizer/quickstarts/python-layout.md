---
title: 'Rychlý Start: extrakce textu a rozložení pomocí funkce pro rozpoznávání formulářů v Pythonu'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete rozložení pro rozpoznávání formulářů REST API s Pythonem ke čtení textu a dat tabulek z vašich formulářů.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 1777d4d09dfb12f490245f726715aed4eefd6227
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2020
ms.locfileid: "88517791"
---
# <a name="quickstart-extract-text-and-layout-information-using-the-form-recognizer-rest-api-with-python"></a>Rychlý Start: extrakce informací o textu a rozložení pomocí REST API pro rozpoznávání formulářů v Pythonu

V tomto rychlém startu použijete nástroj pro rozpoznávání formulářů Azure REST API s Pythonem k extrakci informací o rozložení textu a tabulkových datech z dokumentů formuláře.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu musíte mít:
- Je nainstalovaný [Python](https://www.python.org/downloads/) (Pokud chcete spustit ukázku místně).
- Dokument formuláře. Můžete si stáhnout obrázek ze [vzorové datové sady](https://go.microsoft.com/fwlink/?linkid=2090451) pro tento rychlý Start.

> [!NOTE]
> V tomto rychlém startu se používá místně uložený dokument. Informace o použití vzdálených souborů přístupných pomocí adresy URL najdete v [referenční dokumentaci](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync).


## <a name="create-a-form-recognizer-resource"></a>Vytvoření prostředku pro rozpoznávání formulářů

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-the-form-layout"></a>Analyzovat rozložení formuláře

Chcete-li zahájit analýzu rozložení, zavolejte rozhraní API **[analyzovat rozložení](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)** pomocí skriptu Pythonu níže. Před spuštěním skriptu proveďte tyto změny:

1. Nahraďte `<Endpoint>` koncovým bodem, který jste získali v rámci předplatného pro rozpoznávání formulářů.
1. Nahraďte `<path to your form>` cestou k místnímu dokumentu formuláře.
1. Nahraďte `<subscription key>` klíčem předplatného, který jste zkopírovali z předchozího kroku.

    ```python
    ########### Python Form Recognizer Async Layout #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<Subscription Key>"
    post_url = endpoint + "/formrecognizer/v2.0/Layout/analyze"
    source = r"<path to your form>"
    
    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```

1. Uložte kód do souboru s příponou. py. Například *Form-Recognizer-layout.py*.
1. Otevřete okno příkazového řádku.
1. Ke spuštění ukázky na příkazovém řádku použijte příkaz `python`. Například, `python form-recognizer-layout.py`.

Dostanete `202 (Success)` odpověď, která obsahuje hlavičku **umístění operace** , kterou skript vytiskne do konzoly. Tato hlavička obsahuje ID operace, pomocí které můžete zadat dotaz na stav asynchronní operace a získat výsledky. V následujícím příkladu hodnoty řetězec následuje `operations/` ID operace.

```console
https://cognitiveservice/formrecognizer/v2.0/layout/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-layout-results"></a>Získání výsledků rozložení

Po volání funkce **analyzovat rozložení** rozhraní API zavoláte rozhraní API **[výsledků získat analýzu rozložení](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeLayoutResult)** , abyste získali stav operace a extrahovaná data. Do dolní části skriptu Pythonu přidejte následující kód. Tento kód používá hodnotu ID operace v novém volání rozhraní API. Tento skript volá rozhraní API v pravidelných intervalech, dokud nebudou k dispozici výsledky. Doporučujeme interval jednoho sekundy nebo více.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Layout results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Layout Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Layout Analysis failed:\n%s" % resp_json)
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
1. Znovu použijte `python` příkaz ke spuštění ukázky. Například, `python form-recognizer-layout.py`.

### <a name="examine-the-response"></a>Prozkoumání odpovědi

Skript vytiskne odpovědi na konzolu, dokud se nedokončí operace **analyzovat rozložení** . Pak bude ve formátu JSON tisknout extrahovaná data. `"readResults"`Uzel obsahuje všechny řádky textu s příslušným umístěním ohraničovacího rámečku na stránce. `"pageResults"`Pole zobrazuje všechny části textu v tabulkách, z nichž každá má souřadnici řádku sloupce.

Podívejte se na následující obrázek faktury a příslušný výstup JSON. Výstup byl zkrácen pro zjednodušení.

> [!div class="mx-imgBorder"]
> ![Dokument faktury contoso s tabulkou](../media/contoso-invoice.png)

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-11-12T19:55:36Z",
  "lastUpdatedDateTime":"2019-11-12T19:55:43Z",
  "analyzeResult":{ 
    "version":"2.0.0",
    "readResults":[ 
      { 
        "page":1,
        "language":"en",
        "angle":0,
        "width":8.5,
        "height":11,
        "unit":"inch",
        "lines":[ 
          { 
            "language":"en",
            "boundingBox":[ 
              0.5384,
              1.1583,
              1.4466,
              1.1583,
              1.4466,
              1.3534,
              0.5384,
              1.3534
            ],
            "text":"Contoso",
            "words":[ 
              { 
                "boundingBox":[ 
                  0.5384,
                  1.1583,
                  1.4466,
                  1.1583,
                  1.4466,
                  1.3534,
                  0.5384,
                  1.3534
                ],
                "text":"Contoso",
                "confidence":1
              }
            ]
          },
          { 
            "language":"en",
            "boundingBox":[ 
              0.7994,
              1.5143,
              1.3836,
              1.5143,
              1.3836,
              1.6154,
              0.7994,
              1.6154
            ],
            "text":"Address:",
            "words":[ 
              { 
                "boundingBox":[ 
                  0.7994,
                  1.5143,
                  1.3836,
                  1.5143,
                  1.3836,
                  1.6154,
                  0.7994,
                  1.6154
                ],
                "text":"Address:",
                "confidence":1
              }
            ]
          },
          ...
          { 
            "language":"en",
            "boundingBox":[ 
              6.2285,
              3.4114,
              6.3919,
              3.4114,
              6.3919,
              3.5119,
              6.2285,
              3.5119
            ],
            "text":"PT",
            "words":[ 
              { 
                "boundingBox":[ 
                  6.2285,
                  3.4114,
                  6.3919,
                  3.4114,
                  6.3919,
                  3.5119,
                  6.2285,
                  3.5119
                ],
                "text":"PT",
                "confidence":1
              }
            ]
          }
        ]
      }
    ],
    "pageResults":[ 
      { 
        "page":1,
        "tables":[ 
          { 
            "rows":2,
            "columns":6,
            "cells":[ 
              { 
                "rowIndex":0,
                "columnIndex":0,
                "text":"Invoice Number",
                "boundingBox":[ 
                  0.5075,
                  2.8088,
                  1.9061,
                  2.8088,
                  1.9061,
                  3.3219,
                  0.5075,
                  3.3219
                ],
                "elements":[ 
                  "#/readResults/0/lines/8/words/0",
                  "#/readResults/0/lines/8/words/1"
                ]
              },
              ...
            ]
          }
        ]
      }
    ]
  }
}
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste k extrakci rozložení textu faktury použili REST API pro rozpoznávání formulářů pomocí Pythonu. Dále si přečtěte referenční dokumentaci a prozkoumejte rozhraní API pro rozpoznávání formulářů ve větší hloubkě.

> [!div class="nextstepaction"]
> [Referenční dokumentace REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)
