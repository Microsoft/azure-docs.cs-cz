---
title: 'Kurz: nastavení publikování – LUIS'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu můžete změnit nastavení publikování a získat tak vylepšení předpovědi.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: diberry
ms.openlocfilehash: 7683a73fd1f403847089dc722e5f6382acd948cb
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447695"
---
# <a name="tutorial--add-sentiment-analysis-as-a-publishing-setting"></a>Kurz: Přidání analýzy mínění jako nastavení publikování

V tomto kurzu upravíte nastavení publikování pro extrakci analýzy mínění a pak dotaz na koncový bod LUIS, abyste viděli vrácenou mínění uživatele utterance.

**V tomto kurzu se naučíte:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Přidat analýzu mínění jako nastavení publikování
> * Získat mínění z utterance z publikovaného koncového bodu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>Analýza mínění je nastavení publikování

Následující promluvy ukazují příklady mínění:

|Zabarvení|Hodnocení|Promluva|
|:--|:--|:--|
|negativní|0.01 |Pizza byl awful.|
|pozitivní|0,97 |Pizza sýr je vynikající.|

Analýza mínění je nastavení publikování, které se vztahuje na všechny promluvy. Po nastavení aplikace vrátí mínění pro utterance, aniž byste museli označovat data.

Vzhledem k tomu, že se jedná o nastavení publikování, nezobrazují se na stránkách záměry nebo entity. Zobrazuje se v podokně [interaktivního testu](luis-interactive-test.md#view-sentiment-results) nebo při testování na adrese URL koncového bodu.

## <a name="import-example-json-to-begin-app"></a>Import example. JSON pro zahájení aplikace

1.  Stáhněte a uložte [soubor JSON aplikace](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app"></a>Trénování aplikace

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Konfigurace aplikace pro zahrnutí analýzy mínění

1. V horní nabídce vyberte **publikovat** . Analýza mínění je nastavení publikování.

1. Vyberte **produkční slot** a pak vyberte **změnit nastavení**.
1. Nastavte Analýza mínění nastavení na **zapnuto**.

    ![Zapnout analýzu subjektivního hodnocení jako nastavení publikování](./media/luis-quickstart-intent-and-sentiment-analysis/select-sentiment-publishing-setting.png)

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Získat mínění utterance z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Přejděte na konec adresy URL v adrese a zadejte následující utterance:

    `Deliver 2 of the best cheese pizzas ever!!!`

    Poslední parametr řetězce dotazu je `query`, což je **dotaz** promluvy. Tato promluva není stejná jako žádná z označených promluv, proto je to dobrý test a měl by se vrátit záměr `OrderPizza` s extrahovanou analýzou mínění.

    ```json
    {
        "query": "Deliver 2 of the best cheese pizzas ever!!!",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.4659952
                },
                "None": {
                    "score": 0.16901511
                },
                "Confirm": {
                    "score": 0.00421415
                },
                "Greeting": {
                    "score": 0.00109914376
                }
            },
            "entities": {
                "number": [
                    2
                ],
                "$instance": {
                    "number": [
                        {
                            "type": "builtin.number",
                            "text": "2",
                            "startIndex": 8,
                            "length": 1,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            },
            "sentiment": {
                "label": "positive",
                "score": 0.861665964
            }
        }
    }
    ```

    Analýza mínění je kladná s skóre 86%.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Související informace

* Mínění je k dispozici pomocí [Analýza textu](../Text-Analytics/index.yml)služby pro rozpoznávání. Tato funkce je omezená, aby Analýza textu [podporované jazyky](luis-language-support.md##languages-supported).
* [Postup výuky](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Testování na portálu LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Další kroky
V tomto kurzu se přidá analýza mínění jako nastavení publikování tak, aby se extrahovaly hodnoty mínění z promluvy jako celku.

> [!div class="nextstepaction"]
> [Kontrola promluv koncového bodu v aplikaci HR](luis-tutorial-review-endpoint-utterances.md)

