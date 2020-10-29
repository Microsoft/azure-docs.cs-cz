---
title: Příjmy – Nástroj pro rozpoznávání formulářů
titleSuffix: Azure Cognitive Services
description: Přečtěte si o konceptech souvisejících s analýzou příjmů pomocí rozhraní API pro rozpoznávání formulářů – využití a omezení.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: 5125fff0ef8987d313c6611e4d5de08d090f2263
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913190"
---
# <a name="receipt-concepts"></a>Principy účtenek

Nástroj pro rozpoznávání formulářů Azure dokáže analyzovat účtenky pomocí jednoho z předdefinovaných modelů. Rozhraní API pro příjem dat extrahuje klíčové informace z prodejních příjmů v angličtině, jako je například název obchodníka, datum transakce, součet transakcí, položky řádků a další. 

## <a name="understanding-receipts"></a>Porozumění příjemkám 

Řada firem a jednotlivců se stále spoléhá na ruční extrakci dat z jejich prodejních příjmů, ať už pro obchodní výdaje, úhrady, audity, daňové účely, rozpočtování, uvádění na trh nebo jiné účely. V těchto scénářích se často pro účely ověření vyžadují image fyzického příjmu.  

Automatické extrakce dat z těchto příjmů může být složité. Příjmy můžou být Crumpled a těžko čitelnými, tištěnými nebo ručně psanými částmi a smartphone obrázky o příjmech mohou být nízké kvality. Také je možné, že se šablony a pole účtenky můžou významně lišit podle trhu, oblasti a obchodníka. Tyto problémy při extrakci dat i při detekci polí umožňují zpracování jedinečného problému.  

V případě použití optického rozpoznávání znaků (OCR) a našeho předem připraveného modelu příjemek rozhraní API pro příjem umožňuje tyto scénáře zpracování příjmu a extrahovat data z příjmů, např. název obchodníka, tip, součet, položky řádků a další. S tímto rozhraním API není nutné naškolovat model, který jste právě odeslali, do rozhraní API pro analýzu příjemů a data se extrahují.

![Ukázka účtenky](./media/contoso-receipt-small.png)

## <a name="what-does-the-receipt-api-do"></a>Co dělá rozhraní API pro příjem? 

Předem vytvořené rozhraní API pro příjem extrakce vyextrahuje obsah prodejních příjmů &mdash; na základě typu příjemky, který byste běžně získali v restauracích, maloobchodníkech nebo prodejnách.

### <a name="fields-extracted"></a>Extrahovaná pole

* Název obchodníka 
* Adresa obchodníka 
* Telefonní číslo pro obchodní telefon 
* Datum transakce 
* Čas transakce 
* Mezisoučet 
* Daň 
* Celkem 
* Tip 
* Extrakce položek řádku (například množství položky, cena za položku, název položky)

### <a name="additional-features"></a>Další funkce

Rozhraní API pro příjem také vrátí následující informace:

* Typ účtenky (například podle položky, platební karta atd.)
* Úroveň spolehlivosti pole (každé pole vrátí přidruženou hodnotu spolehlivosti)
* Nezpracovaný text OCR (OCR – textový výstup extrahovaný pro celou příjem)
* Ohraničovací rámeček pro každou hodnotu, řádek a slovo

## <a name="input-requirements"></a>Požadavky na vstup

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>Podporovaná národní prostředí 

* **Předem sestavená příjem v 2.0** (GA) podporuje prodejní příjeme v národním prostředí en-US.
* **Předem sestavená příjem v 2.1-Preview. 1** (Public Preview) přidává další podporu následujících národních prostředí pro příjem hodnot EN: 
  * EN-AU 
  * EN-CA 
  * EN-GB 
  * EN-IN 

  > [!NOTE]
  > Vstup jazyka 
  >
  > Předem sestavená příjem verze 2.1-Preview. 1 má volitelný parametr Request pro určení národního prostředí pro příjem z dalších anglických trhů. Pro prodejní příjeme v angličtině z Austrálie (EN-AU), Kanada (EN-CA), Velká Británie (EN-GB) a Indie (EN-IN), můžete určit národní prostředí a získat tak lepší výsledky. Pokud není zadané žádné národní prostředí v rámci verze 2.1-Preview. 1, model se nastaví jako výchozí pro model EN-US.


## <a name="the-analyze-receipt-operation"></a>Operace analyzovat příjem

Při [analýze se obdrží](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeReceiptAsync) jako vstup obrázek nebo PDF účtenky a získá hodnoty zájmu a text. Volání vrátí pole hlavičky odpovědi s názvem `Operation-Location` . `Operation-Location`Hodnota je adresa URL, která obsahuje ID výsledku, které se má použít v dalším kroku.

