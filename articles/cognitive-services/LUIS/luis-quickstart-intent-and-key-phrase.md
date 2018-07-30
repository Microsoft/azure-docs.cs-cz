---
title: Kurz vytvoření aplikace LUIS vracející klíčové fráze – Azure | Microsoft Docs
description: V tomto kurzu zjistíte, jak do své aplikace LUIS přidat entitu klíčové fráze a na základě jejího vracení analyzovat klíčová témata v promluvách.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/27/2018
ms.author: diberry
ms.openlocfilehash: 1cafca2433cd96c0595a6124df82856d0c491a49
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224258"
---
# <a name="tutorial-8-add-keyphrase-entity"></a>Kurz: 8. Přidání entity klíčové fráze 
V tomto kurzu použijete aplikaci, která ukazuje, jak z promluv extrahovat klíčová témata.

<!-- green checkmark -->
> [!div class="checklist"]
> * Vysvětlení entit klíčové fráze 
> * Použití aplikace LUIS v doméně lidských zdrojů 
> * Přidání entity klíčové fráze pro extrahování obsahu z promluvy
> * Trénování a publikování aplikace
> * Odeslání dotazu na koncový bod aplikace a zobrazení odpovědi JSON ze služby LUIS obsahující klíčové fráze

Pro účely tohoto článku můžete použít bezplatný účet [LUIS](luis-reference-regions.md#publishing-regions), abyste mohli vytvořit svou aplikaci LUIS.

## <a name="before-you-begin"></a>Než začnete
Pokud nemáte aplikaci pro lidské zdroje z kurzu k [jednoduchým entitám](luis-quickstart-primary-and-secondary-data.md), [naimportujte](luis-how-to-start-new-app.md#import-new-app) JSON do nové aplikace na webu služby [LUIS](luis-reference-regions.md#luis-website). Aplikaci k importování najdete v úložišti [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-simple-HumanResources.json) na Githubu.

Pokud chcete zachovat původní aplikaci pro lidské zdroje, naklonujte verzi na stránce [Settings](luis-how-to-manage-versions.md#clone-a-version) (Nastavení) a pojmenujte ji `keyphrase`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. 

## <a name="keyphrase-entity-extraction"></a>Extrakce entity klíčové fráze
Klíčová témata poskytuje předem připravená entita klíčové fráze **keyPhrase**. Tato entita vrací klíčové téma promluvy.

Následující promluvy ukazují příklady klíčových frází:

|Promluva|Hodnoty entity klíčové fráze|
|--|--|
|Bude příští rok v nabídce nový plán zdravotní péče s nižší spoluúčastí?|„nižší spoluúčast“<br>„nový plán zdravotní péče“<br>„rok“|
|Pokrývá plán zdravotní péče s vysokou spoluúčastí léčbu zraku?|„plán zdravotní péče s vysokou spoluúčastí“<br>„léčba zraku“|

Vaše klientská aplikace může tyto hodnoty používat spolu s dalšími extrahovanými entitami k rozhodování o dalším kroku v konverzaci.

## <a name="add-keyphrase-entity"></a>Přidání entity klíčové fráze 
Přidejte předem připravenou entitu klíčové fráze pro extrahování témat z promluv.

1. Ujistěte se, že je vaše aplikace pro lidské zdroje uvedená v části **Build** (Sestavení) služby LUIS. Do této části můžete přejít výběrem možnosti **Build** (Sestavit) v pravém horním řádku nabídek. 

    [ ![Snímek obrazovky aplikace LUIS se zvýrazněnou možností Build (Sestavit) na pravém horním navigačním panelu](./media/luis-quickstart-intent-and-key-phrase/hr-first-image.png)](./media/luis-quickstart-intent-and-key-phrase/hr-first-image.png#lightbox)

2. V levé nabídce vyberte **Entities** (Entity).

    [ ![Snímek obrazovky se zvýrazněnou možností Entities (Entity) na levém navigačním panelu v části Build (Sestavení)](./media/luis-quickstart-intent-and-key-phrase/hr-select-entities-button.png)](./media/luis-quickstart-intent-and-key-phrase/hr-select-entities-button.png#lightbox)

3. Vyberte **Manage prebuilt entities** (Spravovat předem připravené entity).

    [ ![Snímek obrazovky s automaticky otevíraným dialogovým oknem se seznamem entit](./media/luis-quickstart-intent-and-key-phrase/hr-manage-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-manage-prebuilt-entities.png#lightbox)

4. V automaticky otevíraném dialogovém okně vyberte **keyPhrase** (Klíčová fráze) a pak vyberte **Done** (Hotovo). 

    [ ![Snímek obrazovky s automaticky otevíraným dialogovým oknem se seznamem entit](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png#lightbox)

    <!-- TBD: asking Carol
    You won't see these entities labeled in utterances on the intents pages. 
    -->
5. V levé nabídce vyberte **Intents** (Záměry) a pak vyberte záměr **Utilities.Confirm** (Potvrzení nástrojů). Entita klíčové fráze je označená v několika promluvách. 

    [ ![Snímek obrazovky záměru Utilities.Confirm (Potvrzení nástrojů) s klíčovými frázemi označenými v promluvách](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png)](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png#lightbox)

## <a name="train-the-luis-app"></a>Trénování aplikace LUIS
Novou verzi aplikace s entitami `keyphrase` je potřeba natrénovat.  

1. V pravé horní části webu LUIS vyberte tlačítko **Train** (Trénovat).

    ![Trénování aplikace](./media/luis-quickstart-intent-and-key-phrase/train-button.png)

2. Trénování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

    ![Trénování proběhlo úspěšně](./media/luis-quickstart-intent-and-key-phrase/trained.png)

## <a name="publish-app-to-endpoint"></a>Publikování aplikace do koncového bodu

1. Na pravém horním navigačním panelu vyberte **Publish** (Publikovat).

    [![](media/luis-quickstart-intent-and-key-phrase/hr-publish-button-top-nav.png "Snímek obrazovky se stránkou Publish (Publikovat) a zvýrazněným tlačítkem Publish to production slot (Publikovat do produkčního slotu)")](media/luis-quickstart-intent-and-key-phrase/hr-publish-button-top-nav.png#lightbox)

2. Vyberte slot Production (Produkční) a tlačítko **Publish** (Publikovat).

    [![](media/luis-quickstart-intent-and-key-phrase/hr-publish-to-production-expanded.png "Snímek obrazovky se stránkou Publish (Publikovat) a zvýrazněným tlačítkem Publish to production slot (Publikovat do produkčního slotu)")](media/luis-quickstart-intent-and-key-phrase/hr-publish-to-production-expanded.png#lightbox)

3. Publikování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

## <a name="query-the-endpoint-with-an-utterance"></a>Odeslání dotazu na koncový bod s promluvou

1. V dolní části stránky **Publish** (Publikovat) vyberte odkaz na **koncový bod**. Tato akce otevře další okno prohlížeče s adresou URL koncového bodu v adresním řádku. 

    ![Snímek obrazovky se stránkou Publish (Publikovat) a zvýrazněnou adresou URL koncového bodu](media/luis-quickstart-intent-and-key-phrase/hr-endpoint-url-inline.png )

2. Na konec adresy URL zadejte `does form hrf-123456 cover the new dental benefits and medical plan`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. 

```
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

## <a name="what-has-this-luis-app-accomplished"></a>Co tato aplikace LUIS udělala?
Tato aplikace s rozpoznáváním entity klíčové fráze identifikovala záměr dotazu v přirozeném jazyce a vrátila extrahovaná data, včetně hlavního tématu. 

Váš chatbot má teď dostatek informací k určení dalšího kroku v konverzaci. 

## <a name="where-is-this-luis-data-used"></a>Kde se tato data služby LUIS používají? 
Služba LUIS s tímto požadavkem skončila. Volající aplikace, například chatbot, může převzít výsledek topScoringIntent a data entity klíčové fráze z promluvy a provést další krok. Služba LUIS neprovádí tuto programovou práci za chatbota ani nevolá aplikaci. Služba LUIS pouze určuje, co je záměrem uživatele. 

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už aplikaci LUIS nepotřebujete, odstraňte ji. V nabídce vlevo nahoře vyberte **Moje aplikace**. Vyberte tlačítko se třemi tečkami (***...***) vpravo od názvu aplikace v seznamu aplikací a potom vyberte **Delete** (Odstranit). V automaticky otevíraném dialogovém okně **Delete app?** (Odstranit aplikaci?) vyberte **Ok**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání analýzy mínění do aplikace](luis-quickstart-intent-and-sentiment-analysis.md)