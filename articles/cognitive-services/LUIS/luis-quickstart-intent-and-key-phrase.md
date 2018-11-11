---
title: 'Kurz 8: Extrahování klíčových frází v LUIS'
titleSuffix: Azure Cognitive Services
description: Použijte předem připravenou entitu keyPhrase pro extrahování témat z promluv. Není potřeba označovat promluvy s předem vytvořenými entitami. Příslušná entita se rozpozná automaticky.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 126e081bae08d220b0e3921cdfec7d7f58eacea8
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50138289"
---
# <a name="tutorial-8-extract-key-phrases-of-utterance"></a>Kurz 8: Extrahování klíčových frází z promluvy
V tomto kurzu použijete předem připravenou entitu keyPhrase k extrahování témat z promluv. Není potřeba označovat promluvy s předem vytvořenými entitami. Příslušná entita se rozpozná automaticky.

Následující promluvy ukazují příklady klíčových frází:

|Promluva|Hodnoty entity klíčové fráze|
|--|--|
|Bude příští rok v nabídce nový plán zdravotní péče s nižší spoluúčastí?|„nižší spoluúčast“<br>„nový plán zdravotní péče“<br>„rok“|
|Pokrývá plán zdravotní péče s vysokou spoluúčastí léčbu zraku?|„plán zdravotní péče s vysokou spoluúčastí“<br>„léčba zraku“|

Vaše klientská aplikace může tyto hodnoty používat spolu s dalšími extrahovanými entitami k rozhodování o dalším kroku v konverzaci.

**V tomto kurzu se naučíte:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Používat existující ukázkovou aplikaci
> * Přidání entity klíčové fráze 
> * Trénování
> * Publikování
> * Zjistit záměry a entity z koncového bodu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Použití existující aplikace

Pokračujte s aplikací **HumanResources**, kterou jste vytvořili v posledním kurzu. 

Pokud aplikaci HumanResources z předchozího kurzu nemáte, postupujte takto:

1.  Stáhněte si [soubor JSON aplikace](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-simple-HumanResources.json) a uložte si ho.

2. Naimportujte soubor JSON do nové aplikace.

3. V části **Manage** (Správa) na kartě **Versions** (Verze) naklonujte verzi a pojmenujte ji `keyphrase`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. Název verze je součástí cesty URL, a proto smí obsahovat jenom znaky, které jsou platné v adresách URL.

## <a name="add-keyphrase-entity"></a>Přidání entity klíčové fráze 
Přidejte předem připravenou entitu klíčové fráze pro extrahování témat z promluv.

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. V levé nabídce vyberte **Entities** (Entity).

3. Vyberte **Add prebuilt entity** (Přidat předem připravenou entitu).

4. V automaticky otevíraném dialogovém okně vyberte **keyPhrase** (Klíčová fráze) a pak vyberte **Done** (Hotovo). 

    [ ![Snímek obrazovky s automaticky otevíraným dialogovým oknem se seznamem entit](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png#lightbox)

5. V levé nabídce vyberte **Intents** (Záměry) a pak vyberte záměr **Utilities.Confirm** (Potvrzení nástrojů). Entita klíčové fráze je označená v několika promluvách. 

    [ ![Snímek obrazovky záměru Utilities.Confirm (Potvrzení nástrojů) s klíčovými frázemi označenými v promluvách](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png)](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png#lightbox)

## <a name="train"></a>Trénování

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publikování

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Získání záměru a entit z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Na konec adresy URL zadejte `does form hrf-123456 cover the new dental benefits and medical plan`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. 
    
    ```JSON
    {
      "query": "does form hrf-123456 cover the new dental benefits and medical plan",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9300641
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9300641
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0359598845
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0141798034
        },
        {
          "intent": "MoveEmployee",
          "score": 0.0112197418
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00507669244
        },
        {
          "intent": "None",
          "score": 0.00238501839
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00202810857
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00102957746
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.0008688423
        },
        {
          "intent": "Utilities.Confirm",
          "score": 3.557994E-05
        }
      ],
      "entities": [
        {
          "entity": "hrf-123456",
          "type": "HRF-number",git 
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "new dental benefits",
          "type": "builtin.keyPhrase",
          "startIndex": 31,
          "endIndex": 49
        },
        {
          "entity": "medical plan",
          "type": "builtin.keyPhrase",
          "startIndex": 55,
          "endIndex": 66
        },
        {
          "entity": "hrf",
          "type": "builtin.keyPhrase",
          "startIndex": 10,
          "endIndex": 12
        },
        {
          "entity": "-123456",
          "type": "builtin.number",
          "startIndex": 13,
          "endIndex": 19,
          "resolution": {
            "value": "-123456"
          }
        }
      ]
    }
    ```

    Při hledání formuláře poskytl uživatel víc informací, než bylo potřeba k nalezení formuláře. Další informace se vrátí jako hodnota **builtin.keyPhrase**. Klientská aplikace může tyto další informace použít pro následnou otázku, například „Chtěli byste hovořit se zástupcem oddělení lidských zdrojů o nových benefitech v oblasti péče o chrup“, nebo může poskytnou nabídku s dalšími možnostmi včetně položky „Další informace o nových benefitech v oblasti péče o chrup nebo o zdravotním připojištění“.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přidali předem připravenou entitu keyPhrase, která rychle poskytuje klíčové fráze v promluvách bez nutnosti označování promluv. 

> [!div class="nextstepaction"]
> [Přidání analýzy mínění do aplikace](luis-quickstart-intent-and-sentiment-analysis.md)