---
title: Kontextová data s rolemi – LUIS
titleSuffix: Azure Cognitive Services
description: Najde související data na základě kontextu. Spolu například souvisí počáteční a cílová umístění pro fyzický přesun z jedné budovy a kanceláře do jiné.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 2eac05712dc7b3deceba52681195101f9bf2b40c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560008"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Kurz: Extrakce kontextově souvisejících dat z utterance

V tomto kurzu vyhledáte související části dat na základě kontextu. Například zdroj a cílové umístění pro přenos z jednoho města do druhého. Je možné, že obě části dat jsou nutné a jsou vzájemně propojené.  

Roli lze použít pro libovolný předem sestavený nebo vlastní typ entity a používá se v obou příkladech projevy a Patterns. 

**V tomto kurzu se naučíte:**

> [!div class="checklist"]
> * Vytvoření nové aplikace
> * Přidat záměr 
> * Získání informací o původu a cíli pomocí rolí
> * Trénování
> * Publikování
> * Získat záměry a role entit z koncového bodu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="related-data"></a>Související data

Tato aplikace určuje, kam má být zaměstnanec přesunut od města původu do cílového města. Používá předem vytvořenou entitu GeographyV2 k identifikaci názvů měst a používá role k určení typů umístění (zdroj a cíl) v rámci utterance.

Role by se měla použít při extrakci dat entity:

* Se vzájemně souvisí v kontextu utterance.
* Označuje jednotlivé role pomocí konkrétního výběru slova. Mezi příklady těchto slov patří from/to (z/do), leaving/headed to (odstěhovat/nastěhovat), away from/toward (z kanceláře/do kanceláře).
* Obě role jsou často ve stejném utterance, což umožňuje LUIS získat informace z tohoto častého kontextového použití.
* Musí být seskupeny a zpracovány klientskou aplikací jako jediná informace.

## <a name="create-a-new-app"></a>Vytvoření nové aplikace

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-move-employees-between-cities"></a>Vytvoření záměru pro přesun zaměstnanců mezi městy

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Vyberte **Create new intent** (Vytvořit nový záměr). 

1. V automaticky otevíraném dialogovém okně zadejte `MoveEmployeeToCity` a pak vyberte **Done** (Hotovo). 

    ![Snímek obrazovky s dialogovým oknem pro vytvoření nového záměru](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. Přidejte do záměru ukázkové promluvy.

    |Ukázkové promluvy|
    |--|
    |Přesunutí Jan W. Smith opouští Seattle s Orlandu|
    |přenos Jill Novotný z Seattlu do Cairo|
    |Umístění Jan Jacksonůvch z Tampaů, přicházejících do Brna |
    |přesunout Debra Doughtery na Tulsa z Chicago|
    |MV Jill, který opouští Cairo na Tampa|
    |Posunutí Alice Anderson na Oakland z Redmond|
    |Carl Chamerlin z San Francisco do Redmond|
    |Přenos Steve Standish z San Diegu směrem k Bellevue |
    |přezvednutí Tanner Václav z Kansas City a Shift na Chicago|

    [![Snímek obrazovky s LUIS s novým projevy v MoveEmployee záměru](./media/tutorial-entity-roles/hr-enter-utterances.png)](./media/tutorial-entity-roles/hr-enter-utterances.png#lightbox)

## <a name="add-prebuilt-entity-geographyv2"></a>Přidat předem sestavenou entitu geographyV2

Předem sestavená entita, geographyV2, extrahuje informace o poloze, včetně názvů měst. Vzhledem k tomu, že projevy mají dva názvy měst, které jsou v kontextu vzájemně související, použijte role k extrakci daného kontextu.

1. Vyberte **entity** z navigace na levé straně.

1. Vyberte **Přidat**předem vytvořenou entitu a `geo` pak vyberte na panelu hledání, abyste mohli filtrovat předem připravené entity. 

    ![Přidat předem vytvořenou entitu geographyV2 do aplikace](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)
1. Zaškrtněte políčko a vyberte **Hotovo**.
1. V seznamu **entity** vyberte **geographyV2** a otevřete novou entitu. 
1. Přidejte dvě role, `Origin`a. `Destination` 

    ![Přidání rolí k předem připravené entitě](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)
1. Vyberte **záměry** z navigace na levé straně a pak vyberte záměr **MoveEmployeeToCity** . Všimněte si, že názvy měst jsou označené předem vytvořenou entitou **geographyV2**.
1. V prvním utterance seznamu vyberte umístění původu. Zobrazí se rozevírací nabídka. V seznamu vyberte **geographyV2** a potom podle nabídky vedle vyberte **počátek**.
1. Pomocí metody z předchozího kroku označíte všechny role umístění ve všech projevy. 


## <a name="add-example-utterances-to-the-none-intent"></a>Přidat příklad projevy k záměru None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Výuka aplikace, aby se mohly testovat změny záměru 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publikujte aplikaci, aby se Queryable z koncového bodu vyškolený model.

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Získání záměru a předpovědi entit z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


1. Přejděte na konec adresy URL v panelu adresy a zadejte `Please move Carl Chamerlin from Tampa to Portland`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. Tento utterance není stejný jako žádný z označených projevy, takže je dobrým testem a měl by vracet `MoveEmployee` záměr s extrahovanou entitou.

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
    
    Je předpokládaná správná záměr a pole entity obsahuje zdrojové i cílové role ve vlastnosti odpovídajících **entit** .
    
## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Související informace

* [Koncepty entit](luis-concept-entity-types.md)
* [Koncepty rolí](luis-concept-roles.md)
* [Seznam předem připravených entit](luis-reference-prebuilt-entities.md)
* [Postup výuky](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Testování na portálu LUIS](luis-interactive-test.md)
* [Role](luis-concept-roles.md)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili nový záměr a Přidali jste příklad projevy pro kontextové naučená data o původu a cílovém umístění. Jakmile aplikaci vytrénujete a publikujete, klientská aplikace může tyto informace použít k vytvoření lístku přesunu s relevantními informacemi.

> [!div class="nextstepaction"] 
> [Další informace o přidání složené entity](luis-tutorial-composite-entity.md) 
