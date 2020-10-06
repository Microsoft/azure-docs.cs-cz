---
title: 'Rychlý Start: extrakce příjmových dat pomocí funkce pro rozpoznávání složeného formuláře'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete REST API pro rozpoznávání formulářů s kudrlinkou k extrakci dat z imagí prodejních příjmů v USA.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: pafarley
ms.openlocfilehash: 282b8e1292bf1fe24655691fbbeb876d871bc31e
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761341"
---
# <a name="quickstart-extract-receipt-data-using-the-form-recognizer-rest-api-with-curl"></a>Rychlý Start: extrakce údajů o příjemcích pomocí REST API pro rozpoznávání formulářů s kudrlinkou

V tomto rychlém startu použijete REST API pro rozpoznávání formulářů Azure s kudrlinkou k extrakci a identifikaci relevantních informací z prodejních příjmů v USA.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto rychlého startu musíte mít:
- byl nainstalován [oblý](https://curl.haxx.se/windows/) .
- Adresa URL obrázku účtenky Pro tento rychlý Start můžete použít [ukázkový obrázek](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg) .

## <a name="create-a-form-recognizer-resource"></a>Vytvoření prostředku pro rozpoznávání formulářů

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-receipt"></a>Analýza účtenky

Chcete-li zahájit analýzu účtenky, zavolejte rozhraní API **[analýzy pro příjem](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeReceiptAsync)** pomocí příkazu složeného níže. Před spuštěním příkazu proveďte tyto změny:

1. Nahraďte `<Endpoint>` koncovým bodem, který jste získali v rámci předplatného pro rozpoznávání formulářů.
1. Nahraďte `<your receipt URL>` adresou URL obrázku účtenky.
1. Nahraďte `<subscription key>` klíčem předplatného, který jste zkopírovali z předchozího kroku.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v2.1-preview.1/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \"<your receipt URL>\"}"
```

Dostanete `202 (Success)` odpověď, která zahrnuje hlavičku **Operations – Location Location** . Hodnota této hlavičky obsahuje ID operace, pomocí které můžete zadat dotaz na stav asynchronní operace a získat výsledky. V následujícím příkladu řetězce následuje `operations/` ID operace.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.1/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-receipt-results"></a>Získání výsledků pro příjem

Po volání funkce **analyzovat příjem** rozhraní API zavoláte rozhraní API pro **[získání výsledku analýzy](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/GetAnalyzeReceiptResult)** pro získání stavu operace a extrahovaná data. Před spuštěním příkazu proveďte tyto změny:

1. Nahraďte `<Endpoint>` koncovým bodem, který jste získali pomocí klíče předplatného pro rozpoznávání formulářů. Můžete ji najít na kartě **Přehled** prostředků nástroje pro rozpoznávání formulářů.
1. Nahraďte `<operationId>` ID operace z předchozího kroku.
1. Místo `<subscription key>` použijte váš klíč předplatného.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.1-preview.1/prebuilt/receipt/analyzeResults/<operationId>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>Prozkoumání odpovědi

Dostanete `200 (Success)` odpověď s výstupem JSON. První pole `"status"` indikuje stav operace. Pokud je operace dokončena, `"readResults"` pole obsahuje každý řádek textu, který byl extrahován z účtenky, a `"documentResults"` pole obsahuje informace o klíč/hodnotě pro nejrelevantnější části účtenky. Pokud operace není dokončena, hodnota `"status"` bude `"running"` nebo `"notStarted"` a později byste měli zavolat rozhraní API, a to buď ručně, nebo prostřednictvím skriptu. Pro mezi voláními doporučujeme interval o délce jedné sekundy nebo více.

Podívejte se na následující obrázek účtenky a příslušný výstup JSON. Výstup byl zkrácen z důvodu čitelnosti.

![Účtenka z obchodu contoso](../media/contoso-allinone.jpg)

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

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste k extrakci obsahu prodejní příjemky použili REST API pro rozpoznávání formuláře s kudrlinkou. Dále si přečtěte referenční dokumentaci a prozkoumejte rozhraní API pro rozpoznávání formulářů ve větší hloubkě.

> [!div class="nextstepaction"]
> [Referenční dokumentace REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeReceiptAsync)
