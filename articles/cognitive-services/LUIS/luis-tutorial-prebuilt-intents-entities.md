---
title: 'Kurz: předem sestavené záměry a entity – LUIS'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu přidáte do aplikace předem připravené záměry a entity, abyste rychle získali předpověď záměrů a extrakci dat. Není potřeba označovat promluvy s předem vytvořenými entitami. Příslušná entita se rozpozná automaticky.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: diberry
ms.openlocfilehash: cf0ef1095946b1c8e9479b3cd47fe403baeed7d1
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757126"
---
# <a name="tutorial-identify-common-intents-and-entities"></a>Kurz: identifikace běžných záměrů a entit

V tomto kurzu přidejte předem připravené záměry a entity do aplikace kurz pro lidské zdroje, abyste mohli rychle získat předpověď záměrů a extrakci dat. Nemusíte označit žádné projevy s předem vytvořenými entitami, protože entita se detekuje automaticky.

Předem připravené modely (domény, záměry a entity) vám pomůžou rychle sestavit model.

**Co se v tomto kurzu naučíte:**

> [!div class="checklist"]
> * Vytvoření nové aplikace
> * Přidání předem připravených záměrů 
> * Přidání předem připravených entit 
> * Trénování 
> * Publikování 
> * Získat záměry a entity z koncového bodu

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

1. V seznamu předem vytvořených entit vyberte následující entity a potom vyberte **Hotovo**:

   * **[GeographyV2](luis-reference-prebuilt-geographyV2.md)**

     Tato entita vám pomůže přidat do klientské aplikace rozpoznávání místa.

## <a name="add-example-utterances-to-the-none-intent"></a>Přidat příklad projevy k záměru None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Výuka aplikace, aby se mohly testovat změny záměru 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publikujte aplikaci, aby se Queryable z koncového bodu vyškolený model.

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Získání záměru a předpovědi entit z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Přejděte v adresním řádku prohlížeče na konec adresy URL a zadejte `I want to cancel my trip to Seattle`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. 

    ```json
    {
      "query": "I want to cancel my trip to Seattle",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.1055009
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.1055009
        },
        {
          "intent": "Utilities.SelectItem",
          "score": 0.02659072
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.0253379084
        },
        {
          "intent": "Utilities.ReadAloud",
          "score": 0.02528683
        },
        {
          "intent": "Utilities.SelectNone",
          "score": 0.02434013
        },
        {
          "intent": "Utilities.Escalate",
          "score": 0.009161292
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006861785
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00633448
        },
        {
          "intent": "Utilities.ShowNext",
          "score": 0.0053827134
        },
        {
          "intent": "None",
          "score": 0.002602003
        },
        {
          "intent": "Utilities.ShowPrevious",
          "score": 0.001797354
        },
        {
          "intent": "Utilities.SelectAny",
          "score": 0.000831930141
        },
        {
          "intent": "Utilities.Repeat",
          "score": 0.0006924066
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.000606057351
        },
        {
          "intent": "Utilities.GoBack",
          "score": 0.000276725681
        },
        {
          "intent": "Utilities.FinishTask",
          "score": 0.000267822179
        },
        {
          "intent": "Utilities.Reject",
          "score": 3.21784828E-05
        }
      ],
      "entities": [
        {
          "entity": "seattle",
          "type": "builtin.geographyV2.city",
          "startIndex": 28,
          "endIndex": 34
        }
      ]
    }
    ```

    Výsledkem bylo předpověď těchto nástrojů. zrušení záměru s 80% jistotou a extrahování dat města. 


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