|Hlavička odpovědi| Adresa URL výsledku |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/56a36454-fc4d-4354-aa07-880cfbf0064f` |

## <a name="the-get-analyze-receipt-result-operation"></a>Operace získání výsledku analýzy pro příjem

Druhým krokem je volání operace [získat analýzu výsledků příjmu](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/GetAnalyzeReceiptResult) . Tato operace přijímá jako vstup ID výsledku, které bylo vytvořeno operací analyzovat příjem. Vrátí odpověď JSON, která obsahuje pole **stav** s následujícími možnými hodnotami. Tuto operaci zavoláte iterativní, dokud se nevrátí s hodnotou **úspěch** . Použijte interval 3 až 5 sekund, abyste předešli překročení sazby za sekundu (RPS).

|Pole| Typ | Možné hodnoty |
|:-----|:----:|:----|
|status | řetězec | notStarted: operace analýzy nebyla spuštěna. |
| |  | běží: probíhá operace analýzy. |
| |  | Nepovedlo se: operace analýzy se nezdařila. |
| |  | úspěch: operace analýzy byla úspěšná. |

Pokud má pole **stav** hodnotu **úspěch** , bude odpověď JSON zahrnovat porozumění příjmu a výsledky rozpoznávání textu. Výsledek porozumění příjmu je uspořádaný jako slovník hodnot pojmenovaných polí, kde každá hodnota obsahuje extrahovaný text, normalizovanou hodnotu, ohraničovací rámeček, spolehlivost a odpovídající prvky slova. Výsledek rozpoznávání textu je uspořádán jako hierarchie řádků a slov s textem, ohraničujícím polem a informacemi o spolehlivosti.

![Ukázka výsledků pro příjem](./media/contoso-receipt-2-information.png)

### <a name="sample-json-output"></a>Ukázkový výstup JSON

Podívejte se na následující příklad úspěšné odpovědi JSON: uzel "readResults" obsahuje veškerý rozpoznaný text. Text je uspořádán podle stránky, potom podle řádku, podle jednotlivých slov. Uzel "documentResults" obsahuje hodnoty specifické pro obchodní karty, které model zjistil. Tady najdete užitečné páry klíč/hodnota jako křestní jméno, příjmení, název společnosti a další.

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


## <a name="customer-scenarios"></a>Scénáře zákazníků  

Data extrahovaná pomocí rozhraní API pro příjem lze použít k provádění různých úloh. Následuje několik příkladů toho, co naši zákazníci dokončí s rozhraním API pro příjem. 

### <a name="business-expense-reporting"></a>Vykazování obchodních výdajů  

Pracovní výdaje se často dodávají do doby trvání útraty ručním zadáváním dat z imagí příjmů. Pomocí rozhraní API pro příjem můžete použít extrahovaná pole pro částečně automatizaci tohoto procesu a rychle analyzovat vaše účtenky.  

Vzhledem k tomu, že rozhraní API pro příjem má jednoduchý výstup JSON, můžete hodnoty extrahovaných polí použít několika způsoby. Integrujte s interními výdajovými aplikacemi a předem naplnit sestavy výdajů. Další informace o tomto scénáři najdete v článku o tom, jak Acumatica využívá rozhraní API pro příjem, aby [vykazují náklady s méně bolestivý procesy](https://customers.microsoft.com/story/762684-acumatica-partner-professional-services-azure).  

### <a name="auditing-and-accounting"></a>Auditování a monitorování účtů 

Výstup rozhraní API pro příjem se dá také použít k analýze velkého počtu výdajů v různých fázích vykazování výdajů a procesu refundace. Můžete zpracovávat příjmy pro ruční audit nebo rychlé schvalování.  

Výstup příjemky je také užitečný pro obecné obchodní nebo osobní použití. Pomocí rozhraní API pro příjem použijte k transformaci všech nezpracovaných dat příjmu dat nebo souborů PDF do digitálního výstupu, který je napadnutelný.

### <a name="consumer-behavior"></a>Chování uživatele 

Příjmy obsahují užitečná data, která můžete použít k analýze chování zákazníků a nákupních trendů.

Rozhraní API pro příjemce taky zajišťuje [funkci pro zpracování příjmu AIBuilder](/ai-builder/prebuilt-receipt-processing).

## <a name="next-steps"></a>Další kroky

- Dokončete [rychlý Start klientské knihovny pro rozpoznávání formulářů](quickstarts/client-library.md) a začněte psát aplikaci pro zpracování příjmu pomocí nástroje pro rozpoznávání formulářů v jazyce podle vašeho výběru.
- Můžete také použít kurz pro [příjem API v Pythonu](./quickstarts/python-receipts.md) k rozpoznávání účtenek pomocí REST API.

## <a name="see-also"></a>Viz také

* [Co je služba Rozpoznávání formulářů?](./overview.md)
* [Referenční dokumentace REST API](./index.yml)