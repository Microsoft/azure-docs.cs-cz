---
title: 'Kurz: Předvídání záměrů – LUIS'
description: V tomto kurzu vytvořte vlastní aplikaci, která předpovídá záměr uživatele. Tato aplikace představuje nejjednodušší typ aplikace LUIS, protože neextrahuje různé datové prvky z textu promluvy, jako jsou e-mailové adresy nebo kalendářní data.
ms.topic: tutorial
ms.date: 03/24/2020
ms.openlocfilehash: c58c96f717de77c065d7f844928714eb4fb3e4db
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80286740"
---
# <a name="tutorial-build-a-luis-app-to-determine-user-intentions"></a>Kurz: Vytvoření aplikace LUIS k určení uživatelských záměrů

V tomto kurzu vytvoříte vlastní aplikaci, která předpovídá záměr uživatele na základě utterance (text).

**V tomto kurzu se dozvíte, jak:**

> [!div class="checklist"]
> * Vytvoření nové aplikace
> * Vytvořit záměry
> * Přidat ukázkové promluvy
> * Trénovat aplikaci
> * Publikování aplikace
> * Získat předpověď záměru z koncového bodu

## <a name="user-intentions-as-intents"></a>Uživatelské záměry jako záměry

Účelem aplikace je určit záměr konverzačního textu v přirozeném jazyce:

`I'd like to order a veggie pizza with a salad on the side.`

Tyto záměry jsou zařazené do kategorie **Intents** (Záměry).

|Záměr|Účel|
|--|--|
|`ModifyOrder`|Určete objednávku pizzy uživatele.|
|`Greeting`|Začněte konverzaci s roboty.|
|`ConfirmOrder`|Potvrďte objednávku pizzy.|
|`None`|Zjistěte, zda se uživatel ptá na něco, na co by aplikace neměla odpovídat. Tento záměr, pokud je k dispozici jako součást vytváření aplikací a nelze odstranit. |

## <a name="create-a-new-app"></a>Vytvoření nové aplikace

[!INCLUDE [Follow these steps to create a new LUIS app](includes/create-pizza-app.md)]

## <a name="create-a-new-intent"></a>Vytvoření nového záměru

Záměr se používá ke klasifikaci projevy uživatele na základě záměru uživatele, určené z textu přirozeného jazyka.

Chcete-li klasifikovat utterance, záměr potřebuje příklady projevy uživatele, které by měly být klasifikovány s tímto záměrem.

1. V části **Sestavení** na stránce **Záměry** vyberte **+ Vytvořit,** chcete-li vytvořit nový záměr. Zadejte nový název `OrderPizza`záměru a vyberte **Hotovo**.

    Záměr `OrderPizza` je předpovězen, když uživatel chce objednat pizzu.

1. Přidejte několik příklad projevy k tomuto záměru, který očekáváte, že uživatel se zeptat:

    |`OrderPizza`příklad projevy|
    |--|
    |`can i get a pepperoni pizza and a can of coke please`|
    |`can i get a small pizza with onions peppers and olives`|
    |`delivery for a small pepperoni pizza`|
    |`pickup a cheddar cheese pizza large with extra anchovies`|
    |`i need 2 large cheese pizzas 6 large pepperoni pizzas and 1 large supreme pizza`|
    |`Order a pizza for me`|

    ![Přidat ukázkové promluvy](media/tutorial-intents-only/add-example-utterances-for-pizza-order.png)

    Poskytnutím _příklad projevy_, učíte LUIS o jaké druhy projevy by měly být předpovězeny pro tento záměr.

    [!INCLUDE [Do not use too few utterances](includes/do-not-use-too-few-utterances.md)]

## <a name="create-remaining-intents"></a>Vytvořit zbývající záměry

1. Vytvořte `Greeting` záměr a přidejte následující příklad projevy. Toto je záměr zjistit, zda uživatel začíná novou konverzaci objednávky pizzy.

    |`Greeting`příklad projevy|
    |--|
    |`Hi`|
    |`Hello`|
    |`Hey`|
    |`Start`|
    |`Begin`|

