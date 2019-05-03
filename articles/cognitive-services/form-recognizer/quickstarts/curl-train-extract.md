---
title: 'Rychlý start: Trénování modelu a extrahovat data formuláře pomocí příkazu cURL – nástroj pro rozpoznávání formuláře'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu použijete rozhraní REST API pro rozpoznávání formuláře pomocí cURL k natrénování modelu a extrahovat data z formuláře.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: quickstart
ms.date: 04/15/2019
ms.author: pafarley
ms.openlocfilehash: cc6e8cdb7cd1719a8cd14cbfe6e576e07c34b32c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027261"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-using-rest-api-with-curl"></a>Rychlý start: Trénování modelu Rozlišovač formuláře a extrahování dat formuláře pomocí rozhraní REST API pomocí cURL

V tomto rychlém startu použijete rozhraní REST API pro rozpoznávání formuláře pomocí cURL k trénování a stanovíte jeho skóre formulářů k extrakci páry klíč hodnota a tabulky.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Jste získali přístup k verzi preview pro formulář rozpoznávání omezený přístup. Pokud chcete získat přístup k verzi preview, prosím vyplňte a odešlete [Cognitive Services pro rozpoznávání formulář žádosti o přístup](https://aka.ms/FormRecognizerRequestAccess) formuláře. 
* Musíte mít [cURL](https://curl.haxx.se/windows/).
* Klíč předplatného musí mít pro formulář Rozlišovače. Postupujte podle pokynů v [vytvoření účtu služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) předplatit Rozlišovač formuláře a získejte klíč.
* Musí mít minimální sadu pěti formy stejného typu. Můžete použít [ukázkovou datovou sadou](https://go.microsoft.com/fwlink/?linkid=2090451) pro tento rychlý start.

## <a name="train-a-form-recognizer-model"></a>Trénování modelu Rozlišovač formuláře

Nejprve je třeba sadu trénovací data. Můžete použít data v Azure Blob nebo vlastní místní trénovací data. Měli byste mít minimálně pět vzorku formy (dokumenty PDF a/nebo imagí) stejný typ nebo strukturu jako hlavní vstupní data. Alternativně můžete použít jeden prázdný formulář; Název formuláře souboru obsahuje slovo "prázdný".

K natrénování modelu formuláře Rozlišovač použití dokumentů v kontejnerech objektů Blob v Azure, zavolejte **trénování** rozhraní API pomocí provádí následující příkaz cURL. Před spuštěním příkazu, proveďte následující změny:

* Nahraďte `<Endpoint>` s koncovým bodem, který jste získali váš klíč předplatného Rozlišovač formuláře. Najdete ho na kartě Přehled prostředků vaší Rozlišovač formuláře.
* Nahraďte `<SAS URL>` kontejneru Azure Blob Storage sdílet přístup k podpisu (SAS) adresy URL, kde se nachází trénovací data.  
* Místo `<subscription key>` použijte váš klíč předplatného.

```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/train" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \"<SAS URL>\"}"
```

Zobrazí se `200 (Success)` odpověď se následující výstup JSON:

```json
{
  "modelId": "59e2185e-ab80-4640-aebc-f3653442617b",
  "trainingDocuments": [
    {
      "documentName": "Invoice_1.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_2.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_3.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_4.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_5.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    }
  ],
  "errors": []
}
```

Poznamenejte si `"modelId"` hodnota; je budete potřebovat následující postup.
  
## <a name="extract-key-value-pairs-and-tables-from-forms"></a>Extrahování páry klíč hodnota a tabulek z formulářů

V dalším kroku se analýza dokumentu a z něj extrahovat páry klíč hodnota a tabulky. Volání **Model – analýza** rozhraní API pomocí provádí následující příkaz cURL. Před spuštěním příkazu, proveďte následující změny:

* Nahraďte `<Endpoint>` s koncovým bodem jste získali váš klíč předplatného Rozlišovač formuláře. Najdete ho na kartě Přehled prostředků vaší Rozlišovač formuláře.
* Nahraďte `<modelID>` s ID modelu, který jste získali v předchozím kroku tohoto cvičení modelu.
* Nahraďte `<path to your form>` s cestou k souboru do formuláře.
* Místo `<subscription key>` použijte váš klíč předplatného.
* Nahraďte `<file type>` s typem souboru – podporované typy pdf, image/jpeg, image/png.

```bash
cURL cmd: curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/models/<modelID>/analyze" -H "Content-Type: multipart/form-data" -F "form=@<path to your form>;type=application/<file type>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>Prozkoumání odpovědi

Úspěšná odpověď se vrátí ve formátu JSON a představuje extrahované páry klíč hodnota a tabulky z formuláře.

```bash
{
  "status": "success",
  "pages": [
    {
      "number": 1,
      "height": 792,
      "width": 612,
      "clusterId": 0,
      "keyValuePairs": [
        {
          "key": [
            {
              "text": "Address:",
              "boundingBox": [
                57.4,
                683.1,
                100.5,
                683.1,
                100.5,
                673.7,
                57.4,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "1 Redmond way Suite",
              "boundingBox": [
                57.4,
                671.3,
                154.8,
                671.3,
                154.8,
                659.2,
                57.4,
                659.2
              ],
              "confidence": 0.86
            },
            {
              "text": "6000 Redmond, WA",
              "boundingBox": [
                57.4,
                657.1,
                146.9,
                657.1,
                146.9,
                645.5,
                57.4,
                645.5
              ],
              "confidence": 0.86
            },
            {
              "text": "99243",
              "boundingBox": [
                57.4,
                643.4,
                85,
                643.4,
                85,
                632.3,
                57.4,
                632.3
              ],
              "confidence": 0.86
            }
          ]
        },
        {
          "key": [
            {
              "text": "Invoice For:",
              "boundingBox": [
                316.1,
                683.1,
                368.2,
                683.1,
                368.2,
                673.7,
                316.1,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "Microsoft",
              "boundingBox": [
                374,
                687.9,
                418.8,
                687.9,
                418.8,
                673.7,
                374,
                673.7
              ],
              "confidence": 1
            },
            {
              "text": "1020 Enterprise Way",
              "boundingBox": [
                373.9,
                673.5,
                471.3,
                673.5,
                471.3,
                659.2,
                373.9,
                659.2
              ],
              "confidence": 1
            },
            {
              "text": "Sunnayvale, CA 87659",
              "boundingBox": [
                373.8,
                659,
                479.4,
                659,
                479.4,
                645.5,
                373.8,
                645.5
              ],
              "confidence": 1
            }
          ]
        }
      ],
      "tables": [
        {
          "id": "table_0",
          "columns": [
            {
              "header": [
                {
                  "text": "Invoice Number",
                  "boundingBox": [
                    38.5,
                    585.2,
                    113.4,
                    585.2,
                    113.4,
                    575.8,
                    38.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "34278587",
                    "boundingBox": [
                      38.5,
                      547.3,
                      82.8,
                      547.3,
                      82.8,
                      537,
                      38.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Date",
                  "boundingBox": [
                    139.7,
                    585.2,
                    198.5,
                    585.2,
                    198.5,
                    575.8,
                    139.7,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/18/2017",
                    "boundingBox": [
                      139.7,
                      546.8,
                      184,
                      546.8,
                      184,
                      537,
                      139.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Due Date",
                  "boundingBox": [
                    240.5,
                    585.2,
                    321,
                    585.2,
                    321,
                    575.8,
                    240.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/24/2017",
                    "boundingBox": [
                      240.5,
                      546.8,
                      284.8,
                      546.8,
                      284.8,
                      537,
                      240.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Charges",
                  "boundingBox": [
                    341.3,
                    585.2,
                    381.2,
                    585.2,
                    381.2,
                    575.8,
                    341.3,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "$56,651.49",
                    "boundingBox": [
                      387.6,
                      546.4,
                      437.5,
                      546.4,
                      437.5,
                      537,
                      387.6,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "VAT ID",
                  "boundingBox": [
                    442.1,
                    590,
                    474.8,
                    590,
                    474.8,
                    575.8,
                    442.1,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "PT",
                    "boundingBox": [
                      447.7,
                      550.6,
                      460.4,
                      550.6,
                      460.4,
                      537,
                      447.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            }
          ]
        }
      ]
    }
  ],
  "errors": []
}
```

## <a name="next-steps"></a>Další postup

V tomto průvodci použijete rozhraní REST API pro rozpoznávání formuláře pomocí cURL k natrénování modelu a spustíte ji v případě ukázka. V dalším kroku naleznete v referenční dokumentaci rozhraní API pro rozpoznávání formuláře podrobněji prozkoumat.

> [!div class="nextstepaction"]
> [Dokumentace k rozhraní REST API](https://aka.ms/form-recognizer/api)
