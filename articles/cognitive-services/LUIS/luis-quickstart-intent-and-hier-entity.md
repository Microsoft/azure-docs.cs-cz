---
title: Hierarchická entita
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vyhledáte související části dat na základě kontextu. Spolu například souvisí počáteční a cílová umístění pro fyzický přesun z jedné budovy a kanceláře do jiné.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 91a9646e88adbfaf6d3c3fc0b06b341c647e773f
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2018
ms.locfileid: "53753689"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Kurz: Extrahovat kontextově souvisejících dat ze utterance

V tomto kurzu vyhledáte související části dat na základě kontextu. Například zdroj a cíl umístění pro přenos z jedno z měst na jiný. Oba druhy dat může být nutné a se vztahují k sobě navzájem.  

**V tomto kurzu se naučíte:**

> [!div class="checklist"]
> * Vytvoření nové aplikace
> * Přidat záměr 
> * Přidání hierarchické entity polohy s podřízenými entitami počátku a cíle
> * Trénování
> * Publikování
> * Zjistit záměry a entity z koncového bodu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="hierarchical-data"></a>Hierarchická data

Tato aplikace určuje, kam přesunout z původní město město cílové zaměstnance. K určení míst v rámci promluvy se používá hierarchická entita. 

Hierarchické entita je vhodný pro tento typ dat, protože dva druhy dat, podřízená umístění:

* Jsou jednoduché entity.
* Se k sobě v kontextu promluvy navzájem vztahují.
* Volba určité slovo slouží k označení každé entity. Mezi příklady těchto slov patří from/to (z/do), leaving/headed to (odstěhovat/nastěhovat), away from/toward (z kanceláře/do kanceláře).
* Obě podřízené objekty jsou často používány stejné utterance. 
* Musí být seskupeny a zpracovány klientskou aplikací jako jediná informace.

## <a name="create-a-new-app"></a>Vytvoření nové aplikace

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-move-employees-between-cities"></a>Vytvořit záměr pro přesun mezi měst, ve kterých zaměstnanci

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Vyberte **Create new intent** (Vytvořit nový záměr). 

1. V automaticky otevíraném dialogovém okně zadejte `MoveEmployeeToCity` a pak vyberte **Done** (Hotovo). 

    ![Snímek obrazovky s dialogovým oknem pro vytvoření nového záměru](./media/luis-quickstart-intent-and-hier-entity/create-new-intent-move-employee-to-city.png)

