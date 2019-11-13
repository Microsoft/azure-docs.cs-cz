---
title: 'Kurz: shoda s přesným textem – LUIS'
titleSuffix: Azure Cognitive Services
description: Získejte data, která odpovídají předem definovanému seznamu položek. Každá položka v seznamu může mít synonyma, která také přesně odpovídají.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: a8021885bf51ab6d44bc8576b9fdd69f1bdd270a
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953720"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance"></a>Kurz: získání přesného textu se shodnými daty z utterance

V tomto kurzu se seznámíte s tím, jak získat data entity, která odpovídají předdefinovanému seznamu položek. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**Co se v tomto kurzu naučíte:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Vytvořit aplikaci
> * Přidat záměr
> * Přidání entity seznamu 
> * Trénování 
> * Publikování
> * Zjistit záměry a entity z koncového bodu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>Co je entita seznamu?

Entita seznamu je přesný text, který se shoduje se slovy v utterance. 

Ke každé položce v seznamu může existovat seznam synonym. V případě aplikace pro lidské zdroje může být oddělení společnosti identifikováno několika klíčovými informacemi, jako je například oficiální název, běžné zkratky a kódy fakturačního oddělení. 

Aplikace lidských zdrojů potřebuje určit oddělení, na které se zaměstnanec převádí. 

Entita seznamu je vhodná pro tento typ dat, když platí následující:

* Hodnoty dat jsou známou sadou.
* Tato sada nepřekračuje maximální [hranice](luis-boundaries.md) aplikace LUIS pro tento typ entity.
* Text promluvy se přesně shoduje se synonymem nebo názvem v kanonickém tvaru. LUIS nepoužívá seznam nad rámec přesně vyhovujících textů. Odvozování, plural a další variace se nevyřešily pouze entitou seznamu. Chcete-li spravovat variace, zvažte použití [vzoru](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) s volitelnou syntaxí textu. 

## <a name="create-a-new-app"></a>Vytvoření nové aplikace

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-transfer-employees-to-a-different-department"></a>Vytvoření záměru pro přenos zaměstnanců do jiného oddělení

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Vyberte **Create new intent** (Vytvořit nový záměr). 

3. V automaticky otevíraném dialogovém okně zadejte `TransferEmployeeToDepartment` a pak vyberte **Done** (Hotovo). 

    ![Snímek obrazovky s dialogovým oknem pro vytvoření nového záměru](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. Přidejte do záměru ukázkové promluvy.

    |Ukázkové promluvy|
    |--|
    |přesunout Jan W. Novák do účetního oddělení|
    |přenos Jill Novotný z na R & D|
    |Oddělení 1234 má nového člena s názvem Bill Bradstreet|
    |Umístění Jan Jacksonův in Engineering |
    |přesunout Debra Doughtery na prodej v rámci|
    |MV Jill Novotný|
    |Posunutí Alice Anderson na DevOps|
    |Carl Chamerlin na finance|
    |Steve Standish na 1234|
    |Tanner Václav na 3456|

    [![Snímek obrazovky záměru s příkladem projevy](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png "Snímek obrazovky záměru s příkladem projevy")](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="department-list-entity"></a>Entita seznamu oddělení

Teď, když má **TransferEmployeeToDepartment** záměr příklad projevy, Luis musí pochopit, co je oddělení. 

Primární, _kanonický_název pro každou položku je název oddělení. Příklady synonym každého kanonického názvu jsou: 

|Kanonický název|Synonyma|
|--|--|
|Účtárna|účtu<br>accting<br>3456|
|Operace vývoje|Vývoj a provoz<br>4949|
|Inženýrství|ENG<br>modulu<br>4567|
|Finance|–<br>2020|
|Informační technologie|IT<br>2323|
|V rámci prodeje|isale<br>prodej<br>1414|
|Výzkum a vývoj|R & D<br>1234|

1. Na levém panelu vyberte **Entities** (Entity).

1. Vyberte **Create new entity** (Vytvořit novou entitu).

1. V automaticky otevíraném dialogovém okně entity zadejte `Department` jako název entity a **List** (Seznam) jako typ entity. Vyberte **Done** (Hotovo).  

    [![Snímek obrazovky s místním dialogovým oknem pro vytváření nových entit](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png "Snímek obrazovky s místním dialogovým oknem pro vytváření nových entit")](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png#lightbox)

1. Na stránce entita oddělení zadejte jako novou hodnotu `Accounting`.

1. V případě synonym přidejte synonyma z předchozí tabulky.

1. Pokračujte v přidávání všech kanonických názvů a jejich synonym. 

## <a name="add-example-utterances-to-the-none-intent"></a>Přidat příklad projevy k záměru None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Výuka aplikace, aby se mohly testovat změny záměru 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publikujte aplikaci, aby se Queryable z koncového bodu vyškolený model.

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Získání záměru a předpovědi entit z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. Na konec adresy URL zadejte `shift Joe Smith to IT`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. Tato promluva není stejná jako žádná z označených promluv, proto je to dobrý test a měl by se vrátit záměr `TransferEmployeeToDepartment` s extrahovanou hodnotou `Department`.

   ```json
    {
      "query": "shift Joe Smith to IT",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9775754
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9775754
        },
        {
          "intent": "None",
          "score": 0.0154493852
        }
      ],
      "entities": [
        {
          "entity": "it",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 20,
          "resolution": {
            "values": [
              "Information Technology"
            ]
          }
        }
      ]
    }
   ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Související informace

* [Výpis](luis-concept-entity-types.md#list-entity) koncepčních informací entity
* [Postup výuky](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Testování na portálu LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili nový záměr, přidali jste ukázkové promluvy a pak jste vytvořili entitu seznamu určenou k extrakci shodného textu ze záznamů. Dotaz na koncový bod po trénování a publikování aplikace identifikoval záměr a vrátil extrahovaná data.

Pokračujte v této aplikaci a [přidejte složenou entitu](luis-tutorial-composite-entity.md).

> [!div class="nextstepaction"]
> [Přidat předem vytvořenou entitu s rolí do aplikace](tutorial-entity-roles.md)

