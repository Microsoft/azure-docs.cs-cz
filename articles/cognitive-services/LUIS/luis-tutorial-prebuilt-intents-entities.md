---
title: Předem připravené záměry a entity – LUIS
titleSuffix: Azure Cognitive Services
description: V tomto kurzu přidáte do aplikace předem připravené záměry a entity, abyste rychle získali předpověď záměrů a extrakci dat. Není potřeba označovat promluvy s předem vytvořenými entitami. Příslušná entita se rozpozná automaticky.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: 4697bad15a374bed0de08b7cabc5aceaad7f1259
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876705"
---
# <a name="tutorial-identify-common-intents-and-entities"></a>Kurz: Identifikace běžných záměrů a entit

V tomto kurzu přidejte předem připravené záměry a entity do aplikace kurz pro lidské zdroje, abyste mohli rychle získat předpověď záměrů a extrakci dat. Nemusíte označit žádné projevy s předem vytvořenými entitami, protože entita se detekuje automaticky.

Předem připravené modely (domény, záměry a entity) vám pomůžou rychle sestavit model.

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


## <a name="add-prebuilt-intents-to-help-with-common-user-intentions"></a>Přidejte předem připravené záměry, které vám pomůžou s běžnými záměry uživatelů

Služba LUIS poskytuje několik předem připravených záměrů, které vám pomůžou s běžnými záměry uživatelů.  

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Vyberte **Add prebuilt domain intent** (Přidat předem připravený záměr domény). 

1. Vyhledejte `Utilities`. 

1. Vyberte všechny záměry a vyberte **Hotovo**. Tyto záměry jsou užitečné k určení, kde v konverzaci je uživatel a co žádají. 

## <a name="add-prebuilt-entities-to-help-with-common-data-type-extraction"></a>Přidat předem připravené entity, které vám pomůžou s extrakcí běžných datových typů

Služba LUIS poskytuje několik předem připravených entit pro extrakci běžných dat. 

1. V levé navigační nabídce vyberte **Entities** (Entity).

1. Vyberte tlačítko **Add prebuilt entity** (Přidat předem připravenou entitu).

1. V seznamu předem vytvořených entit vyberte následující entity a potom vyberte Hotovo:

   * **[PersonName](luis-reference-prebuilt-person.md)** 
   * **[GeographyV2](luis-reference-prebuilt-geographyV2.md)**

     ![Snímek obrazovky s číslem vybraným v dialogu předem připravené entity](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

     Tyto entity vám pomůžou přidat název a umístit rozpoznávání do klientské aplikace.

## <a name="add-example-utterances-to-the-none-intent"></a>Přidat příklad projevy k záměru None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Výuka aplikace, aby se mohly testovat změny záměru 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publikujte aplikaci, aby se Queryable z koncového bodu vyškolený model.

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Získání záměru a předpovědi entit z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Přejděte v adresním řádku prohlížeče na konec adresy URL a zadejte `I want to cancel my trip to Seattle to see Bob Smith`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. 

    ```json
    {
      "query": "I want to cancel my trip to Seattle to see Bob Smith.",
      "topScoringIntent": {
        "intent": "Utilities.ReadAloud",
        "score": 0.100361854
      },
      "intents": [
        {
          "intent": "Utilities.ReadAloud",
          "score": 0.100361854
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.08102781
        },
        {
          "intent": "Utilities.SelectNone",
          "score": 0.0398852825
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.0277276486
        },
        {
          "intent": "Utilities.SelectItem",
          "score": 0.0220712926
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0145813478
        },
        {
          "intent": "None",
          "score": 0.012434179
        },
        {
          "intent": "Utilities.Escalate",
          "score": 0.0122632384
        },
        {
          "intent": "Utilities.ShowNext",
          "score": 0.008534077
        },
        {
          "intent": "Utilities.ShowPrevious",
          "score": 0.00547111453
        },
        {
          "intent": "Utilities.SelectAny",
          "score": 0.00152912608
        },
        {
          "intent": "Utilities.Repeat",
          "score": 0.0005556819
        },
        {
          "intent": "Utilities.FinishTask",
          "score": 0.000169488427
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.000149565312
        },
        {
          "intent": "Utilities.GoBack",
          "score": 0.000141017343
        },
        {
          "intent": "Utilities.Reject",
          "score": 6.27324E-06
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

    Výsledkem bylo předpověď těchto nástrojů. zrušení záměru s 80% jistotou a extrahování dat města a jména osoby. 


## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Související informace

Další informace o předem sestavených modelech:

* [Předem připravené domény](luis-reference-prebuilt-domains.md): Jedná se o běžné domény, které omezují celkový vývoj aplikací Luis.
* Předem sestavené záměry: Jedná se o individuální záměry běžných domén. Místo přidávání celé domény můžete přidat záměry individuálně.
* [Předem připravené entity](luis-prebuilt-entities.md): Jedná se o běžné datové typy, které jsou užitečné pro většinu aplikací Luis.

Další informace o práci s aplikací LUIS:

* [Postup výuky](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Testování na portálu LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Další kroky

Díky přidávání předem připravených záměrů a entit může klientská aplikace určovat běžné záměry uživatelů a extrahovat běžné datové typy.  

> [!div class="nextstepaction"]
> [Přidání entity regulárního výrazu do aplikace](luis-quickstart-intents-regex-entity.md)

