---
title: Kurz vytvoření aplikace LUIS vracející klíčové fráze – Azure | Microsoft Docs
description: V tomto kurzu zjistíte, jak do své aplikace LUIS přidat entitu klíčové fráze a na základě jejího vracení analyzovat klíčová témata v promluvách.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 12c306b5199da5862302c28d1690b81c6e1edb0e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264611"
---
# <a name="tutorial-create-app-that-returns-keyphrases-entity-data-found-in-utterances"></a>Kurz: Vytvoření aplikace vracející data entity klíčové fráze nalezená v promluvách
V tomto kurzu vytvoříte aplikaci, která ukazuje, jak z promluv extrahovat klíčová témata.

<!-- green checkmark -->
> [!div class="checklist"]
> * Vysvětlení entit klíčové fráze 
> * Vytvoření nové aplikace LUIS pro doménu lidských zdrojů
> * Přidání záměru _None_ (Žádný) a přidání ukázkových promluv
> * Přidání entity klíčové fráze pro extrahování obsahu z promluvy
> * Trénování a publikování aplikace
> * Odeslání dotazu na koncový bod aplikace a zobrazení odpovědi JSON ze služby LUIS

Pro účely tohoto článku můžete použít bezplatný účet [LUIS][LUIS], abyste mohli vytvořit svou aplikaci LUIS.

## <a name="keyphrase-entity-extraction"></a>Extrakce entity klíčové fráze
Klíčová témata poskytuje předem připravená entita **klíčové fráze**. Tato entita vrací klíčové téma promluvy.

Následující promluvy ukazují příklady klíčových frází:

|Promluva|Hodnoty entity klíčové fráze|
|--|--|
|Bude příští rok v nabídce nový plán zdravotní péče s nižší spoluúčastí?|„nižší spoluúčast“<br>„nový plán zdravotní péče“<br>„rok“|
|Pokrývá plán zdravotní péče s vysokou spoluúčastí léčbu zraku?|„plán zdravotní péče s vysokou spoluúčastí“<br>„léčba zraku“|

Váš chatbot může při rozhodování o dalším kroku v konverzaci posuzovat kromě všech ostatních extrahovaných entit i tyto hodnoty.

## <a name="download-sample-app"></a>Stažení ukázkové aplikace
Stáhněte aplikaci pro [lidské zdroje](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/HumanResources.json) a uložte ji do souboru s příponou *.json. Tato ukázková aplikace rozpozná promluvy týkající se zaměstnaneckých výhod, organizačních diagramů a fyzických prostředků.

## <a name="create-a-new-app"></a>Vytvoření nové aplikace
1. Přihlaste se k webu [LUIS][LUIS]. Nezapomeňte se přihlásit k [oblasti][LUIS-regions], ve které potřebujete publikovat koncové body služby LUIS.

