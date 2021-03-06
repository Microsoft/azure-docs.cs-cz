---
title: Jak volat rozhraní API pro čtení
titleSuffix: Azure Cognitive Services
description: Naučte se volat rozhraní API pro čtení a podrobně nakonfigurovat jeho chování.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: pafarley
ms.openlocfilehash: 8e0ef789653181d744100ef6e179bcf328f6d704
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308621"
---
# <a name="call-the-read-api"></a>Volání rozhraní API pro čtení

V této příručce se dozvíte, jak volat rozhraní API pro čtení k extrakci textu z obrázků. Seznámíte se s různými způsoby, jak můžete nakonfigurovat chování tohoto rozhraní API, aby vyhovovalo vašim potřebám.

Tato příručka předpokládá, že jste už <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" vytvořili prostředek počítačové zpracování obrazu "  target="_blank"> vytvořit prostředek počítačové zpracování obrazu </a> a získali klíč předplatného a adresu URL koncového bodu. Pokud jste to ještě neudělali, začněte pomocí [rychlého](../quickstarts-sdk/client-library.md) startu.

## <a name="submit-data-to-the-service"></a>Odeslat data do služby

[Volání](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) ČTECÍHO rozhraní API pro čtení přebírá obrázek nebo dokument PDF jako vstup a extrahuje text asynchronně.

`https://{endpoint}/vision/v3.2/read/analyze[?language][&pages][&readingOrder]`

Volání se vrátí s polem hlavičky odpovědi s názvem `Operation-Location` . `Operation-Location`Hodnota je adresa URL, která obsahuje ID operace, která se má použít v dalším kroku.

|Hlavička odpovědi| Příklad hodnoty |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.2/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Fakturace** 
>
> Stránka s [cenami počítačové zpracování obrazu](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) zahrnuje cenovou úroveň pro čtení. Každý analyzovaný obrázek nebo stránka je jedna transakce. Pokud zavoláte operaci s dokumentem PDF nebo TIFF obsahujícím stránky 100, operace čtení se bude počítat jako 100 transakcí a bude se vám účtovat transakce 100. Pokud jste provedli 50 volání operace a každé volání odeslalo dokument s 100 stránky, bude se vám účtovat 50 X 100 = 5000 transakcí.

## <a name="determine-how-to-process-the-data"></a>Určení způsobu zpracování dat

### <a name="language-specification"></a>Specifikace jazyka

Volání metody [Read](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) má volitelný parametr žádosti pro jazyk. Čtení podporuje automatické identifikaci jazyka a vícejazyčné dokumenty, takže zadejte jenom kód jazyka, pokud chcete vynutit zpracování dokumentu jako u tohoto konkrétního jazyka.

### <a name="natural-reading-order-output-latin-languages-only"></a>Výstup přirozeného pořadí čtení (pouze jazyky latinky)
Určete pořadí, ve kterém jsou textové řádky ve výstupu s `readingOrder` parametrem dotazu. Použijte `natural` pro více uživatelsky přívětivého výstupu pořadí čtení, jak je znázorněno v následujícím příkladu. Tato funkce je podporována pouze v jazycích Latin.

:::image border type="content" source="../Images/ocr-reading-order-example.png" alt-text="Příklad pořadí čtení v OCR":::



### <a name="select-pages-or-page-ranges-for-text-extraction"></a>Vybrat stránky nebo rozsahy stránek pro extrakci textu
U rozsáhlých vícestránkových dokumentů použijte `pages` parametr dotazu k zadání čísel stránek nebo rozsahů stránek pro extrakci textu pouze z těchto stránek. Následující příklad ukazuje dokument s 10 stránkami a text extrahovaný pro oba případy – všechny stránky (1-10) a vybrané stránky (3-6).

:::image border type="content" source="../Images/ocr-select-pages.png" alt-text="Výstup vybraných stránek":::

## <a name="get-results-from-the-service"></a>Získat výsledky ze služby

Druhým krokem je volání operace [získat výsledky čtení](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d9869604be85dee480c8750) . Tato operace přijímá jako vstup ID operace, kterou vytvořila operace čtení. 

`https://{endpoint}/vision/v3.2/read/analyzeResults/{operationId}`

Vrátí odpověď JSON, která obsahuje pole **stav** s následujícími možnými hodnotami. 

|Hodnota | Význam |
|:-----|:----|
| `notStarted`| Operace nebyla zahájena. |
| `running`| Operace se zpracovává. |
| `failed`| Operace se nezdařila. |
| `succeeded`| Operace byla úspěšná. |

Tuto operaci zavoláte iterativní, dokud se nevrátí s hodnotou **úspěch** . Pokud nechcete, aby se překročila sazba požadavků za sekundu (RPS), použijte interval 1 až 2 sekund.

> [!NOTE]
> Úroveň Free omezuje rychlost požadavků na 20 volání za minutu. Placená úroveň umožňuje 10 požadavků za sekundu (RPS), které se dají na vyžádání zvýšit. Poznamenejte si identfier a oblast prostředků Azure a otevřete lístek podpory Azure nebo se obraťte na tým vašeho účtu, aby požádal o vyšší míru požadavků za sekundu (RPS).

Pokud má pole **stav** `succeeded` hodnotu, odpověď JSON obsahuje extrahovaný textový obsah z obrázku nebo dokumentu. Odpověď JSON uchovává původní spojnici rozpoznaných slov. Obsahuje extrahované textové řádky a jejich Souřadnice ohraničovacího rámečku. Každý textový řádek obsahuje všechna extrahovaná slova s jejich souřadnicemi a výsledky spolehlivosti.

> [!NOTE]
> Data odeslaná do `Read` operace jsou dočasně zašifrovaná a uložená po krátké době a pak se odstraní. To umožňuje aplikacím získat extrahovaný text jako součást odpovědi služby.

### <a name="sample-json-output"></a>Ukázkový výstup JSON

Podívejte se na následující příklad úspěšné odpovědi JSON:

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-02-04T06:32:08.2752706+00:00",
  "lastUpdatedDateTime": "2021-02-04T06:32:08.7706172+00:00",
  "analyzeResult": {
    "version": "3.2",
    "readResults": [
      {
        "page": 1,
        "angle": 2.1243,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              58,
              42,
              314,
              59,
              311,
              123,
              56,
              121
            ],
            "text": "Tabs vs",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.96
              }
            },
            "words": [
              {
                "boundingBox": [
                  68,
                  44,
                  225,
                  59,
                  224,
                  122,
                  66,
                  123
                ],
                "text": "Tabs",
                "confidence": 0.933
              },
              {
                "boundingBox": [
                  241,
                  61,
                  314,
                  72,
                  314,
                  123,
                  239,
                  122
                ],
                "text": "vs",
                "confidence": 0.977
              }
            ]
          }
        ]
      }
    ]
  }
}
```

### <a name="handwritten-classification-for-text-lines-latin-languages-only"></a>Ručně psaná klasifikace pro textové řádky (pouze jazyky latinky)
Odpověď zahrnuje klasifikaci, zda je každý textový řádek ve stylu rukopisu nebo ne, spolu s skóre spolehlivosti. Tato funkce je podporována pouze v jazycích Latin. Následující příklad ukazuje ručně vytvořenou klasifikaci textu v obrázku.

:::image border type="content" source="../Images/ocr-handwriting-classification.png" alt-text="Příklad klasifikace rukopisu OCR":::

## <a name="next-steps"></a>Další kroky

Pokud si chcete vyzkoušet REST API, [Přečtěte si referenční informace o rozhraní API pro čtení](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005).
