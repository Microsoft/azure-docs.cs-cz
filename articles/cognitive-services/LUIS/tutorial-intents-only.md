---
title: 'Kurz: předpověď záměrů – LUIS'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvoříte vlastní aplikaci, která předpovídá záměr uživatele. Tato aplikace představuje nejjednodušší typ aplikace LUIS, protože neextrahuje různé datové prvky z textu promluvy, jako jsou e-mailové adresy nebo kalendářní data.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 89fb76f8c5cc4323e1211524340c0965a7d0716d
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76262741"
---
# <a name="tutorial-build-a-luis-app-to-determine-user-intentions"></a>Kurz: Vytvoření aplikace v LUIS pro určení záměrů uživatele

V tomto kurzu vytvoříte vlastní aplikaci, která předpovídá záměr uživatele na základě utterance (text).

**Co se v tomto kurzu naučíte:**

> [!div class="checklist"]
> * Vytvoření nové aplikace
> * Vytvořit záměry
> * Přidat ukázkové promluvy
> * Trénovat aplikaci
> * Publikování aplikace
> * Získat předpověď záměru z koncového bodu

## <a name="user-intentions-as-intents"></a>Záměry uživatelů jako záměrů

Účelem aplikace je určit záměr konverzací, text přirozeného jazyka:

`I'd like to order a veggie pizza with a salad on the side.`

Tyto záměry jsou zařazené do kategorie **Intents** (Záměry).

|Záměr|Účel|
|--|--|
|`ModifyOrder`|Určuje pořadí Pizza uživatele.|
|`Greeting`|Začněte s konverzací robot.|
|`ConfirmOrder`|Potvrďte pořadí Pizza.|
|`None`|Určete, jestli uživatel žádá o něco, co aplikace nemá odpovídat. Tento záměr, pokud je k dispozici jako součást vytváření aplikace a nelze jej odstranit. |

## <a name="create-a-new-app"></a>Vytvoření nové aplikace

[!INCLUDE [Follow these steps to create a new LUIS app](includes/create-pizza-app.md)]

## <a name="create-a-new-intent"></a>Vytvoření nového záměru

1. Na portálu uvnitř oddílu **Build** aplikace vyberte **+ vytvořit**. Zadejte název nového záměru `OrderPizza`a potom vyberte **Hotovo**.

    Předpokládá se `OrderPizza` záměr, když: uživatel chce objednat Pizza.

1. Do tohoto záměru přidejte několik příkladů projevy, u kterých očekáváte, že se uživatel zeptá:

    |`OrderPizza` příklad projevy|
    |--|
    |`can i get a pepperoni pizza and a can of coke please`|
    |`can i get a small pizza with onions peppers and olives`|
    |`delivery for a small pepperoni pizza`|
    |`pickup a cheddar cheese pizza large with extra anchovies`|
    |`i need 2 large cheese pizzas 6 large pepperoni pizzas and 1 large supreme pizza`|
    |`Order a pizza for me`|

    ![Přidat ukázkové promluvy](media/tutorial-intents-only/add-example-utterances-for-pizza-order.png)

    Poskytnutím _ukázkového projevyu_budete školením Luis o tom, jaké druhy projevyy by se měly pro tento záměr odhadnout.

    [!INCLUDE [Do not use too few utterances](includes/do-not-use-too-few-utterances.md)]

## <a name="create-remaining-intents"></a>Vytvořit zbývající záměry

1. Vytvořte `Greeting` záměr a přidejte následující příklad projevy. Účelem je určit, jestli uživatel začíná novou konverzaci v Pizza pořadí.

    |`Greeting` příklad projevy|
    |--|
    |`Hi`|
    |`Hello`|
    |`Hey`|
    |`Start`|
    |`Begin`|

1. Vytvořte `Confirm` záměr a přidejte následující příklad projevy. Cílem je zjistit, jestli je uživatel hotový, a přijmout podrobnosti objednávky.

    |`Confirm` příklad projevy|
    |--|
    |`Go ahead`|
    |`ok`|
    |`Yes`|
    |`Sure`|


## <a name="none-intent-example-utterances"></a>Projevy příklad záměru None

[!INCLUDE [Follow these steps to add the None intent to the app](includes/add-example-utterances-none-intent.md)]

## <a name="train-the-app"></a>Trénování aplikace

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app"></a>Publikování aplikace

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-prediction"></a>Získat předpověď záměru

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. V adresním řádku přejděte na konec adresy URL a zadejte:

    `get a medium vegetarian pizza for delivery`

    To není naprosto stejné jako u příkladu utterance, takže je dobrým testem zjistit, jestli se LUIS může naučit, co by mělo být s tímto záměrem předpověď.

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

    Pole entit je prázdné, protože tato aplikace v současné době neobsahuje žádné entity (v rámci utterance k extrakci dat).

    Výsledek JSON identifikuje záměr s nejvyšším skóre jako vlastnost **`prediction.topIntent`** . Všechna skóre jsou mezi 1 a 0 a lepším skóre je blíže 1.

1. Změňte parametr **dotazu** adresy URL tak, aby byl cílen na záměr **pozdravu** :

    `Howdy`

    To není naprosto stejné jako u příkladu utterance, takže je dobrým testem zjistit, jestli se LUIS může naučit, co by mělo být s tímto záměrem předpověď.

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

    Tato předpověď má 44% spolehlivost. Chcete-li zvýšit míru spolehlivosti, přidejte mezi 15 a 30 příkladem projevy.

## <a name="client-application-next-steps"></a>Klient-aplikace – další kroky

Poté, co LUIS vrátí odpověď JSON, služba LUIS s touto žádostí skončila. Služba LUIS neposkytuje odpovědi na promluvy uživatelů, pouze identifikuje, na jaký typ informací směřuje dotaz v přirozeném jazyce. Následná následné konverzace poskytuje klientská aplikace, jako je třeba robot Azure.


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Související informace

* [Typy entit](luis-concept-entity-types.md)
* [Postup výuky](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Testování na portálu LUIS](luis-interactive-test.md)
* [Robot Azure bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili aplikaci LUIS, vytvořené záměry, Přidali jste vzorový projevy ke každému záměru a Přidali jste příklad projevy k záměru None, trained, Published a test na koncovém bodu. Toto jsou základní kroky vytvoření modelu LUIS.

> [!div class="nextstepaction"]
> [Přidat do této aplikace entitu s možností destavitele](tutorial-machine-learned-entity.md)
