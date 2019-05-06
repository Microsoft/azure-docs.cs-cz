---
title: Kontextové údaje s rolemi – Language Understanding
titleSuffix: Azure Cognitive Services
description: Vyhledejte související data na základě kontextu. Spolu například souvisí počáteční a cílová umístění pro fyzický přesun z jedné budovy a kanceláře do jiné.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: diberry
ms.openlocfilehash: 2e2b2c39cf9039c78a610e9b28065b972484123e
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65069273"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Kurz: Extrahovat kontextově souvisejících dat ze utterance

V tomto kurzu vyhledáte související části dat na základě kontextu. Například zdroj a cíl umístění pro přenos z jedno z měst na jiný. Oba druhy dat může být nutné a se vztahují k sobě navzájem.  

Roli můžete používat s libovolného typu předem připravená nebo vlastní entity a použít v příkladu projevy a vzory. 

**V tomto kurzu se naučíte:**

> [!div class="checklist"]
> * Vytvoření nové aplikace
> * Přidat záměr 
> * Získat informace o původu a cíle pomocí rolí
> * Trénování
> * Publikování
> * Získejte záměry a entity role z koncového bodu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="related-data"></a>Související data

Tato aplikace určuje, kam přesunout z původní město město cílové zaměstnance. Použije GeographyV2 předem připravených entit k identifikaci názvy měst a používá role k určení umístění typů (zdroj a cíl) v rámci utterance.

Role by měl být použít, pokud data entity k extrakci:

* Má vztah k sobě navzájem v kontextu utterance.
* Volba určité slovo se používá k označení jednotlivé role. Mezi příklady těchto slov patří from/to (z/do), leaving/headed to (odstěhovat/nastěhovat), away from/toward (z kanceláře/do kanceláře).
* Obě role jsou ve stejné utterance povolení LUIS Učte se od použití těchto častých kontextové často.
* Musí být seskupeny a zpracovány klientskou aplikací jako jediná informace.

## <a name="create-a-new-app"></a>Vytvoření nové aplikace

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-move-employees-between-cities"></a>Vytvořit záměr pro přesun mezi měst, ve kterých zaměstnanci

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Vyberte **Create new intent** (Vytvořit nový záměr). 

1. V automaticky otevíraném dialogovém okně zadejte `MoveEmployeeToCity` a pak vyberte **Done** (Hotovo). 

    ![Snímek obrazovky s dialogovým oknem pro vytvoření nového záměru](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. Přidejte do záměru ukázkové promluvy.

    |Ukázkové promluvy|
    |--|
    |Přesun Jan Macek W. opuštění Seattle směřují do Orlando|
    |přenos Jill Jones ze Seattlu do Káhira|
    |Místo Jan Jackson mimo Tampa, už pro Atlantu |
    |Přesunutí Debra Doughtery Tulsa z Chicaga|
    |MV Jill Jones byste museli opustit Cairo směřují do Tampa|
    |Posunout Alice Anderson Oakland z Redmondu|
    |Carl Chamerlin ze San Franciska do Redmondu|
    |Steve Standish přenos v síti San Diegu směrem k Bellevue |
    |výtah Nováková Thompson ze Kansas Město a shift do Chicaga|

    [![Snímek obrazovky služby LUIS s novou projevy v MoveEmployee záměr](./media/tutorial-entity-roles/hr-enter-utterances.png)](./media/tutorial-entity-roles/hr-enter-utterances.png#lightbox)

## <a name="add-prebuilt-entity-geographyv2"></a>Přidat geographyV2 předem připravených entit

Předem připravených entit geographyV2, extrahuje informace o poloze, včetně názvy měst. Protože projevy mít dva názvy měst, vztahující se k sobě navzájem v kontextu, role můžete použijte k extrahování daného kontextu.

1. Vyberte **entity** z levé navigace.

1. Vyberte **přidat předem připravených entit**a pak vyberte `geo` na panelu hledání k filtrování předem připravených entit. 

    ![Přidat geographyV2 předem připravených entit do aplikace](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)
1. Zaškrtněte políčko a vyberte **provádí**.
1. V **entity** seznamu, vyberte **geographyV2** otevřete novou entitu. 
1. Přidat dvě role, `Origin`, a `Destination`. 

    ![Přidání rolí na předem připravených entit](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)
1. Vyberte **záměry** z levé navigace, vyberte **MoveEmployeeToCity** záměr. Všimněte si, že názvy měst jsou označeny pomocí předem připravených entit **geogrpahyV2**.
1. V první utterance seznamu vyberte umístění, původu. Zobrazí se rozevírací nabídka. Vyberte **geographyV2** v seznamu, postupujte podle nabídce napříč vyberte **původu**.

    [![Snímek obrazovky s označením město jako původní umístění](media/tutorial-entity-roles/tag-origin-city-with-role.png "snímek obrazovky s označením město jako původní umístění")](media/tutorial-entity-roles/tag-origin-city-with-role.png#lightbox)

1. K označení všechny role, umístění v všechny projevy, použijte metodu z předchozího kroku. 

    [![Snímek obrazovky umístění entity označené v projevy](media/tutorial-entity-roles/all-locations-marked-with-roles.png "entity umístění snímek obrazovky s popisem projevy")](media/tutorial-entity-roles/all-locations-marked-with-roles.png#lightbox)

## <a name="add-example-utterances-to-the-none-intent"></a>Přidání projevů příklad na hodnotu None záměru 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Trénování aplikace, takže můžete otestovat změny k příslušnému záměru 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publikování aplikace, tedy dotazovatelné z koncového bodu trénovaného modelu

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Získání předpovědi záměr a entity z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


1. Přejděte na konec adresy URL v panelu adresy a zadejte `Please move Carl Chamerlin from Tampa to Portland`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. Tato utterance není stejný jako některý z označených projevy tak, aby je dobrá a by měl vrátit `MoveEmployee` záměru s entitou extrahovat.

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
          "entity": "geographyV2",
          "role": "Destination",
          "startIndex": 41,
          "endIndex": 48,
          "score": 0.6044041
        },
        {
          "entity": "geographyV2",
          "role": "Origin",
          "startIndex": 32,
          "endIndex": 36,
          "score": 0.739491045
        }
      ]
    }
    ```
    
    Shromážděno správné záměr a pole entity má původ a cílové role z odpovídajících **entity** vlastnost.
    
## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Související informace

* [Koncepty entity](luis-concept-entity-types.md)
* [Koncepty role](luis-concept-roles.md)
* [Seznam předem připravených entit](luis-reference-prebuilt-entities.md)
* [Trénování](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Testování v portálu služby LUIS](luis-interactive-test.md)
* [Role](luis-concept-roles.md)

## <a name="next-steps"></a>Další postup

V tomto kurzu nové záměr a přidá příklad projevy kontextově zjištěná data míst původu a cíle. Jakmile aplikaci vytrénujete a publikujete, klientská aplikace může tyto informace použít k vytvoření lístku přesunu s relevantními informacemi.

> [!div class="nextstepaction"] 
> [Další informace o přidání složené entity](luis-tutorial-composite-entity.md) 
