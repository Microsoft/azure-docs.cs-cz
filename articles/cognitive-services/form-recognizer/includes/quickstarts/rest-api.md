---
title: 'Rychlý Start: Klientská knihovna pro rozpoznávání formulářů pro .NET'
description: Použijte klientskou knihovnu pro rozpoznávání formulářů pro .NET k vytvoření aplikace pro zpracování formulářů, která extrahuje páry klíč/hodnota a tabulková data z vlastních dokumentů.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: a677623c030ff26567f14a4526f6d07bc6631a37
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/01/2021
ms.locfileid: "97853716"
---
> [!NOTE]
> V tomto průvodci se k provádění volání REST API používá oblé. K dispozici je také [ukázkový kód na GitHubu](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/FormRecognizer/rest) , který ukazuje, jak volat rozhraní REST API pomocí Pythonu.

## <a name="prerequisites"></a>Požadavky

* byl nainstalován [oblý](https://curl.haxx.se/windows/) .
* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services/) .
* Objekt blob Azure Storage, který obsahuje sadu školicích dat. Tipy a možnosti pro sestavení sady školicích dat najdete v tématu [Vytvoření školicích dat sady pro vlastní model](../../build-training-data-set.md) . Pro účely tohoto rychlého startu můžete použít soubory ve složce **výuka** [ukázkové sady dat](https://go.microsoft.com/fwlink/?linkid=2090451) (stažení a extrakce *sample_data.zip*).
* Jakmile budete mít předplatné Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" vytvořte prostředek pro rozpoznávání formulářů "  target="_blank"> vytvořením prostředku pro rozpoznávání formulářů <span class="docon docon-navigate-external x-hidden-focus"></span> </a> v Azure Portal, abyste získali svůj klíč a koncový bod. Po nasazení klikněte na **Přejít k prostředku**.
    * K připojení aplikace k rozhraní API pro rozpoznávání formulářů budete potřebovat klíč a koncový bod z prostředku, který vytvoříte. Svůj klíč a koncový bod vložíte do níže uvedeného kódu později v rychlém startu.
    * K vyzkoušení služby můžete použít bezplatnou cenovou úroveň ( `F0` ) a upgradovat ji později na placenou úroveň pro produkční prostředí.
* Adresa URL obrázku účtenky Pro tento rychlý Start můžete použít [ukázkový obrázek](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg) .
* Adresa URL obrázku vizitky Pro tento rychlý Start můžete použít [ukázkový obrázek](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg) .
* Adresa URL obrázku faktury Pro tento rychlý Start můžete použít [ukázkový dokument](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf) .


## <a name="recognize-form-content"></a>Rozpoznávání obsahu formuláře

Nástroj pro rozpoznávání formulářů můžete použít k rozpoznání a extrahování tabulek, řádků a slov v dokumentech, aniž byste museli přeškolit model. Před spuštěním příkazu proveďte tyto změny:

1. Nahraďte `{Endpoint}` koncovým bodem, který jste získali v rámci předplatného pro rozpoznávání formulářů.
1. Nahraďte `{subscription key}` klíčem předplatného, který jste zkopírovali z předchozího kroku.
1. Nahraďte adresu URL v těle žádosti jednou z ukázkových adres URL.

# <a name="v20"></a>[v2.0](#tab/v2-0)    

```bash
curl -v -X POST "https://{Endpoint}/formrecognizer/v2.0/layout/analyze"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
--data-ascii "{\"source\": \"http://example.com/test.jpg\"}" 
```

# <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1)    

```bash
curl -v -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/layout/analyze"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
--data-ascii "{\"source\": \"http://example.com/test.jpg\"}" 
```
---

Dostanete `202 (Success)` odpověď, která zahrnuje hlavičku **Operations – Location Location** . Hodnota této hlavičky obsahuje ID operace, pomocí které můžete zadat dotaz na stav asynchronní operace a získat výsledky. V následujícím příkladu řetězce následuje `analyzeResults/` ID operace.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>Získat výsledky rozložení

Po volání funkce **[analyzovat rozložení](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync)** rozhraní API zavoláte rozhraní API **[výsledků získat analýzu rozložení](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeLayoutResult)** , abyste získali stav operace a extrahovaná data. Před spuštěním příkazu proveďte tyto změny:

1. Nahraďte `{Endpoint}` koncovým bodem, který jste získali v rámci předplatného pro rozpoznávání formulářů.
1. Nahraďte `{subscription key}` klíčem předplatného, který jste zkopírovali z předchozího kroku.
1. Nahraďte `{resultId}` ID operace z předchozího kroku.

# <a name="v20"></a>[v2.0](#tab/v2-0)    

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/layout/analyzeResults/{resultId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

# <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1)  
```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/layout/analyzeResults/{resultId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```
---

### <a name="examine-the-results"></a>Prozkoumání výsledků

Dostanete `200 (success)` odpověď s obsahem JSON.

Podívejte se na následující obrázek faktury a příslušný výstup JSON. Výstup byl zkrácen pro zjednodušení. `"readResults"`Uzel obsahuje všechny řádky textu s příslušným umístěním ohraničovacího rámečku na stránce. `"selectionMarks"`Uzel (ve verzi 2.1 Preview) zobrazuje každou značku výběru (zaškrtávací políčko, přepínač) a zda má stav "vybráno" nebo "nevybrané". `"pageResults"`Pole zobrazuje všechny části textu v tabulkách, z nichž každá má souřadnici řádku sloupce.

:::image type="content" source="../../media/contoso-invoice.png" alt-text="Dokument contoso pro výpis projektu s tabulkou":::

# <a name="v20"></a>[v2.0](#tab/v2-0)    
```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:36:52Z",
    "lastUpdatedDateTime": "2020-08-20T20:36:58Z",
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
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/14/words/0",
                                    "#/readResults/0/lines/14/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
```

# <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1)   
```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:40:50Z",
    "lastUpdatedDateTime": "2020-08-20T20:40:55Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                        ]
                    }
                ],
                "selectionMarks": [
                    {
                        "boundingBox": [
                            3.9737,
                            3.7475,
                            4.1693,
                            3.7475,
                            4.1693,
                            3.9428,
                            3.9737,
                            3.9428
                        ],
                        "confidence": 0.989,
                        "state": "selected"
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/12/words/0",
                                    "#/readResults/0/lines/12/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
``` 

---

## <a name="recognize-receipts"></a>Rozpoznávání příjmů

Chcete-li zahájit analýzu účtenky, zavolejte rozhraní API **[analýzy pro příjem](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeReceiptAsync)** pomocí příkazu složeného níže. Před spuštěním příkazu proveďte tyto změny:

1. Nahraďte `{Endpoint}` koncovým bodem, který jste získali v rámci předplatného pro rozpoznávání formulářů.
1. Nahraďte `{your receipt URL}` adresou URL obrázku účtenky.
1. Nahraďte `{subscription key>` klíčem předplatného, který jste zkopírovali z předchozího kroku.

# <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ \"source\": \"{your receipt URL}\"}"
```

# <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1)    

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ \"source\": \"{your receipt URL}\"}"
```
---

Dostanete `202 (Success)` odpověď, která zahrnuje hlavičku **Operations – Location Location** . Hodnota této hlavičky obsahuje ID operace, pomocí které můžete zadat dotaz na stav asynchronní operace a získat výsledky. V následujícím příkladu řetězce následuje `operations/` ID operace.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-the-receipt-results"></a>Získání výsledků pro příjem

Po volání funkce **analyzovat příjem** rozhraní API zavoláte rozhraní API pro **[získání výsledku analýzy](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeReceiptResult)** pro získání stavu operace a extrahovaná data. Před spuštěním příkazu proveďte tyto změny:

1. Nahraďte `{Endpoint}` koncovým bodem, který jste získali pomocí klíče předplatného pro rozpoznávání formulářů. Můžete ji najít na kartě **Přehled** prostředků nástroje pro rozpoznávání formulářů.
1. Nahraďte `{operationId}` ID operace z předchozího kroku.
1. Místo `{subscription key}` použijte váš klíč předplatného.

# <a name="v20"></a>[v2.0](#tab/v2-0)  
```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```
# <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1)  
```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```
---

### <a name="examine-the-response"></a>Prozkoumání odpovědi

Dostanete `200 (Success)` odpověď s výstupem JSON. První pole `"status"` indikuje stav operace. Pokud je operace dokončena, `"readResults"` pole obsahuje každý řádek textu, který byl extrahován z účtenky, a `"documentResults"` pole obsahuje informace o klíč/hodnotě pro nejrelevantnější části účtenky. Pokud operace není dokončena, hodnota `"status"` bude `"running"` nebo `"notStarted"` a později byste měli zavolat rozhraní API, a to buď ručně, nebo prostřednictvím skriptu. Pro mezi voláními doporučujeme interval o délce jedné sekundy nebo více.

Podívejte se na následující obrázek účtenky a příslušný výstup JSON. Výstup byl zkrácen z důvodu čitelnosti.

![Účtenka z obchodu contoso](../../media/contoso-allinone.jpg)

`"readResults"`Uzel obsahuje veškerý rozpoznaný text (Pokud nastavíte volitelný parametr *includeTextDetails* na hodnotu `true` ). Text je uspořádán podle stránky, potom podle řádku, podle jednotlivých slov. `"documentResults"`Uzel obsahuje hodnoty specifické pro příjem, které model zjistil. Tady najdete užitečné páry klíč/hodnota, jako je daň, celková, obchodní adresa atd.

```json
{
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{
    "version":"2.1.0",
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

## <a name="recognize-business-cards"></a>Rozpoznávání vizitek

# <a name="v20"></a>[v2.0](#tab/v2-0)  

> [!IMPORTANT]
> Tato funkce není ve vybrané verzi rozhraní API k dispozici.

# <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1)  

Pokud chcete začít s analýzou vizitky, zavoláte rozhraní API pro **[analýzu vizitky](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)** pomocí příkazu kudrlinkou níže. Před spuštěním příkazu proveďte tyto změny:

1. Nahraďte `{Endpoint}` koncovým bodem, který jste získali v rámci předplatného pro rozpoznávání formulářů.
1. Nahraďte `{your receipt URL}` adresou URL obrázku účtenky.
1. Nahraďte `{subscription key}` klíčem předplatného, který jste zkopírovali z předchozího kroku.

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ \"source\": \"{your receipt URL}\"}"
```

Dostanete `202 (Success)` odpověď, která zahrnuje hlavičku **Operations – Location Location** . Hodnota této hlavičky obsahuje ID operace, pomocí které můžete zadat dotaz na stav asynchronní operace a získat výsledky.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-business-card-results"></a>Získání výsledků na vizitce

Po vyvolání rozhraní API pro **analýzu vizitky** zavoláte rozhraní API pro **[získání výsledku analýzy](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeBusinessCardResult)** pro získání stavu operace a extrahovaných dat. Před spuštěním příkazu proveďte tyto změny:

1. Nahraďte `{Endpoint}` koncovým bodem, který jste získali pomocí klíče předplatného pro rozpoznávání formulářů. Můžete ji najít na kartě **Přehled** prostředků nástroje pro rozpoznávání formulářů.
1. Nahraďte `{resultId}` ID operace z předchozího kroku.
1. Místo `{subscription key}` použijte váš klíč předplatného.

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/formrecognizer/v2.1-preview.2/prebuilt/businessCard/analyzeResults/{resultId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Prozkoumání odpovědi

Dostanete `200 (Success)` odpověď s výstupem JSON. `"readResults"`Uzel obsahuje veškerý rozpoznaný text. Text je uspořádán podle stránky, potom podle řádku, podle jednotlivých slov. `"documentResults"`Uzel obsahuje hodnoty specifické pro obchodní karty, které model zjistil. Tady najdete užitečné kontaktní údaje, jako je název společnosti, jméno, příjmení, telefonní číslo atd.

![Firemní karta od společnosti Contoso](../../media/business-card-english.jpg)

Tato ukázka znázorňuje výstup JSON vrácený nástrojem pro rozpoznávání formulářů. Bylo zkráceno pro čtení.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-06-04T08:19:29Z",
    "lastUpdatedDateTime": "2020-06-04T08:19:35Z",
    "analyzeResult": {
        "version": "2.1.1",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel"
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.993
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

Skript vytiskne odpovědi na konzolu, dokud se nedokončí operace **analyzovat obchodní kartu** .

---

## <a name="recognize-invoices"></a>Rozpoznávání faktur

# <a name="version-20"></a>[verze 2,0](#tab/v2-0)

> [!IMPORTANT]
> Tato funkce není ve vybrané verzi rozhraní API k dispozici.

# <a name="version-21-preview"></a>[verze 2,1 Preview](#tab/v2-1)

Chcete-li zahájit analýzu faktury, zavolejte následující příkaz **[analyzovat fakturu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9843c2794cbb1a96291)** API pomocí příkazu kudrlinkou níže. Před spuštěním příkazu proveďte tyto změny:

1. Nahraďte `{Endpoint}` koncovým bodem, který jste získali v rámci předplatného pro rozpoznávání formulářů.
1. Nahraďte `{your invoice URL}` adresou URL dokumentu faktury.
1. Místo `{subscription key}` použijte váš klíč předplatného.

```bash
curl -v -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyze"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
--data-ascii "{ \"source\": \"{your invoice URL}\"}"
```

Dostanete `202 (Success)` odpověď, která zahrnuje hlavičku **Operations – Location Location** . Hodnota této hlavičky obsahuje ID operace, pomocí které můžete zadat dotaz na stav asynchronní operace a získat výsledky.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-invoice-results"></a>Získat výsledky faktury

Poté, co jste volali rozhraní API pro **analýzu** , zavoláte rozhraní API **[výsledků získat analýzu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/5ed8c9acb78c40a2533aee83)** , které získá stav operace a extrahovaná data. Před spuštěním příkazu proveďte tyto změny:

1. Nahraďte `{Endpoint}` koncovým bodem, který jste získali pomocí klíče předplatného pro rozpoznávání formulářů. Můžete ji najít na kartě **Přehled** prostředků nástroje pro rozpoznávání formulářů.
1. Nahraďte `{resultId}` ID operace z předchozího kroku.
1. Místo `{subscription key}` použijte váš klíč předplatného.

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/prebuilt/invoice/analyzeResults/{resultId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Prozkoumání odpovědi

Dostanete `200 (Success)` odpověď s výstupem JSON. `"readResults"`Pole obsahuje všechny řádky textu, které byly extrahovány z faktury, `"pageResults"` zahrnuje tabulky a výběry, které byly extrahovány z faktury a `"documentResults"` pole obsahuje informace o klíč/hodnotě pro nejrelevantnější části faktury.

Podívejte se na následující dokument faktury a příslušný výstup JSON. Obsah JSON byl zkrácen pro čitelnost.

* [Ukázková faktura](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf)

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-11-06T23:32:11Z",
    "lastUpdatedDateTime": "2020-11-06T23:32:20Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [{
            "page": 1,
            "angle": 0,
            "width": 8.5,
            "height": 11,
            "unit": "inch"
        }],
        "pageResults": [{
            "page": 1,
            "tables": [{
                "rows": 3,
                "columns": 4,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "QUANTITY",
                    "boundingBox": [0.4953,
                    5.7306,
                    1.8097,
                    5.7306,
                    1.7942,
                    6.0122,
                    0.4953,
                    6.0122]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "DESCRIPTION",
                    "boundingBox": [1.8097,
                    5.7306,
                    5.7529,
                    5.7306,
                    5.7452,
                    6.0122,
                    1.7942,
                    6.0122]
                },
                ...
                ],
                "boundingBox": [0.4794,
                5.7132,
                8.0158,
                5.714,
                8.0118,
                6.5627,
                0.4757,
                6.5619]
            },
            {
                "rows": 2,
                "columns": 6,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "SALESPERSON",
                    "boundingBox": [0.4979,
                    4.963,
                    1.8051,
                    4.963,
                    1.7975,
                    5.2398,
                    0.5056,
                    5.2398]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "P.O. NUMBER",
                    "boundingBox": [1.8051,
                    4.963,
                    3.3047,
                    4.963,
                    3.3124,
                    5.2398,
                    1.7975,
                    5.2398]
                },
                ...
                ],
                "boundingBox": [0.4976,
                4.961,
                7.9959,
                4.9606,
                7.9959,
                5.5204,
                0.4972,
                5.5209]
            }]
        }],
        "documentResults": [{
            "docType": "prebuilt:invoice",
            "pageRange": [1,
            1],
            "fields": {
                "AmountDue": {
                    "type": "number",
                    "valueNumber": 610,
                    "text": "$610.00",
                    "boundingBox": [7.3809,
                    7.8153,
                    7.9167,
                    7.8153,
                    7.9167,
                    7.9591,
                    7.3809,
                    7.9591],
                    "page": 1,
                    "confidence": 0.875
                },
                "BillingAddress": {
                    "type": "string",
                    "valueString": "123 Bill St, Redmond WA, 98052",
                    "text": "123 Bill St, Redmond WA, 98052",
                    "boundingBox": [0.594,
                    4.3724,
                    2.0125,
                    4.3724,
                    2.0125,
                    4.7125,
                    0.594,
                    4.7125],
                    "page": 1,
                    "confidence": 0.997
                },
                "BillingAddressRecipient": {
                    "type": "string",
                    "valueString": "Microsoft Finance",
                    "text": "Microsoft Finance",
                    "boundingBox": [0.594,
                    4.1684,
                    1.7907,
                    4.1684,
                    1.7907,
                    4.2837,
                    0.594,
                    4.2837],
                    "page": 1,
                    "confidence": 0.998
                },
                ...                
            }
        }]
    }
}
```

---

## <a name="train-a-custom-model"></a>Trénování vlastního modelu

Abyste mohli vytvořit vlastní model, budete potřebovat sadu školicích dat v objektu blob Azure Storage. Měli byste mít minimálně pět vyplněných formulářů (dokumenty PDF a image) stejného typu nebo struktury jako vaše hlavní vstupní data. Nebo můžete použít jednu prázdnou formu se dvěma vyplněnými formuláři. Prázdný název souboru formuláře musí obsahovat slovo "prázdné". Tipy a možnosti pro sestavení vašich školicích dat najdete v tématu [Vytvoření školicích dat sady pro vlastní model](../../build-training-data-set.md) .

> [!NOTE]
> Funkci popisků dat můžete použít k ručnímu označení některých nebo všech vašich školicích dat. Toto je složitější proces, ale výsledkem je lepší poučený model. Další informace o této funkci najdete v části [výuka s visačkami](../../overview.md#train-with-labels) v přehledu.

Pokud chcete pomocí dokumentů v kontejneru objektů BLOB v Azure vytvořit model pro rozpoznávání formulářů, zavolejte rozhraní API pro **[vlastní modely](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** , a to spuštěním následujícího příkazu složeného. Před spuštěním příkazu proveďte tyto změny:

1. Nahraďte `{Endpoint}` koncovým bodem, který jste získali v rámci předplatného pro rozpoznávání formulářů.
1. Nahraďte `{subscription key}` klíčem předplatného, který jste zkopírovali z předchozího kroku.
1. Nahraďte `{SAS URL}` adresou URL sdíleného přístupového podpisu (SAS) kontejneru úložiště objektů BLOB v Azure. [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Načítání adresy URL SAS":::

# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ \"source\": \""{SAS URL}"\"}"
```
# <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1)    
```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ \"source\": \""{SAS URL}"\"}"
```

