---
title: 'Kurz: předpověď záměrů – LUIS'
description: Vytvořte vlastní aplikaci, která předpovídá záměr uživatele na základě utterance (text) v tomto kurzu.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/05/2020
ms.openlocfilehash: 398324099f923a5141b172d61f9fd41a69dbf069
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025289"
---
# <a name="tutorial-build-a-luis-app-to-determine-user-intentions"></a>Kurz: Vytvoření aplikace v LUIS pro určení záměrů uživatele

V tomto kurzu vytvoříte vlastní aplikaci, která předpovídá záměr uživatele na základě utterance (text).

**V tomto kurzu se naučíte:**

> [!div class="checklist"]
> * Vytvoření nové aplikace
> * Vytvořit záměry
> * Přidat ukázkové promluvy
> * Trénovat aplikaci
> * Publikovat aplikaci
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
|`None`|Zjistit, jestli uživatel žádá o něco, aplikace LUIS není navržená tak, aby odpovídala. Tento záměr se poskytuje jako součást vytváření aplikací a nedá se odstranit. |

## <a name="create-a-new-app"></a>Vytvoření nové aplikace

[!INCLUDE [Follow these steps to create a new LUIS app](includes/create-pizza-app.md)]

## <a name="create-a-new-intent"></a>Vytvoření nového záměru

Záměr slouží ke klasifikaci projevy uživatelů na základě záměru uživatele, který je určen z textu přirozeného jazyka.

Aby bylo možné klasifikovat utterance, záměr potřebuje příklady uživatelských projevy, které by měly být klasifikovány pomocí tohoto záměru.

1. V části **sestavení** na stránce **záměry** vyberte **+ vytvořit** pro vytvoření nového záměru. Zadejte název nového záměru a `OrderPizza` potom vyberte **Hotovo**.

    `OrderPizza`Záměr je předpovězený, když chce uživatel objednat Pizza.

1. Do tohoto záměru přidejte několik příkladů projevy, u kterých očekáváte, že se uživatel zeptá:

    |`OrderPizza` Příklad projevy|
    |--|
    |`can i get a pepperoni pizza and a can of coke please`|
    |`can i get a small pizza with onions peppers and olives`|
    |`delivery for a small pepperoni pizza`|
    |`pickup a cheddar cheese pizza large with extra anchovies`|
    |`i need 2 large cheese pizzas 6 large pepperoni pizzas and 1 large supreme pizza`|
    |`Order a pizza for me`|

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s přidáním ukázkového projevyu na stránce záměru LUIS na portálu](media/tutorial-intents-only/add-example-utterances-for-pizza-order.png)

    Poskytnutím _ukázkového projevyu_ Luis informace o tom, jaké druhy projevyy by se měly pro tento záměr odhadnout. Tyto příklady jsou pozitivní. Projevy ve všech ostatních záměrech se pro tento záměr považují za negativní příklady.

    [!INCLUDE [Do not use too few utterances](includes/do-not-use-too-few-utterances.md)]

## <a name="create-remaining-intents"></a>Vytvořit zbývající záměry

1. Vytvořte `Greeting` záměr a přidejte následující příklad projevy. Účelem je určit, jestli uživatel začíná novou konverzaci v Pizza pořadí.

    |`Greeting` Příklad projevy|
    |--|
    |`Hi`|
    |`Hello`|
    |`Hey`|
    |`Start`|
    |`Begin`|

1. Vytvořte `Confirm` záměr a přidejte následující příklad projevy. Cílem je zjistit, jestli je uživatel hotový, a přijmout podrobnosti objednávky.

    |`Confirm` Příklad projevy|
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

  
    Parametr řetězce dotazu je `query` , **dotaz** utterance se PŘEdává v identifikátoru URI. Tato promluva není stejná jako některá z ukázkových promluv. To by mělo být dobrý test, který ověří, jestli se LUIS učí, a předpovídá `OrderPizza` záměr jako nejvyšší záměr hodnocení.

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

    Výsledek JSON identifikuje nejvyšší záměr bodování jako **`prediction.topIntent`** vlastnost. Všechna skóre jsou mezi 1 a 0 a lepším skóre je blíže 1.

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

V tomto kurzu jste vytvořili aplikaci LUIS, vytvořené záměry, Přidali jste vzorový projevy ke každému záměru a Přidali jste příklad projevy k záměru None, trained, Published a test na koncovém bodu. Toto jsou základní kroky vytvoření modelu LUIS.

Poté, co LUIS vrátí odpověď JSON, služba LUIS s touto žádostí skončila. Služba LUIS neposkytuje odpovědi na promluvy uživatelů, pouze identifikuje, na jaký typ informací směřuje dotaz v přirozeném jazyce. Následná následné konverzace poskytuje klientská aplikace, jako je třeba robot Azure.


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Související informace

* [Typy entit](luis-concept-entity-types.md)
* [Postup výuky](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Testování na portálu LUIS](luis-interactive-test.md)
* [Robot Azure bot](/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidat do této aplikace entitu s možností destavitele](tutorial-machine-learned-entity.md)