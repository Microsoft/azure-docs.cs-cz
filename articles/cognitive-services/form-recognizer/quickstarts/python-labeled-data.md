---
title: 'Úvodní příručka: Trénování pomocí popisků pomocí rozhraní REST API a Pythonu – rozpoznávání formulářů'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak pomocí funkce rozpoznávání formulářů s popiskem dat s rozhraním REST API a Pythonem trénovat vlastní model.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: pafarley
ms.openlocfilehash: 36ded3bd85cd7acdffbfe46b9e931a811994fa30
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531096"
---
# <a name="train-a-form-recognizer-model-with-labels-using-rest-api-and-python"></a>Trénování modelu rozpoznávání formulářů s popisky pomocí rozhraní REST API a Pythonu

V tomto rychlém startu použijete rozhraní REST API nástroje pro rozpoznávání formulářů s Pythonem k trénování vlastního modelu s ručně označenými daty. Další informace o této funkci najdete v části [Vlak s popisky](../overview.md#train-with-labels) v přehledu.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit tento rychlý start, musíte mít:
- [Python](https://www.python.org/downloads/) nainstalován (pokud chcete spustit ukázku místně).
- Sada nejméně šesti forem stejného typu. Tato data použijete k trénování modelu a testování formuláře. Pro tento rychlý start můžete použít [ukázkovou sadu dat.](https://go.microsoft.com/fwlink/?linkid=2090451) Nahrajte školicí soubory do kořenového adresáře kontejneru úložiště objektů blob v účtu Azure Storage.

## <a name="create-a-form-recognizer-resource"></a>Vytvoření prostředku nástroje pro rozpoznávání formulářů

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="set-up-training-data"></a>Nastavení trénovacích dat

Dále budete muset nastavit požadovaná vstupní data. Funkce dat označená popiskem má zvláštní vstupní požadavky nad rámec těch, které jsou potřebné k trénování vlastního modelu. 

Ujistěte se, že všechny školicí dokumenty jsou ve stejném formátu. Pokud máte formuláře ve více formátech, uspořádejte je do podsložek na základě běžného formátu. Když trénujete, budete muset rozhraní API nasměrovat do podsložky.

Chcete-li trénovat model pomocí označených dat, budete potřebovat následující soubory jako vstupy v podsložce. Níže se dozvíte, jak vytvořit tyto soubory.

* **Zdrojové formuláře** – formuláře pro extrahování dat. Podporované typy jsou JPEG, PNG, PDF nebo TIFF.
* **Soubory rozložení OCR** - Soubory JSON, které popisují velikosti a umístění veškerého čitelného textu v každém zdrojovém formuláři. Ke generování těchto dat použijete rozhraní API pro rozpoznávání formulářů. 
* **Label files** - JSON files that describe data labels that a user has entered manually.

Všechny tyto soubory by měly zabírat stejnou podsložku a měly by být v následujícím formátu:

* input_file1.pdf 
* input_file1.pdf.ocr.json
* input_file1.pdf.labels.json 
* input_file2.pdf 
* input_file2.pdf.ocr.json
* input_file2.pdf.labels.json
* ...

> [!TIP]
> Když vytvoříte popisky formulářů pomocí [nástroje pro vzorové popisky](./label-tool.md)nástroje rozpoznávání formulářů , nástroj automaticky vytvoří tyto soubory popisků a rozložení rozpoznávání OCR.

### <a name="create-the-ocr-output-files"></a>Vytvoření výstupních souborů Rozpoznávání OCR

Potřebujete OCR výsledky soubory, aby služba, aby zvážila odpovídající vstupní soubory pro školení s popiskem. Chcete-li získat výsledky Rozpoznávání OCR pro daný zdrojový formulář, postupujte podle následujících kroků:

1. Volání **[rozhraní Analyzovat rozhraní](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeLayoutAsync)** API rozložení v kontejneru rozložení pro čtení se vstupním souborem jako součást těla požadavku. Uložte ID nalezené v hlavičce **Operace lokace** odpovědi.
1. Volání **[Získat analyzovat rozložení výsledek](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetAnalyzeLayoutResult)** rozhraní API, pomocí ID operace z předchozího kroku.
1. Získejte odpověď a zapište obsah do souboru. Pro každý zdrojový formulář by měl mít odpovídající soubor OCR připojen původní název souboru `.ocr.json`. Výstup OCR JSON by měl mít následující formát. Úplný příklad naleznete v [ukázkovém souboru OCR.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.ocr.json) 

    ```json
    {
    "status": "succeeded",
    "createdDateTime": "2019-11-12T21:18:12Z",
    "lastUpdatedDateTime": "2019-11-12T21:18:17Z",
    "analyzeResult": {
        "version": "2.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "language": "en",
                        "boundingBox": [
                            0.5384,
                            1.1583,
                            1.4466,
                            1.1583,
                            1.4466,
                            1.3534,
                            0.5384,
                            1.3534
                        ],
                        "text": "Contoso",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5384,
                                    1.1583,
                                    1.4466,
                                    1.1583,
                                    1.4466,
                                    1.3534,
                                    0.5384,
                                    1.3534
                                ],
                                "text": "Contoso",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
    ```

### <a name="create-the-label-files"></a>Vytvoření souborů popisků

Soubory popisků obsahují přidružení klíč-hodnota, která uživatel zadal ručně. Jsou potřebné pro školení označených dat, ale ne každý zdrojový soubor musí mít odpovídající soubor popisků. Zdrojové soubory bez štítků budou považovány za běžné školicí dokumenty. Pro spolehlivé školení doporučujeme pět nebo více označených souborů.

Při vytváření souboru popisků můžete volitelně určit přesné&mdash;umístění hodnot v dokumentu. To dá tréninku ještě vyšší přesnost. Oblasti jsou formátovány jako sada osmi hodnot odpovídajících čtyřem souřadnicím X,Y: vlevo nahoře, vpravo nahoře, vpravo dole a vlevo dole. Hodnoty souřadnic jsou mezi nulou a jednou, měřítko na rozměry stránky.

Pro každý zdrojový formulář by měl mít odpovídající soubor `.labels.json`popisku připojen původní název souboru . Soubor popisku by měl mít následující formát. Úplný příklad naleznete v [ukázkovém souboru popisků.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/Invoice_1.pdf.labels.json)

```json
{
    "document": "Invoice_1.pdf",
    "labels": [
        {
            "label": "Provider",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Contoso",
                    "boundingBoxes": [
                        [
                            0.06334117647058823,
                            0.1053,
                            0.17018823529411767,
                            0.1053,
                            0.17018823529411767,
                            0.12303636363636362,
                            0.06334117647058823,
                            0.12303636363636362
                        ]
                    ]
                }
            ]
        },
        {
            "label": "For",
            "key": null,
            "value": [
                {
                    "page": 1,
                    "text": "Microsoft",
                    "boundingBoxes": [
                        [
                            0.6122941176470589,
                            0.1374,
                            0.6841764705882353,
                            0.1374,
                            0.6841764705882353,
                            0.14682727272727272,
                            0.6122941176470589,
                            0.14682727272727272
                        ]
                    ]
                },
                {
                    "page": 1,
                    "text": "1020",
                    "boundingBoxes": [
                        [
                            0.6121882352941176,
                            0.156,
                            0.6462941176470588,
                            0.156,
                            0.6462941176470588,
                            0.1653181818181818,
                            0.6121882352941176,
                            0.1653181818181818
                        ]
                    ]
                },
                ...
```

> [!NOTE]
> Na každý textový prvek lze aplikovat pouze jeden popisek a každý popisek lze použít pouze jednou na stránku. V současné době nelze použít popisek na více stránkách.


## <a name="train-a-model-using-labeled-data"></a>Trénování modelu pomocí označených dat

Chcete-li trénovat model s označenými daty, zavolejte **[rozhraní TRAIN Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)** API spuštěním následujícího kódu pythonu. Před spuštěním kódu proveďte tyto změny:

1. Nahraďte `<Endpoint>` adresu URL koncového bodu pro prostředek nástroje pro rozpoznávání formulářů.
1. Nahraďte `<SAS URL>` adresu URL sdíleného přístupového podpisu (SAS) kontejneru azure blob. Chcete-li načíst adresu URL SAS, otevřete Průzkumníka úložiště Microsoft Azure, klikněte pravým tlačítkem myši na kontejner a vyberte **získat sdílený přístupový podpis**. Zkontrolujte, zda jsou zaškrtnuta oprávnění **Číst** a **seznam,** a klepněte na **tlačítko Vytvořit**. Pak zkopírujte hodnotu v části **ADRESA URL.** Měl by mít `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`formu: .
1. Nahraďte `<Blob folder name>` název složky v kontejneru objektů blob, kde jsou umístěna vstupní data. Nebo pokud jsou data v kořenovém adresáři, ponechejte toto pole prázdné a odeberte `"prefix"` pole z těla požadavku HTTP.

```python
########### Python Form Recognizer Labeled Async Train #############
import json
import time
from requests import get, post

# Endpoint URL
endpoint = r"<Endpoint>"
post_url = endpoint + r"/formrecognizer/v2.0-preview/custom/models"
source = r"<SAS URL>"
prefix = "<Blob folder name>"
includeSubFolders = False
useLabelFile = True

headers = {
    # Request headers
    'Content-Type': 'application/json',
    'Ocp-Apim-Subscription-Key': '<subsription key>',
}

body =     {
    "source": source,
    "sourceFilter": {
        "prefix": prefix,
        "includeSubFolders": includeSubFolders
    },
    "useLabelFile": useLabelFile
}

try:
    resp = post(url = post_url, json = body, headers = headers)
    if resp.status_code != 201:
        print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
        quit()
    print("POST model succeeded:\n%s" % resp.headers)
    get_url = resp.headers["location"]
except Exception as e:
    print("POST model failed:\n%s" % str(e))
    quit() 
```

## <a name="get-training-results"></a>Získejte výsledky školení

Po spuštění operace vlaku použijete vrácené ID k získání stavu operace. Přidejte následující kód do dolní části skriptu Pythonu. To používá hodnotu ID z volání školení v nové volání rozhraní API. Operace školení je asynchronní, takže tento skript volá rozhraní API v pravidelných intervalech, dokud není dokončen stav školení. Doporučujeme interval jedné sekundy nebo více.

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = headers)
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
            quit()
        model_status = resp_json["modelInfo"]["status"]
        if model_status == "ready":
            print("Training succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if model_status == "invalid":
            print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
            quit()
        # Training still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET model failed:\n%s" % str(e)
        print(msg)
        quit()
print("Train operation did not complete within the allocated time.")
```

Po dokončení procesu školení obdržíte `201 (Success)` odpověď s obsahem JSON, jako je následující. Odezva byla zkrácena pro jednoduchost.

```json
{ 
  "modelInfo":{ 
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{ 
    "trainingDocuments":[ 
      { 
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      }
    ],
    "errors":[ 

    ]
  },
  "keys":{ 
    "0":[ 
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

Zkopírujte `"modelId"` hodnotu pro použití v následujících krocích.

[!INCLUDE [analyze forms](../includes/python-custom-analyze.md)]

Po dokončení procesu obdržíte odpověď `202 (Success)` s obsahem JSON v následujícím formátu. Odezva byla zkrácena pro jednoduchost. Přidružení hlavního klíče/hodnoty `"documentResults"` jsou v uzlu. Výsledky rozhraní API rozložení (obsah a umístění veškerého textu `"readResults"` v dokumentu) jsou v uzlu.

```json
{ 
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
                0.5375,
                1.1349,
                2.6064,
                1.1349,
                2.6064,
                1.354,
                0.5375,
                1.354
              ],
              "text":"Contoso Suites",
              "words":[ 
                { 
                  "boundingBox":[ 
                    0.5375,
                    1.1402,
                    1.6595,
                    1.1402,
                    1.6595,
                    1.354,
                    0.5375,
                    1.354
                  ],
                  "text":"Contoso",
                  "confidence":1
                },
                { 
                  "boundingBox":[ 
                    1.758,
                    1.1349,
                    2.6064,
                    1.1349,
                    2.6064,
                    1.3534,
                    1.758,
                    1.3534
                  ],
                  "text":"Suites",
                  "confidence":1
                }
              ]
            },
            ...
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
                    "#/readResults/0/lines/7/words/0",
                    "#/readResults/0/lines/7/words/1"
                  ]
                },
                { 
                  "rowIndex":0,
                  "columnIndex":1,
                  "text":"Invoice Date",
                  "boundingBox":[ 
                    1.9061,
                    2.8088,
                    3.3074,
                    2.8088,
                    3.3074,
                    3.3219,
                    1.9061,
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
      ],
      "documentResults":[ 
        { 
          "docType":"Analyze",
          "pageRange":[ 
            1,
            1
          ],
          "fields":{ 
            "total":{ 
              "type":"string",
              "valueString":"$22,123.24",
              "text":"$22,123.24",
              "boundingBox":[ 
                5.29,
                3.41,
                5.9750000000000009,
                3.41,
                5.9750000000000009,
                3.54,
                5.29,
                3.54
              ],
              "page":1,
              "confidence":1,
              "elements":[ 
                { 
                  "$ref":"#/analyzeResult/readResults/0/lines/15/words/0"
                }
              ],
              "fieldName":"total"
            },
            "invoice #":{ 
              "type":"string",
              "valueString":"7689302",
              "text":"7689302",
              "boundingBox":[ 
                0.54,
                3.41,
                1.065,
                3.41,
                1.065,
                3.515,
                0.54,
                3.515
              ],
              "page":1,
              "confidence":1,
              "elements":[ 
                { 
                  "$ref":"#/analyzeResult/readResults/0/lines/12/words/0"
                }
              ],
              "fieldName":"invoice #"
            },
            "vat":{ 
              "type":"string",
              "valueString":"QR",
              "text":"QR",
              "boundingBox":[ 
                6.2250000000000009,
                3.41,
                6.425,
                3.41,
                6.425,
                3.52,
                6.2250000000000009,
                3.52
              ],
              "page":1,
              "confidence":0.9839357733726502,
              "elements":[ 
                { 
                  "$ref":"#/analyzeResult/readResults/0/lines/16/words/0"
                }
              ],
              "fieldName":"vat"
            },
            ...
          }
        }
      ]
    },
    "status":"succeeded",
    "createdDateTime":"2019-11-12T21:26:19+00:00",
    "lastUpdatedDateTime":"2019-11-12T21:27:27.0488571+00:00"
}
```

## <a name="improve-results"></a>Zlepšete výsledky

Zkontrolujte `"confidence"` hodnoty pro každý výsledek `"documentResults"` klíče/hodnoty pod uzlem. Měli byste se také podívat `"readResults"` na skóre spolehlivosti v uzlu, které odpovídají operaci rozložení. Důvěra výsledků rozvržení nemá vliv na důvěru výsledků extrakce klíče/hodnoty, takže byste měli zkontrolovat obojí.
* Pokud jsou skóre spolehlivosti operace rozložení nízké, zkuste zlepšit kvalitu vstupních dokumentů (viz [Požadavky na vstup](../overview.md#input-requirements)).
* Pokud jsou skóre spolehlivosti pro operaci extrakce klíč/hodnota nízká, ujistěte se, že analyzované dokumenty jsou stejného typu jako dokumenty použité v trénovací sadě. Pokud dokumenty v trénovací sadě mají varianty vzhledu, zvažte jejich rozdělení do různých složek a školení jeden model pro každou variantu.

### <a name="avoid-cluttered-labels"></a>Vyhněte se přeplněným štítkům

Někdy, když použijete různé popisky v rámci stejného řádku textu, může služba tyto popisky sloučit do jednoho pole. Například v adrese můžete označit město, stát a PSČ jako různá pole, ale během předpovědi tato pole nejsou rozpoznána samostatně.

Chápeme, že tento scénář je pro naše zákazníky nezbytný, a v budoucnu pracujeme na jeho zlepšení. V současné době doporučujeme našim uživatelům označit více přeplněných polí jako jedno pole a potom oddělit termíny v post-zpracování výsledků extrakce.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili používat rozhraní REST API nástroje pro rozpoznávání formulářů s Pythonem k trénování modelu s ručně označenými daty. Dále naleznete [v referenční dokumentaci rozhraní API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm) prozkoumat rozhraní API rozpoznávání formulářů podrobněji.
