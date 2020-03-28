---
title: 'Úvodní příručka: Extrahování informací o textu a rozložení pomocí Pythonu – rozpoznávání formulářů'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu budete ke čtení textových a tabulkových dat z formulářů používat rozhraní REST API pro rozpoznávání formulářů s Pythonem.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: pafarley
ms.openlocfilehash: 342ae7e42c85ad661c04ba4ebb6629673f4af4dc
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77482272"
---
# <a name="quickstart-extract-text-and-layout-information-using-the-form-recognizer-rest-api-with-python"></a>Úvodní příručka: Extrahování informací o textu a rozložení pomocí rozhraní REST API nástroje pro rozpoznávání formulářů pomocí Pythonu

V tomto rychlém startu použijete rozhraní REST API nástroje Azure Pro rozpoznávání formulářů s Pythonem k extrahování informací o rozložení textu a dat tabulek z dokumentů formuláře.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit tento rychlý start, musíte mít:
- [Python](https://www.python.org/downloads/) nainstalován (pokud chcete spustit ukázku místně).
- Formulářový dokument. Obrázek si můžete stáhnout z [ukázkové datové sady](https://go.microsoft.com/fwlink/?linkid=2090451) pro tento rychlý start.

## <a name="create-a-form-recognizer-resource"></a>Vytvoření prostředku nástroje pro rozpoznávání formulářů

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-the-form-layout"></a>Analýza rozložení formuláře

Chcete-li začít analyzovat rozložení, zavolejte **[analyzovat rozhraní API rozložení](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)** pomocí skriptu Pythonu níže. Před spuštěním skriptu proveďte tyto změny:

1. Nahraďte `<Endpoint>` koncovýbod, který jste získali s odběrem nástroje pro rozpoznávání formulářů.
1. Nahraďte `<path to your form>` ji cestou k místnímu dokumentu formuláře.
1. Nahraďte `<subscription key>` klíčem předplatného, který jste zkopírovali z předchozího kroku.

    ```python
    ########### Python Form Recognizer Async Layout #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<Subscription Key>"
    post_url = endpoint + "/formrecognizer/v2.0-preview/Layout/analyze"
    source = r"<path to your form>"
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
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

1. Uložte kód do souboru s příponou Py. Například *form-recognizer-layout.py*.
1. Otevřete okno příkazového řádku.
1. Ke spuštění ukázky na příkazovém řádku použijte příkaz `python`. Například, `python form-recognizer-layout.py`.

Obdržíte `202 (Success)` odpověď, která obsahuje hlavičku **Umístění operace,** kterou bude skript vytisknout do konzoly. Tato hlavička obsahuje ID operace, které můžete použít k dotazování na stav asynchronní operace a získat výsledky. V následujícím příkladu hodnota `operations/` řetězec po je ID operace.

```console
https://cognitiveservice/formrecognizer/v2.0-preview/layout/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-layout-results"></a>Získání výsledků rozvržení

Po volání analyzovat rozhraní API **rozložení,** zavoláte **[získat analyzovat rozložení výsledek](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeLayoutResult)** rozhraní API získat stav operace a extrahovaná data. Přidejte následující kód do dolní části skriptu Pythonu. Tento kód používá hodnotu ID operace v novém volání rozhraní API. Tento skript volá rozhraní API v pravidelných intervalech, dokud nejsou k dispozici výsledky. Doporučujeme interval jedné sekundy nebo více.

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

Skript bude tisknout odpovědi na konzoli, dokud nebude dokončena operace **Analyzovat rozložení.** Potom bude tisknout extrahovaná data ve formátu JSON. Uzel `"readResults"` obsahuje každý řádek textu s příslušným umístěním ohraničovacího rámečku na stránce. Pole `"pageResults"` zobrazuje každý kus textu v tabulkách, každý s řadovou souřadnicí.

Podívejte se na následující obrázek faktury a jeho odpovídající výstup JSON. Výstup byl zkrácen pro jednoduchost.

> [!div class="mx-imgBorder"]
> ![Doklad faktury společnosti Contoso s tabulkou](../media/contoso-invoice.png)

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

V tomto rychlém startu jste použili rozhraní REST API nástroje pro rozpoznávání formulářů s Pythonem k extrahování rozložení textu faktury. Dále naleznete v referenční dokumentaci k podrobnějšímu prozkoumání rozhraní API pro rozpoznávání formulářů.

> [!div class="nextstepaction"]
> [Referenční dokumentace rozhraní REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)
