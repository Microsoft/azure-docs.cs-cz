---
title: 'Kurz: seznam entit – LUIS'
titleSuffix: Azure Cognitive Services
description: Získejte data, která odpovídají předem definovanému seznamu položek. Každá položka v seznamu může mít synonyma, která také přesně odpovídají.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: diberry
ms.openlocfilehash: 83644ac7144adaa664b13b6e7d7993c50da0e779
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851887"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance-with-list-entity"></a>Kurz: získání přesného textu se shodnými daty z entity utterance a list

V tomto kurzu se seznámíte s tím, jak získat data, která se přesně shodují s předdefinovaným seznamem položek.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**Co se v tomto kurzu naučíte:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importovat aplikaci a použít stávající záměr
> * Přidání entity seznamu
> * Školení, publikování a dotazování aplikace pro získání extrahovaných dat

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>Co je entita seznamu?

Entita seznamu je přesný text, který se shoduje se slovy v utterance. Ke každé položce v seznamu může existovat seznam synonym. Pokud chcete vyhledat přesnou shodu, použijte entitu seznam.

Pro tuto importovanou aplikaci Pizza vytvořte entitu seznam pro různé typy Pizza crust.

Entita seznamu je vhodná pro tento typ dat, když platí následující:

* Hodnoty dat jsou známou sadou.
* Tato sada nepřekračuje maximální [hranice](luis-boundaries.md) aplikace LUIS pro tento typ entity.
* Text promluvy se přesně shoduje se synonymem nebo názvem v kanonickém tvaru. LUIS nepoužívá seznam nad rámec přesně vyhovujících textů. Odvozování, plural a další variace se nevyřešily pouze entitou seznamu. Chcete-li spravovat variace, zvažte použití [vzoru](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) s volitelnou syntaxí textu.

> [!CAUTION]
> Pokud si nejste jistí, jestli chcete, aby entita seznamu nebo entita s odkazem na počítač se seznamem frází jako s popisovačem, nejlepším a nejpružnější praxí je použít entitu s frází, která je v seznamu frází jako popisovač. Tato metoda umožňuje LUIS zjistit a roztáhnout hodnoty dat k extrakci.

## <a name="import-example-json-and-add-utterances"></a>Import example. JSON a přidání projevy

1.  Stáhněte a uložte [soubor JSON aplikace](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

    [!INCLUDE [Import app steps](includes/import-app-steps.md)]

1. Importovaná aplikace má `OrderPizza` záměr. Vyberte tento záměr a přidejte několik projevy s novými typy crust:

    |Nový projevy|
    |--|--|
    |Seřaďte si crust malé pepperoniy na úrovni pan a Pizza|
    |3 tenké crust havajština pizzas|
    |doručovat 2 crust pizzas s chlebem – hole|
    |jeden silný crust Pizza pro vyzvednutí|
    |Jedna hluboká miska pepperoni pizza|

## <a name="crust-list-entity"></a>Entita seznamu crust

Teď, když má záměr **OrderPizza** příklad projevy s crust typy, Luis musí pochopit, která slova reprezentují typy crust.

Příklady primárního názvu a synonym jsou:

|Kanonický název|Synonyma|
|--|--|
|Hluboká miska|úrovní<br>crust hloubkové misky<br>tlustá<br>silné crust|
|Posouvání|regulární<br>Původně<br>normální<br>běžný crust<br>původní crust<br>normální crust|
|Nejoblíbenější|crustd|
|Dynamický|tenké crust<br>změny vzhledu<br>crust změny vzhledu|

1. Na levém panelu vyberte **Entities** (Entity).

1. Vyberte **+ vytvořit**.

1. V automaticky otevíraném dialogovém okně entity zadejte `CrustList` jako název entity a **List** (Seznam) jako typ entity. Vyberte **Další**.

    > [!div class="mx-imgBorder"]
    > ![snímek obrazovky s dialogovým oknem vytvořit novou entitu](media/luis-quickstart-intent-and-list-entity/create-pizza-crust-list-entity.png)

1. Na stránce **vytvořit entitu seznamu** zadejte kanonické názvy a synonyma pro každý kanonický název a pak vyberte **vytvořit**.

    > [!div class="mx-imgBorder"]
    > ![snímek obrazovky s přidáním položek do seznamu](media/luis-quickstart-intent-and-list-entity/add-pizza-crust-items-list-entity.png) entit

    Když přidáte entitu seznamu do aplikace LUIS, nemusíte tento text [označovat](label-entity-example-utterance.md) entitou seznamu. Aplikuje se na všechny projevy ve všech záměrech.

## <a name="train-the-app-before-testing-or-publishing"></a>Výuka aplikace před testováním nebo publikováním

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publikování aplikace pro dotaz z koncového bodu

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Získání záměru a předpovědi entit z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. Přejděte na konec adresy URL v adrese a zadejte následující utterance:

    `Deliver 2 deep dish hawaiian pizzas and a thin pepperoni`

    Poslední parametr řetězce dotazu je `query`, což je **dotaz** promluvy.


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

    Typy crust byly nalezeny jako přesné shody textu a vráceny v odpovědi JSON. Tyto informace používá klientská aplikace ke zpracování objednávky.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Související informace

* [Výpis](luis-concept-entity-types.md#list-entity) koncepčních informací entity
* [Postup výuky](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Testování na portálu LUIS](luis-interactive-test.md)
* [Koncept – entity](luis-concept-entity-types.md)
* [Odkaz JSON entity regulárního výrazu](reference-entity-regular-expression.md?tabs=V3)
* [Postup přidání entit pro extrakci dat](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Další kroky
Tento kurz přidal příklad projevy a pak vytvořil entitu seznamu pro extrakci přesně vyhovujících textů z projevy. Dotaz na koncový bod po trénování a publikování aplikace identifikoval záměr a vrátil extrahovaná data.

> [!div class="nextstepaction"]
> [Přidat předem vytvořenou entitu s rolí](tutorial-entity-roles.md)

