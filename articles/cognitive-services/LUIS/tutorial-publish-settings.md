---
title: 'Kurz: Publikovat nastavení - LUIS'
description: V tomto kurzu změňte nastavení publikování získat zlepšení předpovědi.
ms.topic: tutorial
ms.date: 04/01/2020
ms.openlocfilehash: 19913d16ecb1457ad4edb93ea34e4b96a590aca0
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545779"
---
# <a name="tutorial--add-sentiment-analysis-as-a-publishing-setting"></a>Kurz: Přidání analýzy mínění jako nastavení publikování

V tomto kurzu upravte nastavení publikování extrahovat analýzu mínění a pak dotaz u koncového bodu LUIS zobrazíte vrácené mínění uživatele utterance.

**V tomto kurzu se dozvíte, jak:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Přidání analýzy mínění jako nastavení publikování
> * Získat mínění utterance z publikovaného koncového bodu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>Analýza mínění je nastavení publikování

Následující promluvy ukazují příklady mínění:

|Mínění|Skóre|Promluva|
|:--|:--|:--|
|negativní|0.01 |Pizza byla hrozná.|
|pozitivní|0.97 |Sýrová pizza byla úžasná.|

Analýza mínění je nastavení publikování, které se vztahuje na všechny promluvy. Po nastavení aplikace vrátí mínění utterance bez nutnosti označovat data.

Vzhledem k tomu, že se jedná o nastavení publikování, nevidíte jej na stránkách záměrů nebo entit. Zobrazuje se v podokně [interaktivního testu](luis-interactive-test.md#view-sentiment-results) nebo při testování na adrese URL koncového bodu.

## <a name="import-example-json-to-begin-app"></a>Import příkladu json pro spuštění aplikace

1.  Stáhněte a uložte [soubor JSON aplikace](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app"></a>Trénování aplikace

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Konfigurace aplikace pro zahrnutí analýzy mínění

1. V horní nabídce vyberte **Publikovat.** Analýza mínění je nastavení publikování.

1. Vyberte **Produkční patu** a pak vyberte **Změnit nastavení**.
1. Nastavte nastavení Analýzy mínění **na Zapnuto**.

    ![Zapnutí analýzy mínění jako nastavení publikování](./media/luis-quickstart-intent-and-sentiment-analysis/select-sentiment-publishing-setting.png)

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Získat mínění utterance z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Přejděte na konec adresy URL v adresním řádku a nahraďte _YOUR_QUERY_HERE:_

    `Deliver 2 of the best cheese pizzas ever!!!`

    Tato promluva není stejná jako žádná z označených promluv, proto je to dobrý test a měl by se vrátit záměr `OrderPizza` s extrahovanou analýzou mínění.

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

    Analýza mínění je pozitivní se skóre 86%.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Související informace

* Analýzu mínění poskytuje služba Cognitive Service [Text Analytics](../Text-Analytics/index.yml). Tato funkce je omezena na [jazyky podporované](luis-language-support.md#languages-supported)službou Text Analytics .
* [Jak trénovat](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Jak testovat na portálu LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Další kroky
V tomto kurzu se přidá analýza mínění jako nastavení publikování tak, aby se extrahovaly hodnoty mínění z promluvy jako celku.

> [!div class="nextstepaction"]
> [Kontrola promluv koncového bodu v aplikaci HR](luis-tutorial-review-endpoint-utterances.md)

