---
title: Kurz vytvoření aplikace LUIS pro získání dat o poloze – Azure | Microsoft Docs
description: V tomto kurzu zjistíte, jak vytvořit jednoduchou aplikaci LUIS využívající záměry a hierarchickou entitu k extrakci dat.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: v-geberr
ms.openlocfilehash: 2547407126943161ba604fa2f5e80b9186cae57e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266494"
---
# <a name="tutorial-create-app-that-uses-hierarchical-entity"></a>Kurz: Vytvoření aplikace využívající hierarchickou entitu
V tomto kurzu vytvoříte aplikaci, která ukazuje vyhledání souvisejících částí dat na základě kontextu. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Vysvětlení hierarchických entit a kontextově naučených podřízených entit 
> * Vytvoření nové aplikace LUIS pro doménu cestování se záměrem BookFlight (Rezervovat let)
> * Přidání záměru _None_ (Žádný) a přidání ukázkových promluv
> * Přidání hierarchické entity polohy s podřízenými entitami počátku a cíle
> * Trénování a publikování aplikace
> * Odeslání dotazu na koncový bod aplikace a zobrazení odpovědi JSON ze služby LUIS obsahující hierarchické podřízené entity 

Pro účely tohoto článku potřebujete bezplatný účet [LUIS][LUIS], abyste mohli vytvořit svou aplikaci LUIS.

## <a name="purpose-of-the-app-with-this-entity"></a>Účel aplikace s touto entitou
Tato aplikace určuje, jestli chce uživatel zarezervovat let. Pomocí hierarchické entity určuje z textu uživatele polohy, počáteční město a cílové město. 

Hierarchická entita je vhodná pro tento typ dat, protože obě části dat:

* Jsou polohy, obvykle vyjádřené kódem města nebo letiště.
* Obvykle používají jednoznačnou volbu slov umožňující určit, co je počátek a co je cíl. Mezi tato slova patří: do, směřující do, z, odlet.
* Obě polohy se často vyskytují ve stejné promluvě. 

Účelem **hierarchické** entity je na základě kontextu vyhledat data související s promluvou. Představte si následující promluvu:

