---
title: 'Kurz: Entita Seznam – LUIS'
description: Získejte data, která odpovídají předem definovanému seznamu položek. Každá položka v seznamu může mít synonyma, která také přesně odpovídají.
ms.topic: tutorial
ms.date: 03/12/2020
ms.openlocfilehash: 1cfeccbd54e8ef8ec315d53fc7a766760c92a0d1
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "79297403"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance-with-list-entity"></a>Kurz: Získejte přesná data odpovídající textu z entity utterance with list

V tomto kurzu pochopit, jak získat data, která přesně odpovídá předdefinovaný seznam položek.

**V tomto kurzu se dozvíte, jak:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Import aplikace a použití existujícího záměru
> * Přidání entity seznamu
> * Trénování, publikování a dotazování aplikace získat extrahovaná data

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>Co je entita seznamu?

Entita seznamu je přesná shoda textu se slovy v utterance. Ke každé položce v seznamu může existovat seznam synonym. Entitu seznamu použijte, pokud chcete přesnou shodu.

Pro tuto importovoaplikaci pizzy vytvořte entitu seznamu pro různé typy kůry pizzy.

Entita seznamu je vhodná pro tento typ dat, když platí následující:

* Hodnoty dat jsou známou sadou.
* Tato sada nepřekračuje maximální [hranice](luis-boundaries.md) aplikace LUIS pro tento typ entity.
* Text v utterance je malá a velká písmena shoda se synonymem nebo kanonický název. Služba LUIS nepoužívá seznam mimo shodu. Vyplývající, množné číslo a další varianty nejsou vyřešeny pouze entitou seznamu. Chcete-li spravovat varianty, zvažte použití [vzoru](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) s volitelnou syntaxí textu.

> [!CAUTION]
> Pokud si nejste jisti, zda chcete entitu seznamu nebo entitu naučenou počítačem se seznamem frází jako popisovač, je nejlepší a nejflexibilnější praxe použít entitu naučenou počítačem se seznamem frází jako popisovač. Tato metoda umožňuje LUIS učit se a rozšířit hodnoty dat extrahovat.

## <a name="import-example-json-and-add-utterances"></a>Importovat příklad json a přidat projevy

1.  Stáhněte a uložte [soubor JSON aplikace](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

    [!INCLUDE [Import app steps](includes/import-app-steps.md)]

1. Importovaná aplikace má `OrderPizza` záměr. Vyberte tento záměr a přidejte několik promluv s novými typy kůry:

    |Nové projevy|
    |--|--|
    |prosím, objednejte si pánev kůra malé pepperoni pizza|
    |3 tenké kůry havajské pizzy|
    |dodat 2 plněné kůrky pizzy s chlebovými tyčinkami|
    |jedna tlustá kůrka pizza pro vyzvednutí|
    |jedna hluboká miska pepperoni pizza|

## <a name="crust-list-entity"></a>Entita seznamu kůry

Nyní **OrderPizza** záměr má příklad projevy s typy kůry, LUIS potřebuje pochopit, která slova představují typy kůry.

Příklady primárního názvu a synonym jsou:

|Kanonický název|Synonyma|
|--|--|
|Hluboké jídlo|Hluboké<br>hluboká miska kůra<br>Tlustý<br>hustá kůra|
|Posouvání|Pravidelné<br>Původní<br>Normální<br>pravidelná kůra<br>původní kůra<br>normální kůra|
|Plněné|plněná kůrka|
|Dynamický|tenká kůra<br>Hubená<br>hubená kůra|

1. Na levém panelu vyberte **Entities** (Entity).

1. Vyberte **+ Vytvořit**.

1. V automaticky otevíraném dialogovém okně entity zadejte `CrustList` jako název entity a **List** (Seznam) jako typ entity. Vyberte **další**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s dialogovým oknem pro vytváření nové entity](media/luis-quickstart-intent-and-list-entity/create-pizza-crust-list-entity.png)

1. Na stránce **Vytvořit entitu seznamu** zadejte kanonické názvy a synonyma pro každý kanonický název a vyberte **vytvořit**.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s přidáním položek do entity seznamu](media/luis-quickstart-intent-and-list-entity/add-pizza-crust-items-list-entity.png)

    Když přidáte entitu seznamu do aplikace LUIS, nemusíte [popisovat](label-entity-example-utterance.md) text entitou seznamu. Je použita pro všechny projevy ve všech záměrech.

## <a name="train-the-app-before-testing-or-publishing"></a>Trénování aplikace před testováním nebo publikováním

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publikování aplikace k dotazování z koncového bodu

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Získání záměru a predikce entit z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. Přejděte na konec adresy URL v adrese a zadejte následující promluvu:

    `Deliver 2 deep dish hawaiian pizzas and a thin pepperoni`

    Poslední parametr querystring `query`je , **utterance dotazu**.


    ```json
    {
        "query": "Deliver 2 deep dish hawaiian pizzas and a thin pepperoni",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.9957229
                },
                "None": {
                    "score": 0.016832687
                },
                "Confirm": {
                    "score": 0.0015708931
                },
                "Greeting": {
                    "score": 0.00057060417
                }
            },
            "entities": {
                "number": [
                    2
                ],
                "CrustList": [
                    [
                        "Deep dish"
                    ],
                    [
                        "Thin"
                    ]
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
                    ],
                    "CrustList": [
                        {
                            "type": "CrustList",
                            "text": "deep dish",
                            "startIndex": 10,
                            "length": 9,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "CrustList",
                            "text": "thin",
                            "startIndex": 42,
                            "length": 4,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

    Typy kůry byly nalezeny jako přesné textové shody a vráceny v odpovědi JSON. Tyto informace používají klientská aplikace ke zpracování objednávky.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Související informace

* Koncepční informace [o entitě seznamu](luis-concept-entity-types.md#list-entity)
* [Jak trénovat](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Jak testovat na portálu LUIS](luis-interactive-test.md)
* [Koncepce - entity](luis-concept-entity-types.md)
* [JSON odkaz entity regulárního výrazu](reference-entity-regular-expression.md?tabs=V3)
* [Jak přidat entity pro extrahování dat](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Další kroky
Tento kurz přidal příklad projevy, pak vytvořil seznam entity extrahovat přesné textové shody z projevy. Po natrénování a publikování aplikace jste dotazem adresovaným koncovému bodu zjistili záměr a vrátili extrahovaná data.

> [!div class="nextstepaction"]
> [Přidání předem sestavené entity s rolí](tutorial-entity-roles.md)