2. Na webu [LUIS][LUIS] vyberte **Import new app** (Importovat novou aplikaci) a importujte aplikaci pro lidské zdroje, kterou jste stáhli v předchozí části. 

    [![](media/luis-quickstart-intent-and-key-phrase/app-list.png "Snímek obrazovky se stránkou se seznamy aplikací")](media/luis-quickstart-intent-and-key-phrase/app-list.png#lightbox)

3. V dialogovém okně **Import new app** (Importovat novou aplikaci) zadejte název aplikace `Human Resources with Key Phrase entity`. 

    ![Obrázek dialogového okna Create new app (Vytvořit novou aplikaci)](./media/luis-quickstart-intent-and-key-phrase/import-new-app-inline.png)

    Po dokončení procesu vytváření aplikace zobrazí služba LUIS seznam záměrů.

    [![](media/luis-quickstart-intent-and-key-phrase/intents-list.png "Snímek obrazovky se stránkou se seznamy záměrů")](media/luis-quickstart-intent-and-key-phrase/intents-list.png#lightbox)

## <a name="add-keyphrase-entity"></a>Přidání entity klíčové fráze 
Přidejte předem připravenou entitu klíčové fráze pro extrahování témat z promluv.

1. V levé nabídce vyberte **Entities** (Entity).

    [ ![Snímek obrazovky se zvýrazněnou možností Entities (Entity) na levém navigačním panelu v části Build (Sestavení)](./media/luis-quickstart-intent-and-key-phrase/select-entities.png)](./media/luis-quickstart-intent-and-key-phrase/select-entities.png#lightbox)

2. Vyberte **Manage prebuilt entities** (Spravovat předem připravené entity).

    [ ![Snímek obrazovky s automaticky otevíraným dialogovým oknem se seznamem entit](./media/luis-quickstart-intent-and-key-phrase/manage-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/manage-prebuilt-entities.png#lightbox)

3. V automaticky otevíraném dialogovém okně vyberte **keyPhrase** (Klíčová fráze) a pak vyberte **Done** (Hotovo). 

    [ ![Snímek obrazovky s automaticky otevíraným dialogovým oknem se seznamem entit](./media/luis-quickstart-intent-and-key-phrase/add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/add-or-remove-prebuilt-entities.png#lightbox)

    <!-- TBD: asking Carol
    You won't see these entities labeled in utterances on the intents pages. 
    -->

## <a name="train-the-luis-app"></a>Trénování aplikace LUIS
Služba LUIS nemá informace o této změně modelu, dokud se nenatrénuje. 

1. V pravé horní části webu LUIS vyberte tlačítko **Train** (Trénovat).

    ![Snímek obrazovky se zvýrazněným tlačítkem Train (Trénovat)](./media/luis-quickstart-intent-and-key-phrase/train-button-expanded.png)

2. Trénování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

    ![Snímek obrazovky s pruhem oznamujícím úspěšné trénování ](./media/luis-quickstart-intent-and-key-phrase/trained-inline.png)

## <a name="publish-app-to-endpoint"></a>Publikování aplikace do koncového bodu

1. Na pravém horním navigačním panelu vyberte **Publish** (Publikovat).

    ![Snímek obrazovky se stránkou entity a rozbaleným tlačítkem Publish (Publikovat) ](./media/luis-quickstart-intent-and-key-phrase/publish-expanded.png)

2. Vyberte slot Production (Produkční) a tlačítko **Publish** (Publikovat).

    [![](media/luis-quickstart-intent-and-key-phrase/publish-to-production-inline.png "Snímek obrazovky se stránkou Publish (Publikovat) a zvýrazněným tlačítkem Publish to production slot (Publikovat do produkčního slotu)")](media/luis-quickstart-intent-and-key-phrase/publish-to-production-expanded.png#lightbox)

3. Publikování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

## <a name="query-the-endpoint-with-an-utterance"></a>Odeslání dotazu na koncový bod s promluvou

1. V dolní části stránky **Publish** (Publikovat) vyberte odkaz na **koncový bod**. Tato akce otevře další okno prohlížeče s adresou URL koncového bodu v adresním řádku. 

    ![Snímek obrazovky se stránkou Publish (Publikovat) a zvýrazněnou adresou URL koncového bodu](media/luis-quickstart-intent-and-key-phrase/endpoint-url-inline.png )

2. Na konec adresy URL zadejte `Is there a new medical plan with a lower deductible offered next year?`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. 

```
{
  "query": "Is there a new medical plan with a lower deductible offered next year?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.216838628
  },
  "entities": [
    {
      "entity": "lower deductible",
      "type": "builtin.keyPhrase",
      "startIndex": 35,
      "endIndex": 50
    },
    {
      "entity": "new medical plan",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 26
    },
    {
      "entity": "year",
      "type": "builtin.keyPhrase",
      "startIndex": 65,
      "endIndex": 68
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>Co tato aplikace LUIS udělala?
Tato aplikace s rozpoznáváním entity klíčové fráze identifikovala záměr dotazu v přirozeném jazyce a vrátila extrahovaná data, včetně hlavního tématu. 

Váš chatbot má teď dostatek informací k určení dalšího kroku v konverzaci. 

## <a name="where-is-this-luis-data-used"></a>Kde se tato data služby LUIS používají? 
Služba LUIS s tímto požadavkem skončila. Volající aplikace, například chatbot, může převzít výsledek topScoringIntent a data entity klíčové fráze z promluvy a provést další krok. Služba LUIS neprovádí tuto programovou práci za chatbota ani nevolá aplikaci. Služba LUIS pouze určuje, co je záměrem uživatele. 

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už aplikaci LUIS nepotřebujete, odstraňte ji. Provedete to tak, že vyberete nabídku se třemi tečkami (...) vpravo od názvu aplikace v seznamu aplikací a vyberete **Delete** (Odstranit). V automaticky otevíraném dialogovém okně **Delete app?** (Odstranit aplikaci?) vyberte **Ok**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření aplikace vracející mínění a předpověď záměru](luis-quickstart-intent-and-sentiment-analysis.md)

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
