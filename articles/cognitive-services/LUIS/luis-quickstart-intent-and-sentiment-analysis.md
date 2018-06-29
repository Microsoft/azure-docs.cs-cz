---
title: Kurz vytvoření aplikace LUIS vracející analýzu mínění – Azure | Microsoft Docs
description: V tomto kurzu zjistíte, jak do své aplikace LUIS přidat analýzu mínění umožňující analýzu pozitivních, negativních a neutrálních pocitů v promluvách.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: d000637312619fc493e2f7bad8e8edf0d8d0d94b
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265330"
---
# <a name="tutorial-create-app-that-returns-sentiment-along-with-intent-prediction"></a>Kurz: Vytvoření aplikace vracející mínění a předpověď záměru
V tomto kurzu vytvoříte aplikaci, která ukazuje, jak z promluv extrahovat pozitivní, negativní a neutrální mínění.

<!-- green checkmark -->
> [!div class="checklist"]
> * Vysvětlení hierarchických entit a kontextově naučených podřízených entit 
> * Vytvoření nové aplikace LUIS pro doménu cestování se záměrem BookFlight (Rezervovat let)
> * Přidání záměru _None_ (Žádný) a přidání ukázkových promluv
> * Přidání hierarchické entity polohy s podřízenými entitami počátku a cíle
> * Trénování a publikování aplikace
> * Odeslání dotazu na koncový bod aplikace a zobrazení odpovědi JSON ze služby LUIS obsahující hierarchické podřízené entity 

Pro účely tohoto článku potřebujete bezplatný účet [LUIS][LUIS], abyste mohli vytvořit svou aplikaci LUIS.

## <a name="sentiment-analysis"></a>Analýza mínění
Analýza mínění představuje možnost určit, jestli je promluva uživatele pozitivní, negativní nebo neutrální. 

Následující promluvy ukazují příklady mínění:

|Mínění a skóre|Promluva|
|:--|--|
|pozitivní – 0,89 |Ta kombinace polévky a salátu byla skvělá.|
|negativní – 0,07 |U večeře mi nechutnal předkrm.|

Analýza mínění je nastavení aplikace, které se vztahuje na všechny promluvy. V promluvách nemusíte hledat slova značící mínění a označovat je. Služba LUIS to udělá za vás.

## <a name="create-a-new-app"></a>Vytvoření nové aplikace
1. Přihlaste se k webu [LUIS][LUIS]. Nezapomeňte se přihlásit k [oblasti][LUIS-regions], ve které potřebujete publikovat koncové body služby LUIS.

