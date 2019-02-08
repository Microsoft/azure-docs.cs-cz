---
title: Předem připravené záměry a entity
titleSuffix: Azure Cognitive Services
description: V tomto kurzu přidáte do aplikace rychle získat záměru extrakce předpovědi a data předem připravených záměry a entity. Není potřeba označovat promluvy s předem vytvořenými entitami. Příslušná entita se rozpozná automaticky.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 346e7ac9a76f4ce4a87565ffc77032b095b2975c
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55877996"
---
# <a name="tutorial-identify-common-intents-and-entities"></a>Kurz: Identifikovat běžné záměry a entity

V tomto kurzu přidáte do aplikace pro kurz lidských zdrojů získáte rychlé přehled záměru extrakce předpovědi a data předem připravených záměry a entity. Nepotřebujete označte jakékoli projevy s předem vytvořenými entitami, protože entita je detekován automaticky.

Předem připravené modely (domén, záměry a entity) pomáhají rychle vytvářet model.

**V tomto kurzu se naučíte:**

> [!div class="checklist"]
> * Vytvoření nové aplikace
> * Přidání předem připravených záměrů 
> * Přidání předem připravených entit 
> * Trénování 
> * Publikování 
> * Zjistit záměry a entity z koncového bodu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="create-a-new-app"></a>Vytvoření nové aplikace

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]


## <a name="add-prebuilt-intents-to-help-with-common-user-intentions"></a>Přidání předem připravených záměrů, abychom vám pomohli s common záměry uživatele

Služba LUIS poskytuje několik předem připravených záměrů, které vám pomůžou s běžnými záměry uživatelů.  

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Vyberte **Add prebuilt domain intent** (Přidat předem připravený záměr domény). 

1. Vyhledejte `Utilities`. 

    [ ![Snímek obrazovky s dialogovým oknem předem připravených záměrů s textem Utilities (Nástroje) ve vyhledávacím poli](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

1. Vyberte následující záměry a pak vyberte **Done** (Hotovo): 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.StartOver
    * Utilities.Stop

    Tyto příkazy jsou užitečná k určení toho, pokud v konverzaci, uživatel je a co se žádáme provést. 


## <a name="add-prebuilt-entities-to-help-with-common-data-type-extraction"></a>Přidat předem připravených entit, abychom vám pomohli s společný typ extrakce

Služba LUIS poskytuje několik předem připravených entit pro extrakci běžných dat. 

1. V levé navigační nabídce vyberte **Entities** (Entity).

1. Vyberte tlačítko **Add prebuilt entity** (Přidat předem připravenou entitu).

1. Vyberte ze seznamu předem připravených entit následující entity a pak vyberte **provádí**:

    * **[PersonName](luis-reference-prebuilt-person.md)** 
    * **[GeographyV2](luis-reference-prebuilt-geographyV2.md)**

    ![Snímek obrazovky s vybranou možností number (číslo) v dialogovém okně s předem připravenými entitami](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

    Tyto entity můžete přidat název a místo rozpoznávání do klientské aplikace.

## <a name="add-example-utterances-to-the-none-intent"></a>Přidání projevů příklad na hodnotu None záměru 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Trénování aplikace, takže můžete otestovat změny k příslušnému záměru 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publikování aplikace, tedy dotazovatelné z koncového bodu trénovaného modelu

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Získání předpovědi záměr a entity z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Přejděte v adresním řádku prohlížeče na konec adresy URL a zadejte `I want to cancel my trip to Seattle to see Bob Smith`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. 

    ```json
    {
      "query": "I want to cancel my trip to Seattle to see Bob Smith",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.807676256
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.807676256
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0487322025
        },
        {
          "intent": "Utilities.Help",
          "score": 0.0208660364
        },
        {
          "intent": "None",
          "score": 0.008789532
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.006929268
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00136293867
        }
      ],
      "entities": [
        {
          "entity": "seattle",
          "type": "builtin.geographyV2.city",
          "startIndex": 28,
          "endIndex": 34
        },
        {
          "entity": "bob smith",
          "type": "builtin.personName",
          "startIndex": 43,
          "endIndex": 51
        }
      ]
    }
    ```

    Výsledek předpovědět záměr Utilities.Cancel bez obav 80 % a extrahovaná data název města a osoby. 


## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Související informace

Další informace o předem připravené modely:

* [Předem připravených domén](luis-reference-prebuilt-domains.md): Toto jsou běžné domén, které snížit celkový vytváření aplikace LUIS
* Předem připravené záměry: Jedná se o jednotlivých záměry běžné domén. Můžete přidat záměry jednotlivě nepřidávat v celé doméně.
* [Předem připravených entit](luis-prebuilt-entities.md): Toto jsou běžné typy dat užitečné pro většinu aplikací LUIS.

Další informace o práci s vaší aplikací LUIS:

* [Trénování](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Testování v portálu služby LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Další postup

Díky přidávání předem připravených záměrů a entit může klientská aplikace určovat běžné záměry uživatelů a extrahovat běžné datové typy.  

> [!div class="nextstepaction"]
> [Přidání entity regulárního výrazu do aplikace](luis-quickstart-intents-regex-entity.md)