```JSON
1 ticket from Seattle to Cairo`
```

V promluvě jsou uvedené dvě polohy. Jedna představuje počáteční město Seattle a druhá představuje cílové město Káhira. Obě tato města jsou důležitá pro rezervaci letu. I když by se dala najít pomocí jednoduchých entit, navzájem spolu souvisí a často se nacházejí ve stejné promluvě. Proto má smysl je obě seskupit jako podřízené prvky hierarchické entity **Location** (Poloha). 

Vzhledem k tomu, že se jedná o strojově naučené entity, aplikace potřebuje ukázkové promluvy s označeným počátečním a cílovým městem. Díky tomu se služba LUIS naučí, v jakých částech promluv se entity nacházejí, jak jsou dlouhé a jaká slova se kolem nich vyskytují. 

## <a name="app-intents"></a>Záměry aplikace
Záměry představují kategorie toho, co uživatel chce. Tato aplikace obsahuje dva záměry: BookFlight (Rezervovat let) a None (Žádný). Záměr [None](luis-concept-intent.md#none-intent-is-fallback-for-app) (Žádný) se používá účelně k označení všeho mimo aplikaci.  

## <a name="hierarchical-entity-is-contextually-learned"></a>Hierarchická entita je kontextově naučená 
Účelem této entity je vyhledat v promluvě části textu a uspořádat je do kategorií. [Hierarchická](luis-concept-entity-types.md) entita je entita nadřazeného a podřízeného prvku na základě kontextu použití. Člověk dokáže určit počáteční a cílové město v promluvě na základě použití slov `to` (do) a `from` (z). Tady jsou příklady kontextového použití.  

Pro tuto cestovní aplikaci služba LUIS extrahuje počáteční a cílovou polohu takovým způsobem, aby bylo možné vytvořit a vyplnit standardní rezervaci. Služba LUIS umožňuje v promluvách používat variace, zkratky a hovorové výrazy. 

Mezi jednoduché ukázkové promluvy od uživatelů patří:

```
Book a flight to London for next Monday
2 tickets from Dallas to Dublin this weekend
Researve a seat from New York to Paris on the first of April
```

Mezi zkrácené nebo hovorové verze promluv patří:

```
LHR tomorrow
SEA to NYC next Monday
LA to MCO spring break
```
 
Hierarchická entita hledá shodu počáteční a cílové polohy. Pokud je k dispozici pouze jeden podřízený prvek (počátek nebo cíl) hierarchické entity, přesto se extrahuje. Není potřeba vyhledat všechny podřízené entity, aby se extrahovala jedna nebo několik z nich. 

## <a name="what-luis-does"></a>Co dělá služba LUIS
Když se záměr a entity promluvy identifikují, [extrahují](luis-concept-data-extraction.md#list-entity-data) a vrátí z [koncového bodu](https://aka.ms/luis-endpoint-apis) ve formátu JSON, služba LUIS je hotová. Volající aplikace nebo chatbot převezme tuto odpověď JSON a splní požadavek způsobem, pro který jsou aplikace nebo chatbot určené. 

## <a name="create-a-new-app"></a>Vytvoření nové aplikace
1. Přihlaste se k webu [LUIS][LUIS]. Nezapomeňte se přihlásit k [oblasti][LUIS-regions], ve které potřebujete publikovat koncové body služby LUIS.

2. Na webu [LUIS][LUIS] vyberte **Create new app** (Vytvořit novou aplikaci).  

    [![](media/luis-quickstart-intent-and-hier-entity/app-list.png "Snímek obrazovky se stránkou se seznamy aplikací")](media/luis-quickstart-intent-and-hier-entity/app-list.png#lightbox)

3. V automaticky otevíraném dialogovém okně zadejte název `MyTravelApp`. 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-app.png "Snímek obrazovky s automaticky otevíraným dialogovým oknem Create new app (Vytvořit novou aplikaci)")](media/luis-quickstart-intent-and-hier-entity/create-new-app.png#lightbox)

4. Po dokončení tohoto procesu aplikace zobrazí stránku **Intents** (Záměry) se záměrem **None** (Žádný). 

    [![](media/luis-quickstart-intent-and-hier-entity/intents-page-none-only.png "Snímek obrazovky se seznamem záměrů obsahujícím pouze záměr None (Žádný)")](media/luis-quickstart-intent-and-hier-entity/intents-page-none-only.png#lightbox)

## <a name="create-a-new-intent"></a>Vytvoření nového záměru

1. Na stránce **Intents** (Záměry) vyberte **Create new intent** (Vytvořit nový záměr). 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-intent-button.png "Snímek obrazovky se seznamem záměrů a zvýrazněným tlačítkem Create new intent (Vytvořit nový záměr)")](media/luis-quickstart-intent-and-hier-entity/create-new-intent-button.png#lightbox)

2. Zadejte název nového záměru `BookFlight`. Tento záměr by se měl vybrat vždy, když chce uživatel provést rezervaci letu.

    Vytvořením záměru vytváříte primární kategorii informací, které chcete identifikovat. Pojmenováním kategorie umožníte všem ostatním aplikacím, které využívají výsledky dotazů ze služby LUIS, použít název této kategorie k vyhledání vhodné odpovědi nebo provedení odpovídající akce. Služba LUIS tyto dotazy nezodpoví, pouze identifikuje, na jaký typ informací směřuje dotaz v přirozeném jazyce. 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-intent.png "Snímek obrazovky s automaticky otevíraným dialogovým oknem Create new intent (Vytvořit nový záměr)")](media/luis-quickstart-intent-and-hier-entity/create-new-intent.png#lightbox)

3. Do záměru `BookFlight` přidejte několik promluv, které očekáváte, že uživatel bude požadovat, například:

    | Ukázkové promluvy|
    |--|
    |Book 2 flights from Seattle to Cairo next Monday (Zarezervovat 2 letenky ze Seattlu do Káhiry na příští pondělí)|
    |Reserve a ticket to London tomorrow (Zarezervovat letenku do Londýna na zítra)|
    |Schedule 4 seats from Paris to London for April 1 (Naplánovat rezervaci 4 míst z Paříže do Londýna na 1. dubna)|

    [![](media/luis-quickstart-intent-and-hier-entity/enter-utterances-on-intent.png "Snímek obrazovky se zadáváním promluv na stránce záměru BookFlight (Rezervovat let)")](media/luis-quickstart-intent-and-hier-entity/enter-utterances-on-intent.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>Přidání promluv do záměru None (Žádný)

Aplikace LUIS aktuálně neobsahuje žádné promluvy pro záměr **None** (Žádný). Aplikace potřebuje promluvy, na které nechcete, aby odpovídala, takže potřebuje mít promluvy v záměru **None** (Žádný). Nenechávejte ho prázdný. 

1. Na levém panelu vyberte **Intents** (Záměry). 

    [![](media/luis-quickstart-intent-and-hier-entity/select-intents-from-bookflight-intent.png "Snímek obrazovky se stránkou záměru BookFlight (Rezervovat let) a zvýrazněným tlačítkem Intents (Záměry)")](media/luis-quickstart-intent-and-hier-entity/select-intents-from-bookflight-intent.png#lightbox)

2. Vyberte záměr **None** (Žádný). Přidejte tři promluvy, které může uživatel zadat, ale které nejsou pro aplikaci relevantní:

    | Ukázkové promluvy|
    |--|
    |Cancel! (Zrušit!)|
    |Good bye (Na shledanou)|
    |What is going on? (Co se děje?)|

## <a name="when-the-utterance-is-predicted-for-the-none-intent"></a>Když se u promluvy předpokládá záměr None (Žádný)
Když služba LUIS vrátí pro promluvu záměr **None** (Žádný), v aplikaci volající službu LUIS (jako je například chatbot) se chatbot může zeptat, jestli chce uživatel ukončit konverzaci. Pokud uživatel nechce ukončit konverzaci, chatbot může také nabídnout možnosti jejího dalšího směřování. 

Entity fungují v záměru **None** (Žádný). Pokud je záměr s nejvyšším skóre **None** (Žádný), ale extrahovala se entita, která má pro chatbota smysl, může chatbot pokračovat otázkou, která se zaměří na záměr zákazníka. 

## <a name="create-a-location-entity-from-the-intent-page"></a>Vytvoření entity polohy na stránce záměru
Když teď oba záměry obsahují promluvy, potřebuje služba LUIS pochopit, co je poloha. Vraťte se k záměru `BookFlight` a podle následujícího postupu označte název města v promluvě:

1. Vraťte se k záměru `BookFlight` tak, že na levém panelu vyberete **Intents** (Záměry).

2. V seznamu záměrů vyberte `BookFlight`.

3. V promluvě `Book 2 flights from Seattle to Cairo next Monday` vyberte slovo `Seattle`. Zobrazí se rozevírací nabídka a v její horní části textové pole umožňující vytvoření nové entity. Zadejte do textového pole název entity `Location` a pak v rozevírací nabídce vyberte **Create new entity** (Vytvořit novou entitu). 

    [![](media/luis-quickstart-intent-and-hier-entity/label-seattle-in-utterance.png "Snímek obrazovky se stránkou záměru BookFlight (Rezervovat let) a vytvářením nové entity z vybraného textu")](media/luis-quickstart-intent-and-hier-entity/label-seattle-in-utterance.png#lightbox)

4. V automaticky otevíraném okně vyberte typ entity **Hierarchical** (Hierarchická) s podřízenými entitami `Origin` a `Destination`. Vyberte **Done** (Hotovo).

    [![](media/luis-quickstart-intent-and-hier-entity/hier-entity-ddl.png "Snímek obrazovky s automaticky otevíraným dialogovým oknem pro vytvoření nové entity polohy")](media/luis-quickstart-intent-and-hier-entity/hier-entity-ddl.png#lightbox)

    Popisek pro `Seattle` je označený jako `Location`, protože služba LUIS neví, jestli je toto slovo počátkem, cílem, nebo ani jedním. Vyberte `Seattle`, pak vyberte Location (Poloha), přejděte v nabídce doprava a vyberte `Origin`.

5. Když je teď entita vytvořená a jedna promluva označená, označte i ostatní města tak, že vyberete název města, pak Location (Poloha), přejdete v nabídce doprava a vyberete `Origin` nebo `Destination`.

    [![](media/luis-quickstart-intent-and-hier-entity/label-destination-in-utterance.png "Snímek obrazovky s entitou BookFlight (Rezervovat let) a vybraným textem promluvy pro výběr entity")](media/luis-quickstart-intent-and-hier-entity/label-destination-in-utterance.png#lightbox)

## <a name="train-the-luis-app"></a>Trénování aplikace LUIS
Služba LUIS nemá informace o změnách záměrů a entit (tedy modelu), dokud se nenatrénuje. 

1. V pravé horní části webu LUIS vyberte tlačítko **Train** (Trénovat).

    ![Trénování aplikace](./media/luis-quickstart-intent-and-hier-entity/train-button.png)

2. Trénování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

    ![Trénování proběhlo úspěšně](./media/luis-quickstart-intent-and-hier-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikování aplikace a získání adresy URL koncového bodu
Abyste mohli využít předpověď služby LUIS v chatbotu nebo jiné aplikaci, musíte aplikaci publikovat. 

1. V pravé horní části webu LUIS vyberte tlačítko **Publish** (Publikovat). 

    [![](media/luis-quickstart-intent-and-hier-entity/publish.png "Snímek obrazovky se záměrem BookFlight (Rezervovat let) a zvýrazněným tlačítkem Publish (Publikovat)")](media/luis-quickstart-intent-and-hier-entity/publish.png#lightbox)

2. Vyberte slot Production (Produkční) a tlačítko **Publish** (Publikovat).

    [![](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png "Snímek obrazovky se stránkou Publish (Publikovat) a zvýrazněným tlačítkem Publish to production slot (Publikovat do produkčního slotu)")](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png#lightbox)

3. Publikování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Odeslání dotazu na koncový bod s jinou promluvou
1. V dolní části stránky **Publish** (Publikovat) vyberte odkaz na **koncový bod**. Tato akce otevře další okno prohlížeče s adresou URL koncového bodu v adresním řádku. 

    [![](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png "Snímek obrazovky se stránkou Publish (Publikovat) a zvýrazněnou adresou URL koncového bodu")](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png#lightbox)

2. Na konec adresy URL zadejte `1 ticket to Portland on Friday`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. Tato promluva není stejná jako žádná z označených promluv, proto je to dobrý test a měl by se vrátit záměr `BookFlight` s extrahovanou hierarchickou entitou.

```
{
  "query": "1 ticket to Portland on Friday",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.9998226
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 0.9998226
    },
    {
      "intent": "None",
      "score": 0.221926212
    }
  ],
  "entities": [
    {
      "entity": "portland",
      "type": "Location::Destination",
      "startIndex": 12,
      "endIndex": 19,
      "score": 0.564448953
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>Co tato aplikace LUIS udělala?
Tato aplikace s pouhými dvěma záměry a hierarchickou entitou identifikovala záměr dotazu v přirozeném jazyce a vrátila extrahovaná data. 

Váš chatbot má teď dostatek informací k určení primární akce `BookFlight` a informací o poloze nalezených v promluvě. 

## <a name="where-is-this-luis-data-used"></a>Kde se tato data služby LUIS používají? 
Služba LUIS s tímto požadavkem skončila. Volající aplikace, například chatbot, může převzít výsledek topScoringIntent a data z entity a provést další krok. Služba LUIS neprovádí tuto programovou práci za chatbota ani nevolá aplikaci. Služba LUIS pouze určuje, co je záměrem uživatele. 

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už aplikaci LUIS nepotřebujete, odstraňte ji. Provedete to tak, že vyberete nabídku se třemi tečkami (...) vpravo od názvu aplikace v seznamu aplikací a vyberete **Delete** (Odstranit). V automaticky otevíraném dialogovém okně **Delete app?** (Odstranit aplikaci?) vyberte **Ok**.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"] 
> [Informace o postupu při přidání entity seznamu](luis-quickstart-intent-and-list-entity.md) 

Přidání [předem připravené entity](luis-how-to-add-entities.md#add-prebuilt-entity) **čísla** pro extrakci čísla. 

Přidání [předem připravené entity](luis-how-to-add-entities.md#add-prebuilt-entity) **datetimeV2** pro extrakci informací o datu.


<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