---


Dostanete `201 (Success)` odpověď s hlavičkou **umístění** . Hodnota tohoto záhlaví je ID nového vyučeného modelu.

### <a name="get-training-results"></a>Získat výsledky školení

Po spuštění operace vlaku použijete novou operaci, **[získáte vlastní model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** a zkontrolujete stav školení. Předejte ID modelu do tohoto volání rozhraní API, abyste zkontrolovali stav školení:

1. Nahraďte `{Endpoint}` koncovým bodem, který jste získali pomocí klíče předplatného pro rozpoznávání formulářů.
1. Nahraďte `{subscription key}` klíčem předplatného.
1. Nahraďte `{model ID}` ID modelu, které jste obdrželi v předchozím kroku.


# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```
# <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1)    
```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```
    
---

`200 (Success)`V následujícím formátu obdržíte odpověď s textem JSON. Všimněte si `"status"` pole. Tato hodnota bude mít hodnotu `"ready"` po dokončení školení. Pokud model nedokončíte, budete ho muset znovu spustit opětovným spuštěním příkazu. Pro mezi voláními doporučujeme interval o délce jedné sekundy nebo více.

`"modelId"`Pole obsahuje ID modelu, který budete školením. Budete ho potřebovat pro další krok.

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

## <a name="analyze-forms-with-a-custom-model"></a>Analýza formulářů pomocí vlastního modelu

V dalším kroku použijete svůj nově vyškolený model k analýze dokumentu a extrakci párů klíč-hodnota a tabulek z něj. Zavolejte rozhraní API pro **[analýzu formuláře](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** spuštěním následujícího příkazu složeného. Před spuštěním příkazu proveďte tyto změny:

1. Nahraďte `{Endpoint}` koncovým bodem, který jste získali z klíče předplatného pro rozpoznávání formulářů. Můžete ji najít na kartě **Přehled** prostředků nástroje pro rozpoznávání formulářů.
1. Nahraďte `{model ID}` ID modelu, které jste obdrželi v předchozí části.
1. Nahraďte `{SAS URL}` adresou URL SAS k souboru ve službě Azure Storage. Postupujte podle kroků v části školení, ale místo získání adresy URL SAS pro celý kontejner objektů BLOB Získejte jeden pro konkrétní soubor, který chcete analyzovat.
1. Místo `{subscription key}` použijte váš klíč předplatného.

# <a name="v20"></a>[v2.0](#tab/v2-0)    

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ \"source\": \""{SAS URL}"\" } "
```

# <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1)    
```bash
curl -v "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models/{model ID}/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ \"source\": \""{SAS URL}"\" } "
```
    
---



Dostanete `202 (Success)` odpověď s hlavičkou **umístění operace** . Hodnota této hlavičky zahrnuje ID výsledků, které používáte ke sledování výsledků operace analyzovat. Uložte toto ID výsledků pro další krok.

### <a name="get-the-analyze-results"></a>Získat výsledky analýzy

Pomocí následujícího rozhraní API můžete zadat dotaz na výsledky operace analyzovat.

1. Nahraďte `{Endpoint}` koncovým bodem, který jste získali z klíče předplatného pro rozpoznávání formulářů. Můžete ji najít na kartě **Přehled** prostředků nástroje pro rozpoznávání formulářů.
1. Nahraďte `{result ID}` ID, které jste obdrželi v předchozí části.
1. Místo `{subscription key}` použijte váš klíč předplatného.

# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```
# <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1)    
```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```
---

`200 (Success)`V následujícím formátu obdržíte odpověď s textem JSON. Výstup byl zkrácen pro zjednodušení. Všimněte si `"status"` pole poblíž dolního okraje. Tato hodnota bude mít hodnotu `"succeeded"` po dokončení operace analyzovat. Pokud se operace analýzy nedokončila, budete se muset znovu dotázat na službu opětovným spuštěním příkazu. Pro mezi voláními doporučujeme interval o délce jedné sekundy nebo více.

Hlavní přidružení páru klíč/hodnota a tabulky jsou v `"pageResults"` uzlu. Pokud jste zadali také extrakci prostého textu prostřednictvím parametru adresy URL *includeTextDetails* , `"readResults"` uzel zobrazí obsah a pozice veškerého textu v dokumentu.

Tento ukázkový výstup JSON byl zkrácen pro zjednodušení.

# <a name="v20"></a>[v2.0](#tab/v2-0)
```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T00:46:25Z",
  "lastUpdatedDateTime": "2020-08-21T00:46:32Z",
  "analyzeResult": {
    "version": "2.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0153,
              0.275,
              8.0944,
              0.275,
              8.0944,
              0.7125,
              5.0153,
              0.7125
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0153,
                  0.275,
                  6.2278,
                  0.275,
                  6.2278,
                  0.7125,
                  5.0153,
                  0.7125
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3292,
                  0.275,
                  8.0944,
                  0.275,
                  8.0944,
                  0.7125,
                  6.3292,
                  0.7125
                ]
              }
            ]
          }, 
        ...
        ]
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9722,
                1.0264,
                7.3417,
                1.0264,
                7.3417,
                1.1931,
                6.9722,
                1.1931
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "confidence": 1
          },
         ...
        ],
        "tables": [
          {
            "rows": 4,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6931,
                  4.2444,
                  1.5681,
                  4.2444,
                  1.5681,
                  4.4125,
                  0.6931,
                  4.4125
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ],
    "documentResults": [],
    "errors": []
  }
}
```    
# <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1)    
```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T01:13:28Z",
  "lastUpdatedDateTime": "2020-08-21T01:13:42Z",
  "analyzeResult": {
    "version": "2.1.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0444,
              0.3613,
              8.0917,
              0.3613,
              8.0917,
              0.6718,
              5.0444,
              0.6718
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0444,
                  0.3587,
                  6.2264,
                  0.3587,
                  6.2264,
                  0.708,
                  5.0444,
                  0.708
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3361,
                  0.3635,
                  8.0917,
                  0.3635,
                  8.0917,
                  0.6396,
                  6.3361,
                  0.6396
                ]
              }
            ]
          }, 
          ...
        ] 
      }
    ],
    "pageResults": [
      {
        "page": 1,
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9833,
                1.0615,
                7.3333,
                1.0615,
                7.3333,
                1.1649,
                6.9833,
                1.1649
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "value": {
              "text": "9/10/2020",
              "boundingBox": [
                7.3833,
                1.0802,
                7.925,
                1.0802,
                7.925,
                1.174,
                7.3833,
                1.174
              ],
              "elements": [
                "#/readResults/0/lines/3/words/0"
              ]
            },
            "confidence": 1
          },
          ...
        ], 
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6944,
                  4.2779,
                  1.5625,
                  4.2779,
                  1.5625,
                  4.4005,
                  0.6944,
                  4.4005
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ], 
    "documentResults": [],
    "errors": []
  }
}
``` 
---

### <a name="improve-results"></a>Zlepšení výsledků

[!INCLUDE [improve results](../improve-results-unlabeled.md)]

## <a name="manage-custom-models"></a>Správa vlastních modelů

### <a name="get-a-list-of-custom-models"></a>Získat seznam vlastních modelů

Pomocí následujícího příkazu vraťte seznam všech vlastních modelů, které patří do vašeho předplatného.

1. Nahraďte `{Endpoint}` koncovým bodem, který jste získali v rámci předplatného pro rozpoznávání formulářů.
1. Nahraďte `{subscription key}` klíčem předplatného, který jste zkopírovali z předchozího kroku.

# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

# <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1)    

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```
---

Dostanete `200` odpověď na úspěch s daty JSON, jako je následující. `"modelList"`Element obsahuje všechny vaše vytvořené modely a jejich informace.

```json
{
  "summary": {
    "count": 0,
    "limit": 0,
    "lastUpdatedDateTime": "string"
  },
  "modelList": [
    {
      "modelId": "string",
      "status": "creating",
      "createdDateTime": "string",
      "lastUpdatedDateTime": "string"
    }
  ],
  "nextLink": "string"
}
```

### <a name="get-a-specific-model"></a>Získat konkrétní model

Chcete-li získat podrobné informace o konkrétním vlastním modelu, použijte následující příkaz.

1. Nahraďte `{Endpoint}` koncovým bodem, který jste získali v rámci předplatného pro rozpoznávání formulářů.
1. Nahraďte `{subscription key}` klíčem předplatného, který jste zkopírovali z předchozího kroku.
1. Nahraďte `{modelId}` ID vlastního modelu, který chcete vyhledat.

# <a name="v20"></a>[v2.0](#tab/v2-0)    

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
--data-ascii "{body}" 
```

# <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1)    

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.2/custom/models/{modelId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
--data-ascii "{body}" 
```
---

Dostanete `200` odpověď na úspěch s daty JSON, jako je následující.

```json
{
  "modelInfo": {
    "modelId": "string",
    "status": "creating",
    "createdDateTime": "string",
    "lastUpdatedDateTime": "string"
  },
  "keys": {
    "clusters": {}
  },
  "trainResult": {
    "trainingDocuments": [
      {
        "documentName": "string",
        "pages": 0,
        "errors": [
          "string"
        ],
        "status": "succeeded"
      }
    ],
    "fields": [
      {
        "fieldName": "string",
        "accuracy": 0.0
      }
    ],
    "averageModelAccuracy": 0.0,
    "errors": [
      {
        "message": "string"
      }
    ]
  }
}
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili nástroj pro rozpoznávání formulářů REST API k vytváření výukových modelů a k analýze formulářů různými způsoby. Dále si přečtěte referenční dokumentaci a prozkoumejte rozhraní API pro rozpoznávání formulářů ve větší hloubkě.

> [!div class="nextstepaction"]
> [Referenční dokumentace REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)

* [Co je služba Rozpoznávání formulářů?](../../overview.md)
