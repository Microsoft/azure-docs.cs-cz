---
title: Předem připravené záměry a entity
titleSuffix: Azure Cognitive Services
description: Přidejte do ukázkové aplikace pro lidské zdroje předem připravené záměry a entity a získejte tak rychle předpověď záměru a extrakci dat. Není potřeba označovat promluvy s předem vytvořenými entitami. Příslušná entita se rozpozná automaticky.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 20f5fb918c836bda9bee19feec3259cfd4858875
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53722410"
---
# <a name="tutorial-2-identify-common-intents-and-entities"></a>Kurz 2: Identifikovat běžné záměry a entity
V tomto kurzu se upravíte aplikaci pro lidské zdroje. Přidejte do ukázkové aplikace pro lidské zdroje předem připravené záměry a entity a získejte tak rychle předpověď záměru a extrakci dat. Není potřeba označovat promluvy s předem vytvořenými entitami, protože příslušná entita se rozpozná automaticky.

Předem připravené modely běžných domén předmětů a datových typů vám pomůžou rychle vytvořit model a také poskytují ukázku, jak model vypadá. 

**Co se v tomto kurzu naučíte:**

> [!div class="checklist"]
> * Používat existující ukázkovou aplikaci
> * Přidání předem připravených záměrů 
> * Přidání předem připravených entit 
> * Trénování 
> * Publikování 
> * Zjistit záměry a entity z koncového bodu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Použití existující aplikace
Pokračujte s aplikací **HumanResources**, kterou jste vytvořili v posledním kurzu. 

Pokud aplikaci HumanResources z předchozího kurzu nemáte, postupujte takto:

1.  Stáhněte si [soubor JSON aplikace](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-intent-only-HumanResources.json) a uložte si ho.

2. Naimportujte soubor JSON do nové aplikace.

3. V části **Manage** (Správa) na kartě **Versions** (Verze) naklonujte verzi a pojmenujte ji `prebuilts`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. Název verze je součástí cesty URL, a proto smí obsahovat jenom znaky, které jsou platné v adresách URL. 

## <a name="add-prebuilt-intents"></a>Přidání předem připravených záměrů
Služba LUIS poskytuje několik předem připravených záměrů, které vám pomůžou s běžnými záměry uživatelů.  

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Vyberte **Add prebuilt intent** (Přidat předem připravený záměr). 

3. Vyhledejte `Utilities`. 

    [ ![Snímek obrazovky s dialogovým oknem předem připravených záměrů s textem Utilities (Nástroje) ve vyhledávacím poli](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. Vyberte následující záměry a pak vyberte **Done** (Hotovo): 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.StartOver
    * Utilities.Stop


## <a name="add-prebuilt-entities"></a>Přidání předem připravených entit
Služba LUIS poskytuje několik předem připravených entit pro extrakci běžných dat. 

1. V levé navigační nabídce vyberte **Entities** (Entity).

2. Vyberte tlačítko **Manage prebuilt entity** (Spravovat předem připravenou entitu).

3. V seznamu předem připravených entit vyberte **number** (číslo) a **datetimeV2** a pak vyberte **Done** (Hotovo).

    ![Snímek obrazovky s vybranou možností number (číslo) v dialogovém okně s předem připravenými entitami](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train"></a>Trénování

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publikování

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Získání záměru a entit z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Přejděte v adresním řádku prohlížeče na konec adresy URL a zadejte `I want to cancel on March 3`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. 

    ```json
    {
      "query": "I want to cancel on March 3",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.7818295
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.7818295
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0237864349
        },
        {
          "intent": "GetJobInformation",
          "score": 0.017576348
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0130122062
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006731322
        },
        {
          "intent": "None",
          "score": 0.00524190161
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.004912514
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00092950504
        }
      ],
      "entities": [
        {
          "entity": "march 3",
          "type": "builtin.datetimeV2.date",
          "startIndex": 20,
          "endIndex": 26,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2018-03-03"
              },
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2019-03-03"
              }
            ]
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 26,
          "endIndex": 26,
          "resolution": {
            "value": "3"
          }
        }
      ]
    }
    ```

    Výsledek předpověděl záměr Utilities.Cancel a extrahoval datum March 3 (3. března) a číslo 3. 

    Pro entitu March 3 (3. března) existují dvě hodnoty, protože promluva neuvádí, jestli se jedná o minulé nebo budoucí datum. Je na klientské aplikaci, aby vytvořila předpoklad nebo v případě potřeby požádala o další informace. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další postup

Díky přidávání předem připravených záměrů a entit může klientská aplikace určovat běžné záměry uživatelů a extrahovat běžné datové typy. 

> [!div class="nextstepaction"]
> [Přidání entity regulárního výrazu do aplikace](luis-quickstart-intents-regex-entity.md)

