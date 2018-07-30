---
title: Kurz vytvoření aplikace LUIS pro získání dat o poloze – Azure | Microsoft Docs
description: V tomto kurzu zjistíte, jak vytvořit jednoduchou aplikaci LUIS využívající záměry a hierarchickou entitu k extrakci dat.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 07/04/2018
ms.author: diberry
ms.openlocfilehash: fb29e0a22331ce279d3dc8fc5a0044ae794d260b
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226080"
---
# <a name="tutorial-5-add-hierarchical-entity"></a>Kurz: 5. Přidání hierarchické entity
V tomto kurzu vytvoříte aplikaci, která ukazuje vyhledání souvisejících částí dat na základě kontextu. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Vysvětlení hierarchických entit a kontextově naučených podřízených entit 
> * Použití aplikace LUIS v doméně lidských zdrojů 
> * Přidání hierarchické entity polohy s podřízenými entitami počátku a cíle
> * Trénování a publikování aplikace
> * Odeslání dotazu na koncový bod aplikace a zobrazení odpovědi JSON ze služby LUIS obsahující hierarchické podřízené entity 

Pro účely tohoto článku potřebujete bezplatný účet [LUIS](luis-reference-regions.md#luis-website), abyste mohli vytvořit svou aplikaci LUIS.

## <a name="before-you-begin"></a>Než začnete
Pokud nemáte aplikaci pro lidské zdroje z kurzu k [entitám seznamu](luis-quickstart-intent-and-list-entity.md), [naimportujte](luis-how-to-start-new-app.md#import-new-app) JSON do nové aplikace na webu služby [LUIS](luis-reference-regions.md#luis-website). Aplikaci k importování najdete v úložišti [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-list-HumanResources.json) na Githubu.

Pokud chcete zachovat původní aplikaci pro lidské zdroje, naklonujte verzi na stránce [Settings](luis-how-to-manage-versions.md#clone-a-version) (Nastavení) a pojmenujte ji `hier`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. 

## <a name="purpose-of-the-app-with-this-entity"></a>Účel aplikace s touto entitou
Tato aplikace určuje, do jakého cílového umístění (budova a kancelář) se má zaměstnanec přesunout z počátečního místa (budova a kancelář). K určení míst v rámci promluvy se používá hierarchická entita. 

Hierarchická entita je vhodná pro tento typ dat, protože obě části dat:

* Se k sobě v kontextu promluvy navzájem vztahují.
* K označení obou míst používají specifická slova. Mezi příklady těchto slov patří from/to (z/do), leaving/headed to (odstěhovat/nastěhovat), away from/toward (z kanceláře/do kanceláře).
* Obě polohy se často vyskytují ve stejné promluvě. 

Účelem **hierarchické** entity je na základě kontextu vyhledat data související s promluvou. Představte si následující promluvu:

```JSON
mv Jill Jones from a-2349 to b-1298
```
V promluvě jsou určená dvě místa – `a-2349` a `b-1298`. Předpokládejme, že písmeno odpovídá názvu budovy a číslo označuje kancelář v této budově. V tomto případě dává smysl, aby byly obě informace seskupeny jako podřízené prvky hierarchické entity `Locations`, protože obě části dat je potřeba z promluvy extrahovat a navzájem se k sobě vztahují. 
 
Pokud je k dispozici pouze jeden podřízený prvek (počátek nebo cíl) hierarchické entity, přesto se extrahuje. Není potřeba vyhledat všechny podřízené entity, aby se extrahovala jedna nebo několik z nich. 

## <a name="remove-prebuilt-number-entity-from-app"></a>Odebrání předem připravené entity čísla z aplikace
Abyste mohli vidět celou promluvu a mohli označit podřízené prvky hierarchické entity, odeberte dočasně předem připravenou entitu čísla.

1. Ujistěte se, že je vaše aplikace pro lidské zdroje uvedená v části **Build** (Sestavení) služby LUIS. Do této části můžete přejít výběrem možnosti **Build** (Sestavit) v pravém horním řádku nabídek. 

    [ ![Snímek obrazovky aplikace LUIS se zvýrazněnou možností Build (Sestavit) na pravém horním navigačním panelu](./media/luis-quickstart-intent-and-hier-entity/hr-first-image.png)](./media/luis-quickstart-intent-and-hier-entity/hr-first-image.png#lightbox)

2. V levé nabídce vyberte **Entities** (Entity).

    [ ![Snímek obrazovky aplikace LUIS se zvýrazněným tlačítkem Entities (Entity) v levé nabídce](./media/luis-quickstart-intent-and-hier-entity/hr-select-entities-button.png)](./media/luis-quickstart-intent-and-hier-entity/hr-select-entities-button.png#lightbox)


3. V seznamu vyberte tlačítko se třemi tečkami (***...***) vpravo od entity čísla. Vyberte **Odstranit**. 

    [ ![Snímek aplikace LUIS na stránce se seznamem entit se zvýrazněným tlačítkem Delete (Odstranit) u předem připravené entity čísla](./media/luis-quickstart-intent-and-hier-entity/hr-delete-number-prebuilt.png)](./media/luis-quickstart-intent-and-hier-entity/hr-delete-number-prebuilt.png#lightbox)


## <a name="add-utterances-to-moveemployee-intent"></a>Přidání projevů do záměru MoveEmployee

1. V levé nabídce vyberte **Intents** (Záměry).

    [ ![Snímek obrazovky aplikace LUIS se zvýrazněným tlačítkem Intents (Záměry) v levé nabídce](./media/luis-quickstart-intent-and-hier-entity/hr-select-intents-button.png)](./media/luis-quickstart-intent-and-hier-entity/hr-select-intents-button.png#lightbox)

2. V seznamu záměrů vyberte **MoveEmployee** (Přesunutí zaměstnance).

    [ ![Snímek obrazovky aplikace LUIS se zvýrazněným záměrem MoveEmployee (Přesunutí zaměstnance) v levé nabídce](./media/luis-quickstart-intent-and-hier-entity/hr-intents-list-moveemployee.png)](./media/luis-quickstart-intent-and-hier-entity/hr-intents-list-moveemployee.png#lightbox)

3. Přidejte následující příklady promluv:

    |Ukázkové promluvy|
    |--|
    |Přesunout John Smithe **do** a-2345.|
    |Poslat Jill Jonesovou **do** b-3499.|
    |Zorganizovat přesun x23456 **z** hh 2345 **do** e-0234.|
    |Začít připravovat podklady, abys se mohl x12345 **odstěhovat** z a-3459 a **nastěhovat** do f-34567.|
    |Přemístit 425-555-0000 **z kanceláře** g-2323 **do kanceláře** hh 2345.|

    V kurzu k [entitám seznamu](luis-quickstart-intent-and-list-entity.md) může být zaměstnanec určen pomocí jména, e-mailu, čísla telefonní linky, čísla mobilního telefonu a čísla amerického federálního sociálního pojištění. V promluvách se používají tato čísla zaměstnanců. Předchozí příklady promluv obsahují různé způsoby (zobrazené tučně), jak označit počáteční a cílové místo. Dvě z těchto promluv obsahují záměrně jen cílové místo. To pomáhá aplikaci LUIS porozumět tomu, jak jsou tato místa v promluvě umístěna, když není určeno počáteční místo.

    [ ![Snímek obrazovky aplikace LUIS s novými promluvami v záměru MoveEmployee (Přesunutí zaměstnance)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)
     

## <a name="create-a-location-entity"></a>Vytvoření entity místa
Služba LUIS potřebuje porozumět tomu, co je místo, tím, že v promluvách označí počáteční a cílové místo. Pokud potřebujete promluvu zobrazit v zobrazení tokenů (nezpracovaných), vyberte přepínač na panelu nad promluvami s popiskem **Entities View** (Zobrazení entit). Po přepnutí přepínače bude mít tento ovládací prvek popisek **Tokens View** (Zobrazení tokenů).

1. V promluvě `Displace 425-555-0000 away from g-2323 toward hh-2345` vyberte slovo `g-2323`. Zobrazí se rozevírací nabídka s textovým polem v horní části. Zadejte do textového pole název entity `Locations` a pak v rozevírací nabídce vyberte **Create new entity** (Vytvořit novou entitu). 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png "Snímek obrazovky s vytvářením nové entity na stránce záměru")](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png#lightbox)

2. V automaticky otevíraném okně vyberte typ entity **Hierarchical** (Hierarchická) s podřízenými entitami `Origin` a `Destination`. Vyberte **Done** (Hotovo).

    ![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "Snímek obrazovky s automaticky otevíraným dialogovým oknem pro novou entitu místa")

3. Popisek pro `g-2323` je označený jako `Locations`, protože služba LUIS neví, jestli je toto slovo počátkem, cílem, nebo ani jedním. Vyberte `g-2323`, pak vyberte **Locations** (Místa) a pak přejděte do nabídky vpravo a vyberte `Origin`.

    [![](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png "Snímek obrazovky s automaticky otevíraným dialogovým oknem označování entit pro změnu podřízeného prvku entity místa")](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png#lightbox)

5. Označte ostatní místa ve všech dalších promluvách tím, že v promluvě vyberete budovu a kancelář, pak vyberete Locations (Místa) a pak v nabídce vpravo vyberete `Origin` (Počátek) nebo `Destination` (Cíl). Až označíte všechna místa, začnou promluvy v zobrazení **Tokens View** (Zobrazení tokenů) vypadat jako vzor. 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png "Snímek obrazovky s entitami míst označených v promluvách")](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png#lightbox)

## <a name="add-prebuilt-number-entity-to-app"></a>Přidání předem připravené entity čísla do aplikace
Přidejte předem připravenou entitu čísla zpět do aplikace.

1. V levé navigační nabídce vyberte **Entities** (Entity).

    [ ![Snímek obrazovky se zvýrazněným tlačítkem Entities (Entity) na levém navigačním panelu](./media/luis-quickstart-intent-and-hier-entity/hr-select-entity-button-from-intent-page.png)](./media/luis-quickstart-intent-and-hier-entity/hr-select-entity-button-from-intent-page.png#lightbox)

2. Vyberte tlačítko **Manage prebuilt entities** (Spravovat předem připravené entity).

    [ ![Snímek obrazovky se seznamem entit se zvýrazněným tlačítkem Manage prebuilt entities (Spravovat předem připravené entity)](./media/luis-quickstart-intent-and-hier-entity/hr-manage-prebuilt-button.png)](./media/luis-quickstart-intent-and-hier-entity/hr-manage-prebuilt-button.png#lightbox)

3. V seznamu předem připravených entit vyberte možnost **number** (číslo) a pak vyberte **Done** (Hotovo).

    ![Snímek obrazovky s vybranou možností number (číslo) v dialogovém okně s předem připravenými entitami](./media/luis-quickstart-intent-and-hier-entity/hr-add-number-back-ddl.png)

## <a name="train-the-luis-app"></a>Trénování aplikace LUIS
Služba LUIS nemá informace o změnách záměrů a entit (tedy modelu), dokud se nenatrénuje. 

1. V pravé horní části webu LUIS vyberte tlačítko **Train** (Trénovat).

    ![Trénování aplikace](./media/luis-quickstart-intent-and-hier-entity/train-button.png)

2. Trénování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

    ![Trénování proběhlo úspěšně](./media/luis-quickstart-intent-and-hier-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikování aplikace a získání adresy URL koncového bodu
Abyste mohli využít předpověď služby LUIS v chatbotu nebo jiné aplikaci, musíte aplikaci publikovat. 

1. V pravé horní části webu LUIS vyberte tlačítko **Publish** (Publikovat). 

2. Vyberte slot Production (Produkční) a tlačítko **Publish** (Publikovat).

    [![](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png "Snímek obrazovky se stránkou Publish (Publikovat) a zvýrazněným tlačítkem Publish to production slot (Publikovat do produkčního slotu)")](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png#lightbox)

3. Publikování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Odeslání dotazu na koncový bod s jinou promluvou
1. V dolní části stránky **Publish** (Publikovat) vyberte odkaz na **koncový bod**. Tato akce otevře další okno prohlížeče s adresou URL koncového bodu v adresním řádku. 

    [![](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png "Snímek obrazovky se stránkou Publish (Publikovat) a zvýrazněnou adresou URL koncového bodu")](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png#lightbox)

2. Přejděte na konec adresy URL v panelu adresy a zadejte `Please relocation jill-jones@mycompany.com from x-2345 to g-23456`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. Tato promluva není stejná jako žádná z označených promluv, proto je to dobrý test a měl by se vrátit záměr `MoveEmployee` s extrahovanou hierarchickou entitou.

```JSON
{
  "query": "Please relocation jill-jones@mycompany.com from x-2345 to g-23456",
  "topScoringIntent": {
    "intent": "MoveEmployee",
    "score": 0.9966052
  },
  "intents": [
    {
      "intent": "MoveEmployee",
      "score": 0.9966052
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.0325253047
    },
    {
      "intent": "FindForm",
      "score": 0.006137873
    },
    {
      "intent": "GetJobInformation",
      "score": 0.00462633232
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00415637763
    },
    {
      "intent": "ApplyForJob",
      "score": 0.00382325822
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00249120337
    },
    {
      "intent": "None",
      "score": 0.00130756292
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.00119622645
    },
    {
      "intent": "Utilities.Confirm",
      "score": 1.26910036E-05
    }
  ],
  "entities": [
    {
      "entity": "jill - jones @ mycompany . com",
      "type": "Employee",
      "startIndex": 18,
      "endIndex": 41,
      "resolution": {
        "values": [
          "Employee-45612"
        ]
      }
    },
    {
      "entity": "x - 2345",
      "type": "Locations::Origin",
      "startIndex": 48,
      "endIndex": 53,
      "score": 0.8520272
    },
    {
      "entity": "g - 23456",
      "type": "Locations::Destination",
      "startIndex": 58,
      "endIndex": 64,
      "score": 0.974032
    },
    {
      "entity": "-2345",
      "type": "builtin.number",
      "startIndex": 49,
      "endIndex": 53,
      "resolution": {
        "value": "-2345"
      }
    },
    {
      "entity": "-23456",
      "type": "builtin.number",
      "startIndex": 59,
      "endIndex": 64,
      "resolution": {
        "value": "-23456"
      }
    }
  ]
}
```

## <a name="could-you-have-used-a-regular-expression-for-each-location"></a>Bylo by možné použít pro každé místo regulární výraz?
Ano, můžete vytvořit regulární výraz s rolemi počátečního a cílového místa a používat ho ve vzoru.

Místa v tomto příkladu, jako je třeba `a-1234`, používají specifický formát, který obsahuje jedno nebo dvě písmena, pomlčku a pak řadu 4 nebo 5 číslic. Tato data je možné popsat jako entitu regulárního výrazu s rolí pro každé místo. U vzorů jsou k dispozici role. Můžete vytvořit vzory založené na těchto promluvách a pak vytvořit regulární výraz pro formát místa a přidat ho do vzorů. <!-- Go to this tutorial to see how that is done -->

## <a name="what-has-this-luis-app-accomplished"></a>Co tato aplikace LUIS udělala?
Tato aplikace pomocí jen několika záměrů a hierarchické entity identifikovala záměr dotazu v přirozeném jazyce a vrátila extrahovaná data. 

Váš chatbot má teď dostatek informací k určení primární akce `MoveEmployee` a informací o poloze nalezených v promluvě. 

## <a name="where-is-this-luis-data-used"></a>Kde se tato data služby LUIS používají? 
Služba LUIS s tímto požadavkem skončila. Volající aplikace, například chatbot, může převzít výsledek topScoringIntent a data z entity a provést další krok. Služba LUIS neprovádí tuto programovou práci za chatbota ani nevolá aplikaci. Služba LUIS pouze určuje, co je záměrem uživatele. 

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už aplikaci LUIS nepotřebujete, odstraňte ji. Vyberte tlačítko se třemi tečkami (***...***) vpravo od názvu aplikace v seznamu aplikací a potom vyberte **Delete** (Odstranit). V automaticky otevíraném dialogovém okně **Delete app?** (Odstranit aplikaci?) vyberte **Ok**.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"] 
> [Další informace o přidání složené entity](luis-tutorial-composite-entity.md) 