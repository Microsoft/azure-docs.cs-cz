---
title: Shoda přesný text
titleSuffix: Azure Cognitive Services
description: Získejte data, která odpovídají předem definovanému seznamu položek. Každá položka v seznamu může mít synonyma, která také přesně odpovídají.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: d416cb176de6f6f79d5b7964462488216421a7a2
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2018
ms.locfileid: "53753570"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance"></a>Kurz: Získat data přesně odpovídající text z utterance

V tomto kurzu se pochopit, jak získat data entity, která odpovídá předdefinovaného seznamu položek. 

**V tomto kurzu se naučíte:**

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

Seznam entit je přesný text odpovídá slova v utterance. 

Ke každé položce v seznamu může existovat seznam synonym. Pro aplikaci lidských zdrojů oddělení společnosti lze identifikovat podle několika klíčových informací, například fakturační oddělení kódy, běžné zkratky a oficiální název. 

Musí určit oddělení, které zaměstnanec přechází k aplikaci lidských zdrojů. 

Entita seznamu je vhodná pro tento typ dat, když platí následující:

* Hodnoty dat jsou známou sadou.
* Tato sada nepřekračuje maximální [hranice](luis-boundaries.md) aplikace LUIS pro tento typ entity.
* Text promluvy se přesně shoduje se synonymem nebo názvem v kanonickém tvaru. Služba LUIS nepoužívá seznamu nad rámec textu přesné shody. Pouze seznam entit nejsou vyřešil slovního rozboru, množné číslo a další varianty konfigurací. Chcete-li spravovat změny, zvažte použití [vzor](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance) syntaxí volitelný text, který. 

## <a name="create-a-new-app"></a>Vytvoření nové aplikace

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-transfer-employees-to-a-different-department"></a>Vytvoření záměru zaměstnanci přenést k jinému oddělení

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Vyberte **Create new intent** (Vytvořit nový záměr). 

3. V automaticky otevíraném dialogovém okně zadejte `TransferEmployeeToDepartment` a pak vyberte **Done** (Hotovo). 

    ![Snímek obrazovky s dialogovým oknem pro vytvoření nového záměru](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. Přidejte do záměru ukázkové promluvy.

    |Ukázkové promluvy|
    |--|
    |Přesunout Jan Macek W. z účetního oddělení|
    |Jill Jones z přenosu pro výzkum a vývoj|
    |Oddělení 1234 má nového člena s názvem Bradstreet faktury|
    |Jackson Jan místo v inženýrství |
    |Přesunout Debra Doughtery uvnitř prodeje|
    |MV Jill Jones do IT|
    |Alice Anderson Shift na DevOps|
    |Carl Chamerlin pro Finance|
    |Steve Standish k 1234|
    |Tanner Thompson k 3456|

    [![Snímek obrazovky záměr pomocí příkladu projevy](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png "snímek záměr pomocí příkladu projevy")](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="department-list-entity"></a>Oddělení seznam entit

Teď, když **TransferEmployeeToDepartment** záměr obsahuje příklad projevy, LUIS je potřeba pochopit, co je oddělení. 

Primární, _kanonické_, název pro každou položku je název oddělení. Mezi synonyma každý název v kanonickém tvaru patří: 

|Název v kanonickém tvaru|Synonyma|
|--|--|
|Účetnictví|Acct<br>accting<br>3456|
|Vývoj pro operace|Vývoj a provoz<br>4949|
|Strojírenství|"Eng"<br>nesmí<br>4567|
|Finance|dokončení<br>2020|
|Informační technologie|IT<br>. 2323|
|Uvnitř prodeje|isale<br>insale<br>1414|
|Výzkum a vývoj|R &AMP; D<br>1234|

1. Na levém panelu vyberte **Entities** (Entity).

1. Vyberte **Create new entity** (Vytvořit novou entitu).

1. V automaticky otevíraném dialogovém okně entity zadejte `Department` jako název entity a **List** (Seznam) jako typ entity. Vyberte **Done** (Hotovo).  

    [![Snímek obrazovky vytváření nové entity automaticky otevíraná okna](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png "snímek obrazovky vytváření nové entity automaticky otevíraná okna")](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png#lightbox)

1. Na stránce entity oddělení zadejte `Accounting` jako novou hodnotu.

    [![Snímek obrazovky se zadáním hodnoty](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png "snímek obrazovky se zadáním hodnoty")](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png#lightbox)

1. Synonyma přidejte synonyma z předchozí tabulky.

    [![Snímek obrazovky zadání synonyma](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png "snímek obrazovky zadání synonyma")](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png#lightbox)

1. Pokračujte v přidávání kanonické názvy a jejich synonyma. 

## <a name="add-example-utterances-to-the-none-intent"></a>Přidání projevů příklad na hodnotu None záměru 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Trénování aplikace, takže můžete otestovat změny k příslušnému záměru 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publikování aplikace, tedy dotazovatelné z koncového bodu trénovaného modelu

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Získání předpovědi záměr a entity z koncového bodu

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

* [Seznam entit](luis-concept-entity-types.md#exact-match) koncepční informace
* [Trénování](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Testování v portálu služby LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Další postup
V tomto kurzu jste vytvořili nový záměr, přidali jste ukázkové promluvy a pak jste vytvořili entitu seznamu určenou k extrakci shodného textu ze záznamů. Po natrénování a publikování aplikace jste dotazem adresovaným koncovému bodu zjistili záměr a vrátili extrahovaná data.

Pokračovat v této aplikaci [přidání složenou entitu](luis-tutorial-composite-entity.md).

> [!div class="nextstepaction"]
> [Přidání hierarchické entity do aplikace](luis-quickstart-intent-and-hier-entity.md)