1. Přidejte do záměru ukázkové promluvy.

    |Ukázkové promluvy|
    |--|
    |Přesun Jan Macek W. opuštění Seattle směřují do Dallas|
    |přenos Jill Jones ze Seattlu do Káhira|
    |Místo Jan Jackson mimo Tampa, už pro Atlantu |
    |Přesunout Debra Doughtery Tulsa v Dallasu|
    |MV Jill Jones byste museli opustit Cairo směřují do Tampa|
    |Posunout Alice Anderson Oakland z Redmondu|
    |Carl Chamerlin ze San Franciska do Redmondu|
    |Steve Standish přenos v síti San Diegu směrem k Bellevue |
    |výtah Nováková Thompson ze Kansas Město a shift do Chicaga|

    [ ![Snímek obrazovky aplikace LUIS s novými promluvami v záměru MoveEmployee (Přesunutí zaměstnance)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)

## <a name="create-a-location-entity"></a>Vytvoření entity místa
Služba LUIS potřebuje porozumět tomu, co je místo, tím, že v promluvách označí počáteční a cílové místo. Pokud potřebujete promluvu zobrazit v zobrazení tokenů (nezpracovaných), vyberte přepínač na panelu nad promluvami s popiskem **Entities View** (Zobrazení entit). Po přepnutí přepínače bude mít tento ovládací prvek popisek **Tokens View** (Zobrazení tokenů).

Představte si následující promluvu:

```json
move John W. Smith leaving Seattle headed to Dallas
```

V promluvě jsou určená dvě místa – `Seattle` a `Dallas`. Obě jsou seskupeny jako podřízené objekty hierarchické entity `Location`, protože oba druhy dat je potřeba extrahovat z utterance k dokončení požadavku v klientské aplikaci se vztahují k sobě navzájem. 
 
Pokud je k dispozici pouze jeden podřízený prvek (počátek nebo cíl) hierarchické entity, přesto se extrahuje. Není potřeba vyhledat všechny podřízené entity, aby se extrahovala jedna nebo několik z nich. 

1. V promluvě `move John W. Smith leaving Seattle headed to Dallas` vyberte slovo `Seattle`. Zobrazí se rozevírací nabídka s textovým polem v horní části. Zadejte do textového pole název entity `Location` a pak v rozevírací nabídce vyberte **Create new entity** (Vytvořit novou entitu). 

    [![Snímek obrazovky vytváření nové entity na stránce záměru](media/luis-quickstart-intent-and-hier-entity/create-location-hierarchical-entity-from-example-utterance.png "snímek obrazovky vytváření nové entity na stránce záměru")](media/luis-quickstart-intent-and-hier-entity/create-location-hierarchical-entity-from-example-utterance.png#lightbox)

1. V automaticky otevíraném okně vyberte typ entity **Hierarchical** (Hierarchická) s podřízenými entitami `Origin` a `Destination`. Vyberte **Done** (Hotovo).

    ![Snímek obrazovky zobrazení dialogu Vytvoření entity pro novou entitu umístění](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "snímek obrazovky zobrazení dialogu Vytvoření entity pro nové entity v umístění")

1. Popisek pro `Seattle` je označený jako `Location`, protože služba LUIS neví, jestli je toto slovo počátkem, cílem, nebo ani jedním. Vyberte `Seattle`a pak vyberte **umístění**, postupujte podle v nabídce vpravo a vyberte `Origin`.

    [![Snímek obrazovky entity označování zobrazení dialogu změnit umístění podřízené entity](media/luis-quickstart-intent-and-hier-entity/choose-hierarchical-child-entity-from-example-utterance.png "snímek entity označování zobrazení dialogu změnit umístění podřízené entity")](media/luis-quickstart-intent-and-hier-entity/choose-hierarchical-child-entity-from-example-utterance.png#lightbox)

1. Popisek v jiných umístěních v všechny projevy. Když jsou označeny všechna místa, začít projevy, aby vypadala jako vzor. 

    [![Snímek obrazovky umístění entity označené v projevy](media/luis-quickstart-intent-and-hier-entity/all-intents-marked-with-origin-and-destination-location.png "entity umístění snímek obrazovky s popisem projevy")](media/luis-quickstart-intent-and-hier-entity/all-intents-marked-with-origin-and-destination-location.png#lightbox)

    Červené podtržení znamená, že služba LUIS není jistí entity. Školení řeší to. 

## <a name="add-example-utterances-to-the-none-intent"></a>Přidání projevů příklad na hodnotu None záměru 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Trénování aplikace, takže můžete otestovat změny k příslušnému záměru 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publikování aplikace, tedy dotazovatelné z koncového bodu trénovaného modelu

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Získání předpovědi záměr a entity z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


1. Přejděte na konec adresy URL v panelu adresy a zadejte `Please move Carl Chamerlin from Tampa to Portland`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. Tato promluva není stejná jako žádná z označených promluv, proto je to dobrý test a měl by se vrátit záměr `MoveEmployee` s extrahovanou hierarchickou entitou.

    ```json
    {
      "query": "Please move Carl Chamerlin from Tampa to Portland",
      "topScoringIntent": {
        "intent": "MoveEmployeeToCity",
        "score": 0.979823351
      },
      "intents": [
        {
          "intent": "MoveEmployeeToCity",
          "score": 0.979823351
        },
        {
          "intent": "None",
          "score": 0.0156363435
        }
      ],
      "entities": [
        {
          "entity": "portland",
          "type": "Location::Destination",
          "startIndex": 41,
          "endIndex": 48,
          "score": 0.6044041
        },
        {
          "entity": "tampa",
          "type": "Location::Origin",
          "startIndex": 32,
          "endIndex": 36,
          "score": 0.739491045
        }
      ]
    }
    ```
    
    Shromážděno správné záměr a pole entity má původ a cílové hodnoty z odpovídajících **entity** vlastnost.
    
## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Související informace

* [Hierarchické entity](luis-concept-entity-types.md) koncepční informace
* [Trénování](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Testování v portálu služby LUIS](luis-interactive-test.md)
* [Role a hierarchické entity](luis-concept-roles.md#roles-versus-hierarchical-entities)
* [Zlepšení predikcí se vzorci](luis-concept-patterns.md)

## <a name="next-steps"></a>Další postup

V tomto kurzu nové záměr a přidá příklad projevy kontextově zjištěná data míst původu a cíle. Jakmile aplikaci vytrénujete a publikujete, klientská aplikace může tyto informace použít k vytvoření lístku přesunu s relevantními informacemi.

> [!div class="nextstepaction"] 
> [Další informace o přidání složené entity](luis-tutorial-composite-entity.md) 