2. Na webu [LUIS][LUIS] vyberte **Create new app** (Vytvořit novou aplikaci). 

    [![](media/luis-quickstart-intent-and-sentiment-analysis/app-list.png "Snímek obrazovky se stránkou se seznamy aplikací")](media/luis-quickstart-intent-and-sentiment-analysis/app-list.png#lightbox)

3. V dialogovém okně **Create new app** (Vytvořit novou aplikaci) zadejte název aplikace `Restaurant Reservations With Sentiment` a vyberte **Done** (Hotovo). 

    ![Obrázek dialogového okna Create new app (Vytvořit novou aplikaci)](./media/luis-quickstart-intent-and-sentiment-analysis/create-app-ddl.png)

    Po dokončení procesu vytváření aplikace zobrazí služba LUIS seznam záměrů obsahující záměr None (Žádný).

    [![](media/luis-quickstart-intent-and-sentiment-analysis/intents-list.png "Snímek obrazovky se stránkou se seznamy záměrů")](media/luis-quickstart-intent-and-sentiment-analysis/intents-list.png#lightbox)

## <a name="add-a-prebuilt-domain"></a>Přidání předem připravené domény
Přidejte předem připravenou doménu, abyste mohli rychle přidávat záměry, entity a označovat promluvy.

1. V levé nabídce vyberte **Prebuilt Domains** (Předem připravené domény).

    [ ![Snímek obrazovky s tlačítkem Prebuilt Domain (Předem připravená doména)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-button-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-button-expanded.png#lightbox)

2. U předem připravené domény **RestaurantReservation** vyberte **Add domain** (Přidat doménu). Počkejte na přidání domény.

    [ ![Snímek obrazovky se seznamem předem připravených domén](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-expanded.png#lightbox)

3. Na levém navigačním panelu vyberte **Intents** (Záměry). Tato předem připravená doména obsahuje jeden záměr.

    [ ![Snímek obrazovky se seznamem předem připravených domén a zvýrazněnou možností Intents (Záměry) na levém navigačním panelu](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-domain-added-expanded.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-domain-added-expanded.png#lightbox)

4.  Vyberte záměr **RestaurantReservation.Reserve**. 

    [ ![Snímek obrazovky se seznamem záměrů a zvýrazněným záměrem RestaurantReservation.Reserve](./media/luis-quickstart-intent-and-sentiment-analysis/select-intent.png)](./media/luis-quickstart-intent-and-sentiment-analysis/select-intent.png#lightbox)

5. Přepněte **Entities View** (Zobrazení entit) a zobrazte řadu uvedených promluv s označenými entitami specifickými pro doménu.

    [ ![Snímek obrazovky se záměrem RestaurantReservation.Reserve a zvýrazněnou možností Entities View (Zobrazení entit) přepnutou na Token View (Zobrazení tokenů)](./media/luis-quickstart-intent-and-sentiment-analysis/utterance-list-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/utterance-list-expanded.png#lightbox)

## <a name="train-the-luis-app"></a>Trénování aplikace LUIS
Služba LUIS nemá informace o změnách záměrů a entit (tedy modelu), dokud se nenatrénuje. 

1. V pravé horní části webu LUIS vyberte tlačítko **Train** (Trénovat).

    ![Snímek obrazovky se zvýrazněným tlačítkem Train (Trénovat)](./media/luis-quickstart-intent-and-sentiment-analysis/train-button-expanded.png)

2. Trénování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

    ![Snímek obrazovky s pruhem oznamujícím úspěšné trénování ](./media/luis-quickstart-intent-and-sentiment-analysis/trained-expanded.png)

## <a name="configure-app-to-include-sentiment-analysis"></a>Konfigurace aplikace pro zahrnutí analýzy mínění
Analýza mínění se povoluje na stránce **Publish** (Publikovat). 

1. Na pravém horním navigačním panelu vyberte **Publish** (Publikovat).

    ![Snímek obrazovky se stránkou záměru a rozbaleným tlačítkem Publish (Publikovat) ](./media/luis-quickstart-intent-and-sentiment-analysis/publish-expanded.png)

2. Vyberte **Enable Sentiment Analysis** (Povolit analýzu mínění).

    ![Snímek obrazovky se stránkou Publish (Publikovat) a zvýrazněnou možností Enable Sentiment Analysis (Povolit analýzu mínění) ](./media/luis-quickstart-intent-and-sentiment-analysis/enable-sentiment-expanded.png)

3. Vyberte slot Production (Produkční) a tlačítko **Publish** (Publikovat).

    [![](media/luis-quickstart-intent-and-sentiment-analysis/publish-to-production-inline.png "Snímek obrazovky se stránkou Publish (Publikovat) a zvýrazněným tlačítkem Publish to production slot (Publikovat do produkčního slotu)")](media/luis-quickstart-intent-and-sentiment-analysis/publish-to-production-expanded.png#lightbox)

4. Publikování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

## <a name="query-the-endpoint-with-an-utterance"></a>Odeslání dotazu na koncový bod s promluvou

1. V dolní části stránky **Publish** (Publikovat) vyberte odkaz na **koncový bod**. Tato akce otevře další okno prohlížeče s adresou URL koncového bodu v adresním řádku. 

    ![Snímek obrazovky se stránkou Publish (Publikovat) a zvýrazněnou adresou URL koncového bodu](media/luis-quickstart-intent-and-sentiment-analysis/endpoint-url-inline.png)

2. Na konec adresy URL zadejte `Reserve table for  10 on upper level away from kitchen`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. Tato promluva není stejná jako žádná z označených promluv, proto je to dobrý test a měl by se vrátit záměr `RestaurantReservation.Reserve` s extrahovanou analýzou mínění.

```
{
  "query": "Reserve table for 10 on upper level away from kitchen",
  "topScoringIntent": {
    "intent": "RestaurantReservation.Reserve",
    "score": 0.9926384
  },
  "intents": [
    {
      "intent": "RestaurantReservation.Reserve",
      "score": 0.9926384
    },
    {
      "intent": "None",
      "score": 0.00961109251
    }
  ],
  "entities": [],
  "sentimentAnalysis": {
    "label": "neutral",
    "score": 0.5
  }
}
```

## <a name="what-has-this-luis-app-accomplished"></a>Co tato aplikace LUIS udělala?
Tato aplikace s povolenou analýzou mínění identifikovala záměr dotazu v přirozeném jazyce a vrátila extrahovaná data, včetně celkového mínění v podobě skóre. 

Váš chatbot má teď dostatek informací k určení dalšího kroku v konverzaci. 

## <a name="where-is-this-luis-data-used"></a>Kde se tato data služby LUIS používají? 
Služba LUIS s tímto požadavkem skončila. Volající aplikace, například chatbot, může převzít výsledek topScoringIntent a data o mínění z promluvy a provést další krok. Služba LUIS neprovádí tuto programovou práci za chatbota ani nevolá aplikaci. Služba LUIS pouze určuje, co je záměrem uživatele. 

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už aplikaci LUIS nepotřebujete, odstraňte ji. Provedete to tak, že vyberete nabídku se třemi tečkami (...) vpravo od názvu aplikace v seznamu aplikací a vyberete **Delete** (Odstranit). V automaticky otevíraném dialogovém okně **Delete app?** (Odstranit aplikaci?) vyberte **Ok**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Volání rozhraní API koncového bodu služby LUIS pomocí jazyka C#](luis-get-started-cs-get-intent.md) 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