1. Vytvořte `Confirm` záměr a přidejte následující příklad projevy. Toto je záměr zjistit, zda uživatel provádí objednávání a přijímá podrobnosti objednávky.

    |`Confirm`příklad projevy|
    |--|
    |`Go ahead`|
    |`ok`|
    |`Yes`|
    |`Sure`|


## <a name="none-intent-example-utterances"></a>Žádný záměr příklad projevy

[!INCLUDE [Follow these steps to add the None intent to the app](includes/add-example-utterances-none-intent.md)]

## <a name="train-the-app"></a>Trénování aplikace

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app"></a>Publikování aplikace

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-prediction"></a>Získat předpověď záměru

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Přejděte na konec adresy URL v adresním řádku a zadejte:

    `get a medium vegetarian pizza for delivery`

    To není přesně stejný jako příklad utterance, takže je dobrý test, pokud LUIS můžete zjistit, co by měl y predikovat s tímto záměrem.

    Poslední parametr řetězce dotazu je `query`, což je **dotaz** promluvy. Tato promluva není stejná jako některá z ukázkových promluv. Jde o vhodný test a jako záměr s nejvyšším skóre by se měl vrátit záměr `OrderPizza`.

    ```JSON
    {
        "query": "get a medium vegetarian pizza for delivery",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.6488959
                },
                "None": {
                    "score": 0.139966831
                },
                "Confirm": {
                    "score": 0.00736504374
                },
                "Greeting": {
                    "score": 0.003970454
                }
            },
            "entities": {}
        }
    }
    ```

    Pole entit je prázdné, protože tato aplikace aktuálně nemá žádné entity (jednotka dat uvnitř utterance extrahovat).

    Výsledek JSON identifikuje záměr nejvyššího **`prediction.topIntent`** bodování jako vlastnost. Všechny skóre jsou mezi 1 a 0, s lepší skóre je blíže k 1.

1. Změňte parametr **dotazu** URL tak, aby cílil na záměr **Pozdravu:**

    `Howdy`

    To není přesně stejný jako příklad utterance, takže je dobrý test, pokud LUIS můžete zjistit, co by měl y predikovat s tímto záměrem.

    ```json
    {
        "query": "howdy",
        "prediction": {
            "topIntent": "Greeting",
            "intents": {
                "Greeting": {
                    "score": 0.446016937
                },
                "Confirm": {
                    "score": 0.2390079
                },
                "None": {
                    "score": 0.09119555
                },
                "OrderPizza": {
                    "score": 0.00109590159
                }
            },
            "entities": {}
        }
    }
    ```

    Tato předpověď má 44% skóre spolehlivosti. Chcete-li zvýšit skóre spolehlivosti, přidejte mezi 15 a 30 příklad projevy.

## <a name="client-application-next-steps"></a>Další kroky klientské aplikace

Poté, co LUIS vrátí odpověď JSON, služba LUIS s touto žádostí skončila. Služba LUIS neposkytuje odpovědi na promluvy uživatelů, pouze identifikuje, na jaký typ informací směřuje dotaz v přirozeném jazyce. Konverzační zpracování je poskytována klientskou aplikací, jako je například Azure Bot.


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Související informace

* [Typy entit](luis-concept-entity-types.md)
* [Jak trénovat](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Jak testovat na portálu LUIS](luis-interactive-test.md)
* [Robot Azure](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>Další kroky

Tento kurz vytvořil aplikaci LUIS, vytvořil záměry, přidal ukázkové projevy ke každému záměru, přidal příklad projevy k záměru None, trénované, publikované a testované v koncovém bodě. Toto jsou základní kroky vytvoření modelu LUIS.

> [!div class="nextstepaction"]
> [Přidání rozložitelné entity do této aplikace](tutorial-machine-learned-entity.md)
