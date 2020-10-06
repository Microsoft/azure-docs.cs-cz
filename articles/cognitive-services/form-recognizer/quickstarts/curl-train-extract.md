---
title: 'Rychlý Start: výuka modelu a extrakce dat formuláře pomocí funkce pro rozpoznávání složeného formuláře'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete REST API pro rozpoznávání formulářů s kudrlinkou k učení modelu a extrakce dat z formulářů.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: pafarley
ms.openlocfilehash: b0532007ff03cd9dcf253824a1158fd0b8661120
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91760457"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Rychlý Start: výuka modelu pro rozpoznávání formulářů a extrakce dat formuláře pomocí REST API s kudrlinkou

V tomto rychlém startu použijete nástroj pro rozpoznávání formulářů Azure REST API s kudrlinkou ke výukám a k extrakci formulářů a jejich skóre a k extrakci párů klíč-hodnota a tabulek.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto rychlého startu musíte mít:
- byl nainstalován [oblý](https://curl.haxx.se/windows/) .
- Sada alespoň šest forem stejného typu. Pro výuku modelu použijete pět z nich a potom ho otestujete s šestým formulářem. Formuláře mohou mít různé typy souborů, ale musí se jednat o stejný typ dokumentu. Pro tento rychlý Start můžete použít [ukázkovou datovou sadu](https://go.microsoft.com/fwlink/?linkid=2090451) . Nahrajte školicí soubory do kořenového adresáře kontejneru úložiště objektů BLOB v účtu Azure Storage úrovně Standard-Performance. Testovací soubory můžete umístit do samostatné složky.

## <a name="create-a-form-recognizer-resource"></a>Vytvoření prostředku pro rozpoznávání formulářů

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Výuka modelu pro rozpoznávání formulářů

Nejdřív budete potřebovat sadu školicích dat v objektu blob Azure Storage. Měli byste mít minimálně pět vyplněných formulářů (dokumenty PDF a image) stejného typu nebo struktury jako vaše hlavní vstupní data. Nebo můžete použít jednu prázdnou formu se dvěma vyplněnými formuláři. Prázdný název souboru formuláře musí obsahovat slovo "prázdné". Tipy a možnosti pro sestavení vašich školicích dat najdete v tématu [Vytvoření školicích dat sady pro vlastní model](../build-training-data-set.md) .

> [!NOTE]
> Funkci popisků dat můžete použít k ručnímu označení některých nebo všech vašich školicích dat. Toto je složitější proces, ale výsledkem je lepší poučený model. Další informace o této funkci najdete v části [výuka s visačkami](../overview.md#train-with-labels) v přehledu.



Pokud chcete pomocí dokumentů v kontejneru objektů BLOB v Azure vytvořit model pro rozpoznávání formulářů, zavolejte rozhraní API pro **[vlastní modely](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)** , a to spuštěním následujícího příkazu složeného. Před spuštěním příkazu proveďte tyto změny:

1. Nahraďte `<Endpoint>` koncovým bodem, který jste získali v rámci předplatného pro rozpoznávání formulářů.
1. Nahraďte `<subscription key>` klíčem předplatného, který jste zkopírovali z předchozího kroku.
1. Nahraďte `<SAS URL>` adresou URL sdíleného přístupového podpisu (SAS) kontejneru úložiště objektů BLOB v Azure. Pokud chcete načíst adresu URL SAS, otevřete Průzkumník služby Microsoft Azure Storage, klikněte pravým tlačítkem na svůj kontejner a vyberte **získat sdílený přístupový podpis**. Ujistěte se, že jsou zaškrtnutá oprávnění **číst** a **Zobrazit seznam** , a klikněte na **vytvořit**. Pak zkopírujte hodnotu v části **Adresa URL** . Měla by mít tento formát: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

    # <a name="v20"></a>[v2.0](#tab/v2-0)    
    ```bash
    curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
    ```
    # <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1)    
    ```bash
    curl -i -X POST "https://<Endpoint>/formrecognizer/v2.1-preview.1/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
    ```
    
    ---



Dostanete `201 (Success)` odpověď s hlavičkou **umístění** . Hodnota tohoto záhlaví je ID nového vyučeného modelu.

## <a name="get-training-results"></a>Získat výsledky školení

Po spuštění operace vlaku použijete novou operaci, **[získáte vlastní model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** a zkontrolujete stav školení. Předejte ID modelu do tohoto volání rozhraní API, abyste zkontrolovali stav školení:

1. Nahraďte `<Endpoint>` koncovým bodem, který jste získali pomocí klíče předplatného pro rozpoznávání formulářů.
1. Nahraďte `<subscription key>` klíčem předplatného.
1. Nahraďte `<model ID>` ID modelu, které jste obdrželi v předchozím kroku.



# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```
# <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.1-preview.1/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```ce\": \""<SAS URL>"\"}"
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

## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Analýza formulářů pro páry klíč-hodnota a tabulky

V dalším kroku použijete svůj nově vyškolený model k analýze dokumentu a extrakci párů klíč-hodnota a tabulek z něj. Zavolejte rozhraní API pro **[analýzu formuláře](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)** spuštěním následujícího příkazu složeného. Před spuštěním příkazu proveďte tyto změny:

1. Nahraďte `<Endpoint>` koncovým bodem, který jste získali z klíče předplatného pro rozpoznávání formulářů. Můžete ji najít na kartě **Přehled** prostředků nástroje pro rozpoznávání formulářů.
1. Nahraďte `<model ID>` ID modelu, které jste obdrželi v předchozí části.
1. Nahraďte `<SAS URL>` adresou URL SAS k souboru ve službě Azure Storage. Postupujte podle kroků v části školení, ale místo získání adresy URL SAS pro celý kontejner objektů BLOB Získejte jeden pro konkrétní soubor, který chcete analyzovat.
1. Místo `<subscription key>` použijte váš klíč předplatného.

# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -v "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```
# <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1)    
```bash
```bash
curl -v "https://<Endpoint>/formrecognizer/v2.1-preview.1/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```
    
---



Dostanete `202 (Success)` odpověď s hlavičkou **umístění operace** . Hodnota této hlavičky zahrnuje ID výsledků, které používáte ke sledování výsledků operace analyzovat. Uložte toto ID výsledků pro další krok.

## <a name="get-the-analyze-results"></a>Získat výsledky analýzy

Pomocí následujícího rozhraní API můžete zadat dotaz na výsledky operace analyzovat.

1. Nahraďte `<Endpoint>` koncovým bodem, který jste získali z klíče předplatného pro rozpoznávání formulářů. Můžete ji najít na kartě **Přehled** prostředků nástroje pro rozpoznávání formulářů.
1. Nahraďte `<result ID>` ID, které jste obdrželi v předchozí části.
1. Místo `<subscription key>` použijte váš klíč předplatného.


# <a name="v20"></a>[v2.0](#tab/v2-0)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```
# <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1)    
```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.1-preview/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
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


## <a name="improve-results"></a>Zlepšení výsledků

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili nástroj pro rozpoznávání formulářů REST API s kudrlinkou ke školení modelu a jeho spuštění ve vzorovém scénáři. Dále si přečtěte referenční dokumentaci a prozkoumejte rozhraní API pro rozpoznávání formulářů ve větší hloubkě.

> [!div class="nextstepaction"]
> [Referenční dokumentace REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
