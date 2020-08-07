---
title: 'Rychlý Start: výuka modelu a extrakce dat formuláře pomocí funkce pro rozpoznávání složeného formuláře'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete REST API pro rozpoznávání formulářů s kudrlinkou k učení modelu a extrakce dat z formulářů.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: 94873a3ea1349a9dfac199d98fd109b1a97f72f9
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87904730"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Rychlý Start: výuka modelu pro rozpoznávání formulářů a extrakce dat formuláře pomocí REST API s kudrlinkou

V tomto rychlém startu použijete nástroj pro rozpoznávání formulářů Azure REST API s kudrlinkou ke výukám a k extrakci formulářů a jejich skóre a k extrakci párů klíč-hodnota a tabulek.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

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
1. Nahraďte `<SAS URL>` adresou URL sdíleného přístupového podpisu (SAS) kontejneru úložiště objektů BLOB v Azure. Pokud chcete načíst adresu URL SAS, otevřete Průzkumník služby Microsoft Azure Storage, klikněte pravým tlačítkem na svůj kontejner a vyberte **získat sdílený přístupový podpis**. Ujistěte se, že jsou zaškrtnutá oprávnění **číst** a **Zobrazit seznam** , a klikněte na **vytvořit**. Pak zkopírujte hodnotu v části **Adresa URL** . Měla by mít tvar: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` .

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

Dostanete `201 (Success)` odpověď s hlavičkou **umístění** . Hodnota tohoto záhlaví je ID nového vyučeného modelu. 

## <a name="get-training-results"></a>Získat výsledky školení

Po spuštění operace vlaku použijete novou operaci, **[získáte vlastní model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetCustomModel)** a zkontrolujete stav školení. Předejte ID modelu do tohoto volání rozhraní API, abyste zkontrolovali stav školení:

1. Nahraďte `<Endpoint>` koncovým bodem, který jste získali pomocí klíče předplatného pro rozpoznávání formulářů.
1. Nahraďte `<subscription key>` klíčem předplatného.
1. Nahraďte `<model ID>` ID modelu, které jste obdrželi v předchozím kroku.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

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

```bash
curl -v "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```

Dostanete `202 (Success)` odpověď s hlavičkou **umístění operace** . Hodnota této hlavičky zahrnuje ID výsledků, které používáte ke sledování výsledků operace analyzovat. Uložte toto ID výsledků pro další krok.

## <a name="get-the-analyze-results"></a>Získat výsledky analýzy

Pomocí následujícího rozhraní API můžete zadat dotaz na výsledky operace analyzovat.

1. Nahraďte `<Endpoint>` koncovým bodem, který jste získali z klíče předplatného pro rozpoznávání formulářů. Můžete ji najít na kartě **Přehled** prostředků nástroje pro rozpoznávání formulářů.
1. Nahraďte `<result ID>` ID, které jste obdrželi v předchozí části.
1. Místo `<subscription key>` použijte váš klíč předplatného.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

`200 (Success)`V následujícím formátu obdržíte odpověď s textem JSON. Výstup byl zkrácen pro zjednodušení. Všimněte si `"status"` pole poblíž dolního okraje. Tato hodnota bude mít hodnotu `"succeeded"` po dokončení operace analyzovat. Pokud se operace analýzy nedokončila, budete se muset znovu dotázat na službu opětovným spuštěním příkazu. Pro mezi voláními doporučujeme interval o délce jedné sekundy nebo více.

Hlavní přidružení páru klíč/hodnota a tabulky jsou v `"pageResults"` uzlu. Pokud jste zadali také extrakci prostého textu prostřednictvím parametru adresy URL *includeTextDetails* , `"readResults"` uzel zobrazí obsah a pozice veškerého textu v dokumentu.

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

## <a name="improve-results"></a>Zlepšení výsledků

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili nástroj pro rozpoznávání formulářů REST API s kudrlinkou ke školení modelu a jeho spuštění ve vzorovém scénáři. Dále si přečtěte referenční dokumentaci a prozkoumejte rozhraní API pro rozpoznávání formulářů ve větší hloubkě.

> [!div class="nextstepaction"]
> [Referenční dokumentace REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm)
