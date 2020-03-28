---
title: 'Úvodní příručka: Trénování modelu a extrahování dat formuláře pomocí cURL - Rozpoznávání formulářů'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete rozhraní REST API nástroje pro rozpoznávání formulářů s cURL k trénování modelu a extrahování dat z formulářů.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 32756187852de0834afc1dc034d3f7419f0c8087
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77118390"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Úvodní příručka: Trénování modelu rozpoznávání formulářů a extrahování dat formuláře pomocí rozhraní REST API s cURL

V tomto rychlém startu použijete rozhraní REST API nástroje Azure Pro rozpoznávání formulářů s cURL k trénování a získávání skóre formulářů pro extrahování párů klíč-hodnota a tabulek.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit tento rychlý start, musíte mít:
- [cURL](https://curl.haxx.se/windows/) nainstalován.
- Sada nejméně šesti forem stejného typu. Budete používat pět z nich trénovat model, a pak budete testovat s šestým formulářem. Formuláře mohou mít různé typy souborů, ale musí se jednat o stejný typ dokumentu. Pro tento rychlý start můžete použít [ukázkovou sadu dat.](https://go.microsoft.com/fwlink/?linkid=2090451) Nahrajte školicí soubory do kořenového adresáře kontejneru úložiště objektů blob v účtu Azure Storage. Testovací soubory můžete umístit do samostatné složky.

## <a name="create-a-form-recognizer-resource"></a>Vytvoření prostředku nástroje pro rozpoznávání formulářů

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Trénování modelu nástroje pro rozpoznávání formulářů

Nejprve budete potřebovat sadu trénovacích dat v objektu blob úložiště Azure. Měli byste mít minimálně pět vyplněných formulářů (pdf dokumentů a/nebo obrázků) stejného typu/struktury jako hlavní vstupní data. Nebo můžete použít jeden prázdný formulář se dvěma vyplněnými formuláři. Název souboru prázdného formuláře musí obsahovat slovo "prázdný". V [tématu Vytvoření trénovací datové sady pro vlastní model,](../build-training-data-set.md) kde najdete tipy a možnosti pro sestavení trénovacích dat.

> [!NOTE]
> Pomocí funkce označená data můžete předem ručně označit některá nebo všechna trénovací data. Jedná se o složitější proces, ale výsledkem je lépe trénovaný model. Další informace o této funkci najdete v části [Vlak s popisky](../overview.md#train-with-labels) v přehledu.

Chcete-li trénovat model rozpoznávání formulářů s dokumenty v kontejneru objektů blob Azure, zavolejte **[rozhraní API vlastního modelu trénování](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)** spuštěním následujícího příkazu cURL. Před spuštěním příkazu proveďte tyto změny:

1. Nahraďte `<Endpoint>` koncovýbod, který jste získali s odběrem nástroje pro rozpoznávání formulářů.
1. Nahraďte `<subscription key>` klíčem předplatného, který jste zkopírovali z předchozího kroku.
1. Nahraďte `<SAS URL>` adresu URL sdíleného přístupového podpisu (SAS) kontejneru azure blob. Chcete-li načíst adresu URL SAS, otevřete Průzkumníka úložiště Microsoft Azure, klikněte pravým tlačítkem myši na kontejner a vyberte **získat sdílený přístupový podpis**. Zkontrolujte, zda jsou zaškrtnuta oprávnění **Číst** a **seznam,** a klepněte na **tlačítko Vytvořit**. Pak zkopírujte hodnotu v části **ADRESA URL.** Měl by mít `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`formu: .

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

Obdržíte `201 (Success)` odpověď s hlavičkou **Umístění.** Hodnota tohoto záhlaví je ID nového modelu, který je trénován. 

## <a name="get-training-results"></a>Získejte výsledky školení

Po spuštění operace vlaku, použijte novou operaci, **[Získat vlastní model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetCustomModel)** pro kontrolu stavu školení. Předejte ID modelu do tohoto volání rozhraní API a zkontrolujte stav školení:

1. Nahraďte `<Endpoint>` koncovýbod, který jste získali pomocí klíče předplatného nástroje pro rozpoznávání formulářů.
1. Nahrazení `<subscription key>` klíčem předplatného
1. Nahrazení `<model ID>` ID modelu, které jste obdrželi v předchozím kroku

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

Obdržíte `200 (Success)` odpověď s tělem JSON v následujícím formátu. Všimněte `"status"` si pole. To bude mít `"ready"` hodnotu po dokončení školení. Pokud model není dokončenškolení, budete muset dotaz služby znovu opětovným spuštěním příkazu. Doporučujeme interval jedné sekundy nebo více mezi hovory.

Pole `"modelId"` obsahuje ID modelu, který trénujete. Budete to potřebovat pro další krok.

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

## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Analýza formulářů pro dvojice a tabulky klíč-hodnota

Dále budete používat nově trénovaný model k analýze dokumentu a extrahování párů klíč-hodnota a tabulek z něj. Volání **[rozhraní Analyzovat rozhraní API formuláře](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)** spuštěním následujícího příkazu cURL. Před spuštěním příkazu proveďte tyto změny:

1. Nahraďte `<Endpoint>` koncovýbod, který jste získali z klíče předplatného nástroje pro rozpoznávání formulářů. Najdete ji na kartě **Přehled** prostředků nástroje pro rozpoznávání formulářů.
1. Nahraďte `<model ID>` id modelu, které jste obdrželi v předchozí části.
1. Nahraďte `<SAS URL>` adresu URL SAS k souboru v úložišti Azure. Postupujte podle pokynů v části Školení, ale místo získání adresy URL SAS pro celý kontejner objektů blob, získejte jeden pro konkrétní soubor, který chcete analyzovat.
1. Místo `<subscription key>` použijte váš klíč předplatného.

```bash
curl -v "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```

Obdržíte `202 (Success)` odpověď s hlavičkou **Operation-Location.** Hodnota této hlavičky obsahuje ID výsledků, které slouží ke sledování výsledků operace Analyzovat. Uložte toto ID výsledků pro další krok.

## <a name="get-the-analyze-results"></a>Získejte výsledky analýzy

Pomocí následujícího rozhraní API můžete dotazovat na výsledky operace Analyzovat.

1. Nahraďte `<Endpoint>` koncovýbod, který jste získali z klíče předplatného nástroje pro rozpoznávání formulářů. Najdete ji na kartě **Přehled** prostředků nástroje pro rozpoznávání formulářů.
1. Nahraďte `<result ID>` id, které jste obdrželi v předchozí části.
1. Místo `<subscription key>` použijte váš klíč předplatného.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0-preview/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

Obdržíte `200 (Success)` odpověď s tělem JSON v následujícím formátu. Výstup byl zkrácen pro jednoduchost. Všimněte `"status"` si pole v dolní části. To bude mít `"succeeded"` hodnotu po dokončení operace Analyzovat. Pokud operace Analyzovat nebyla dokončena, budete muset znovu zadat dotaz na službu opětovným spuštěním příkazu. Doporučujeme interval jedné sekundy nebo více mezi hovory.

Přidružení a tabulky párování klíčů a `"pageResults"` hodnot jsou v uzlu. Pokud jste také zadali extrakci prostého textu `"readResults"` prostřednictvím parametru *includeTextDetails* URL, pak uzel zobrazí obsah a umístění veškerého textu v dokumentu.

```json
{
  "analyzeResult":{ 
    "readResults":[ 
      { 
        "page":1,
        "width":8.5,
        "height":11.0,
        "angle":0,
        "unit":"inch",
        "lines":[ 
          { 
            "text":"Contoso",
            "boundingBox":[ 
              0.5278,
              1.0597,
              1.4569,
              1.0597,
              1.4569,
              1.4347,
              0.5278,
              1.4347
            ],
            "words":[ 
              { 
                "text":"Contoso",
                "boundingBox":[ 
                  0.5278,
                  1.0597,
                  1.4569,
                  1.0597,
                  1.4569,
                  1.4347,
                  0.5278,
                  1.4347
                ]
              }
            ]
          },
          ...
          { 
            "text":"PT",
            "boundingBox":[ 
              6.2181,
              3.3528,
              6.3944,
              3.3528,
              6.3944,
              3.5417,
              6.2181,
              3.5417
            ],
            "words":[ 
              { 
                "text":"PT",
                "boundingBox":[ 
                  6.2181,
                  3.3528,
                  6.3944,
                  3.3528,
                  6.3944,
                  3.5417,
                  6.2181,
                  3.5417
                ]
              }
            ]
          }
        ]
      }
    ],
    "version":"2.0.0",
    "errors":[ 

    ],
    "documentResults":[ 

    ],
    "pageResults":[ 
      { 
        "page":1,
        "clusterId":1,
        "keyValuePairs":[ 
          { 
            "key":{ 
              "text":"Address:",
              "boundingBox":[ 
                0.7972,
                1.5125,
                1.3958,
                1.5125,
                1.3958,
                1.6431,
                0.7972,
                1.6431
              ],
              "elements":[ 
                "#/readResults/0/lines/1/words/0"
              ]
            },
            "value":{ 
              "text":"1 Redmond way Suite 6000 Redmond, WA 99243",
              "boundingBox":[ 
                0.7972,
                1.6764,
                2.15,
                1.6764,
                2.15,
                2.2181,
                0.7972,
                2.2181
              ],
              "elements":[ 
                "#/readResults/0/lines/4/words/0",
                "#/readResults/0/lines/4/words/1",
                "#/readResults/0/lines/4/words/2",
                "#/readResults/0/lines/4/words/3",
                "#/readResults/0/lines/6/words/0",
                "#/readResults/0/lines/6/words/1",
                "#/readResults/0/lines/6/words/2",
                "#/readResults/0/lines/8/words/0"
              ]
            },
            "confidence":0.86
          },
          { 
            "key":{ 
              "text":"Invoice For:",
              "boundingBox":[ 
                4.3903,
                1.5125,
                5.1139,
                1.5125,
                5.1139,
                1.6431,
                4.3903,
                1.6431
              ],
              "elements":[ 
                "#/readResults/0/lines/2/words/0",
                "#/readResults/0/lines/2/words/1"
              ]
            },
            "value":{ 
              "text":"Microsoft 1020 Enterprise Way Sunnayvale, CA 87659",
              "boundingBox":[ 
                5.1917,
                1.4458,
                6.6583,
                1.4458,
                6.6583,
                2.0347,
                5.1917,
                2.0347
              ],
              "elements":[ 
                "#/readResults/0/lines/3/words/0",
                "#/readResults/0/lines/5/words/0",
                "#/readResults/0/lines/5/words/1",
                "#/readResults/0/lines/5/words/2",
                "#/readResults/0/lines/7/words/0",
                "#/readResults/0/lines/7/words/1",
                "#/readResults/0/lines/7/words/2"
              ]
            },
            "confidence":0.86
          },
          ...
        ],
        "tables":[ 
          { 
            "caption":null,
            "rows":2,
            "columns":5,
            "cells":[ 
              { 
                "rowIndex":0,
                "colIndex":0,
                "header":true,
                "text":"Invoice Number",
                "boundingBox":[ 
                  0.5347,
                  2.8722,
                  1.575,
                  2.8722,
                  1.575,
                  3.0028,
                  0.5347,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/9/words/0",
                  "#/readResults/0/lines/9/words/1"
                ]
              },
              { 
                "rowIndex":0,
                "colIndex":1,
                "header":true,
                "text":"Invoice Date",
                "boundingBox":[ 
                  1.9403,
                  2.8722,
                  2.7569,
                  2.8722,
                  2.7569,
                  3.0028,
                  1.9403,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/10/words/0",
                  "#/readResults/0/lines/10/words/1"
                ]
              },
              { 
                "rowIndex":0,
                "colIndex":2,
                "header":true,
                "text":"Invoice Due Date",
                "boundingBox":[ 
                  3.3403,
                  2.8722,
                  4.4583,
                  2.8722,
                  4.4583,
                  3.0028,
                  3.3403,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/11/words/0",
                  "#/readResults/0/lines/11/words/1",
                  "#/readResults/0/lines/11/words/2"
                ]
              },
              ...
            ]
          }
        ]
      }
    ]
  },
  "lastUpdatedDateTime":"2019-10-07T19:32:18+00:00",
  "status":"succeeded",
  "createdDateTime":"2019-10-07T19:32:15+00:00"
}
```

## <a name="improve-results"></a>Zlepšete výsledky

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili rozhraní REST API nástroje pro rozpoznávání formulářů s cURL k trénování modelu a jeho spuštění v ukázkovém scénáři. Dále naleznete v referenční dokumentaci k podrobnějšímu prozkoumání rozhraní API pro rozpoznávání formulářů.

> [!div class="nextstepaction"]
> [Referenční dokumentace rozhraní REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)
