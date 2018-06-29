---
title: Kurz vytvoření aplikace LUIS pro získání přesné shody textu s daty v seznamu – Azure | Microsoft Docs
description: V tomto kurzu zjistíte, jak vytvořit jednoduchou aplikaci LUIS využívající záměry a entity seznamu k extrakci dat v tomto rychlém startu.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 33394dff1091f27c79c74d8648a90724ba8d6698
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264823"
---
# <a name="tutorial-create-app-using-a-list-entity"></a>Kurz: Vytvoření aplikace využívající entitu seznamu
V tomto kurzu vytvoříte aplikaci, která ukazuje, jak získat data odpovídající předdefinovanému seznamu. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Vysvětlení entit seznamu 
> * Vytvoření nové aplikace LUIS pro doménu nápojů se záměrem OrderDrinks (Objednat nápoje)
> * Přidání záměru _None_ (Žádný) a přidání ukázkových promluv
> * Přidání entity seznamu pro extrakci položek nápojů z promluvy
> * Trénování a publikování aplikace
> * Odeslání dotazu na koncový bod aplikace a zobrazení odpovědi JSON ze služby LUIS

Pro účely tohoto článku potřebujete bezplatný účet [LUIS][LUIS], abyste mohli vytvořit svou aplikaci LUIS.

## <a name="purpose-of-the-list-entity"></a>Účel entity seznamu
Tato aplikace přebírá objednávky nápojů, například `1 coke and 1 milk please`, a vrací data, jako je například typ nápoje. Entita **seznamu** nápojů hledá přesné shody textu a vrací tyto shody. 

Entita seznamu je vhodná pro tento typ dat, kdy je známá sada hodnot dat. Názvy nápojů se můžou lišit a můžou se v nich používat hovorové výrazy a zkratky, ale samotné názvy se nemění často. 

## <a name="app-intents"></a>Záměry aplikace
Záměry představují kategorie toho, co uživatel chce. Tato aplikace obsahuje dva záměry: OrderDrinks (Objednat nápoje) a None (Žádný). Záměr [None](luis-concept-intent.md#none-intent-is-fallback-for-app) (Žádný) se používá účelně k označení všeho mimo aplikaci.  

## <a name="list-entity-is-an-exact-text-match"></a>Entita seznamu hledá přesnou shodu textu
Účelem této entity je vyhledat v promluvě části textu a uspořádat je do kategorií. Entita [seznamu](luis-concept-entity-types.md) umožňuje hledat přesnou shodu slov nebo frází.  

Pro tuto nápojovou aplikaci služba LUIS extrahuje objednávku nápojů takovým způsobem, aby bylo možné vytvořit a vyplnit standardní objednávku. Služba LUIS umožňuje v promluvách používat variace, zkratky a hovorové výrazy. 

Mezi jednoduché ukázkové promluvy od uživatelů patří:

```
2 glasses of milk
3 bottles of water
2 cokes
```

Mezi zkrácené nebo hovorové verze promluv patří:

```
5 milk
3 h2o
1 pop
```
 
Entita seznamu páruje `h2o` s vodou a `pop` s nealkoholickými nápoji.  

## <a name="what-luis-does"></a>Co dělá služba LUIS
Když se záměr a entity promluvy identifikují, [extrahují](luis-concept-data-extraction.md#list-entity-data) a vrátí z [koncového bodu](https://aka.ms/luis-endpoint-apis) ve formátu JSON, služba LUIS je hotová. Volající aplikace nebo chatbot převezme tuto odpověď JSON a splní požadavek způsobem, pro který jsou aplikace nebo chatbot určené. 

## <a name="create-a-new-app"></a>Vytvoření nové aplikace
1. Přihlaste se k webu [LUIS][LUIS]. Nezapomeňte se přihlásit k [oblasti][LUIS-regions], ve které potřebujete publikovat koncové body služby LUIS.

2. Na webu [LUIS][LUIS] vyberte **Create new app** (Vytvořit novou aplikaci).  

    ![Vytvoření nové aplikace](./media/luis-quickstart-intent-and-list-entity/app-list.png)

3. V automaticky otevíraném dialogovém okně zadejte název `MyDrinklist`. 

    ![Pojmenování aplikace MyDrinkList](./media/luis-quickstart-intent-and-list-entity/create-app-dialog.png)

4. Po dokončení tohoto procesu aplikace zobrazí stránku **Intents** (Záměry) se záměrem **None** (Žádný). 

    [![](media/luis-quickstart-intent-and-list-entity/intents-page-none-only.png "Snímek obrazovky se stránkou Intents (Záměry)")](media/luis-quickstart-intent-and-list-entity/intents-page-none-only.png#lightbox)

## <a name="create-a-new-intent"></a>Vytvoření nového záměru

1. Na stránce **Intents** (Záměry) vyberte **Create new intent** (Vytvořit nový záměr). 

    [![](media/luis-quickstart-intent-and-list-entity/create-new-intent.png "Snímek obrazovky se stránkou Intents (Záměry) a zvýrazněným tlačítkem Create new intent (Vytvořit nový záměr)")](media/luis-quickstart-intent-and-list-entity/create-new-intent.png#lightbox)

2. Zadejte název nového záměru `OrderDrinks`. Tento záměr by se měl vybrat vždy, když si chce uživatel objednat nápoj.

    Vytvořením záměru vytváříte primární kategorii informací, které chcete identifikovat. Pojmenováním kategorie umožníte všem ostatním aplikacím, které využívají výsledky dotazů ze služby LUIS, použít název této kategorie k vyhledání vhodné odpovědi nebo provedení odpovídající akce. Služba LUIS tyto dotazy nezodpoví, pouze identifikuje, na jaký typ informací směřuje dotaz v přirozeném jazyce. 

    [![](media/luis-quickstart-intent-and-list-entity/intent-create-dialog-order-drinks.png "Snímek obrazovky s vytvářením nového záměru OrderDrinks (Objednat nápoje)")](media/luis-quickstart-intent-and-list-entity/intent-create-dialog-order-drinks.png#lightbox)

3. Do záměru `OrderDrinks` přidejte několik promluv, které očekáváte, že uživatel bude požadovat, například:

    | Ukázkové promluvy|
    |--|
    |Please send 2 cokes and a bottle of water to my room (Pošlete do mého pokoje prosím 2 coly a láhev vody)|
    |2 perriers with a twist of lime (2 minerálky s kouskem citrónu)|
    |h20|

    [![](media/luis-quickstart-intent-and-list-entity/intent-order-drinks-utterance.png "Snímek obrazovky se zadáváním promluvy na stránce záměru OrderDrinks (Objednat nápoje)")](media/luis-quickstart-intent-and-list-entity/intent-order-drinks-utterance.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>Přidání promluv do záměru None (Žádný)

Aplikace LUIS aktuálně neobsahuje žádné promluvy pro záměr **None** (Žádný). Aplikace potřebuje promluvy, na které nechcete, aby odpovídala, takže potřebuje mít promluvy v záměru **None** (Žádný). Nenechávejte ho prázdný. 

1. Na levém panelu vyberte **Intents** (Záměry). 

    [![](media/luis-quickstart-intent-and-list-entity/left-panel-intents.png "Snímek obrazovky s výběrem odkazu Intents (Záměry) na levém panelu")](media/luis-quickstart-intent-and-list-entity/left-panel-intents.png#lightbox)

2. Vyberte záměr **None** (Žádný). Přidejte tři promluvy, které může uživatel zadat, ale které nejsou pro aplikaci relevantní:

    | Ukázkové promluvy|
    |--|
    |Cancel! (Zrušit!)|
    |Good bye (Na shledanou)|
    |What is going on? (Co se děje?)|

## <a name="when-the-utterance-is-predicted-for-the-none-intent"></a>Když se u promluvy předpokládá záměr None (Žádný)
Když služba LUIS vrátí pro promluvu záměr **None** (Žádný), v aplikaci volající službu LUIS (jako je například chatbot) se chatbot může zeptat, jestli chce uživatel ukončit konverzaci. Pokud uživatel nechce ukončit konverzaci, chatbot může také nabídnout možnosti jejího dalšího směřování. 

Entity fungují v záměru **None** (Žádný). Pokud je záměr s nejvyšším skóre **None** (Žádný), ale extrahovala se entita, která má pro chatbota smysl, může chatbot pokračovat otázkou, která se zaměří na záměr zákazníka. 

## <a name="create-a-menu-entity-from-the-intent-page"></a>Vytvoření entity nabídky na stránce záměru
Když teď oba záměry obsahují promluvy, potřebuje služba LUIS pochopit, co je nápoj. Vraťte se k záměru `OrderDrinks` a podle následujícího postupu označte nápoje v promluvě:

1. Vraťte se k záměru `OrderDrinks` tak, že na levém panelu vyberete **Intents** (Záměry).

2. V seznamu záměrů vyberte `OrderDrinks`.

3. V promluvě `Please send 2 cokes and a bottle of water to my room` vyberte slovo `water`. Zobrazí se rozevírací nabídka a v její horní části textové pole umožňující vytvoření nové entity. Zadejte do textového pole název entity `Drink` a pak v rozevírací nabídce vyberte **Create new entity** (Vytvořit novou entitu). 

    [![](media/luis-quickstart-intent-and-list-entity/intent-label-h2o-in-utterance.png "Snímek obrazovky s vytvářením nové entity výběrem slova v promluvě")](media/luis-quickstart-intent-and-list-entity/intent-label-h2o-in-utterance.png#lightbox)

4. V automaticky otevíraném okně vyberte typ entity **List** (Seznam). Přidejte synonymum `h20`. Po zadání každého synonyma stiskněte klávesu Enter. Nepřidávejte do seznamu synonym výraz `perrier`. Ten se přidá jako příklad v dalším kroku. Vyberte **Done** (Hotovo).

    [![](media/luis-quickstart-intent-and-list-entity/create-list-ddl.png "Snímek obrazovky s konfigurací nové entity")](media/luis-quickstart-intent-and-list-entity/create-list-ddl.png#lightbox)

5. Když je teď entita vytvořená, označte i ostatní synonyma pro vodu tak, že vyberete synonymum pro vodu a pak v rozevíracím seznamu vyberete `Drink`. V nabídce přejděte doprava, vyberte `Set as synonym` a pak `water`.

    [![](media/luis-quickstart-intent-and-list-entity/intent-label-perriers.png "Snímek obrazovky s označováním stávající entity v promluvě")](media/luis-quickstart-intent-and-list-entity/intent-label-perriers.png#lightbox)

## <a name="modify-the-list-entity-from-the-entity-page"></a>Úprava entity seznamu na stránce entity
Entita seznamu nápojů je vytvořená, ale obsahuje málo položek a synonym. Pokud znáte některé výrazy, zkratky a hovorové výrazy, je jednodušší vyplnit seznam na stránce **entity**. 

1. Na levém panelu vyberte **Entities** (Entity).

    [![](media/luis-quickstart-intent-and-list-entity/intent-select-entities.png "Snímek obrazovky s výběrem možnosti Entities (Entity) na levém panelu")](media/luis-quickstart-intent-and-list-entity/intent-select-entities.png#lightbox)

2. V seznamu entity vyberte `Drink`.

    [![](media/luis-quickstart-intent-and-list-entity/entities-select-drink-entity.png "Snímek obrazovky s výběrem entity Drink v seznamu entit")](media/luis-quickstart-intent-and-list-entity/entities-select-drink-entity.png#lightbox)

3. Do textového pole zadejte `Soda pop` a stiskněte Enter. Tento výraz se běžně používá pro sycené nápoje. Každá kultura má pro tento typ nápoje nějakou přezdívku nebo hovorový výraz.

    [![](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-canonical-name.png "Snímek obrazovky se zadáváním názvu v kanonickém tvaru")](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-canonical-name.png#lightbox)

4. Na stejném řádku jako `Soda pop` zadejte synonyma, například: 

    ```
    coke
    cokes
    coca-cola
    coca-colas
    ```

    Synonyma můžou obsahovat fráze, interpunkci, přivlastňovací zájmena a množná čísla. Vzhledem k tomu, že entita seznamu hledá přesnou shodu textu (kromě velikosti znaků), musí synonyma obsahovat všechny variace. Seznam můžete rozšiřovat s tím, jak budete zjišťovat další variace z protokolů dotazů nebo při kontrole požadavků přicházejících do koncového bodu. 

    Kvůli zachování stručnosti příkladu obsahuje tento článek jenom pár synonym. Produkční aplikace LUIS by obsahovala velké množství synonym, která by se pravidelně kontrolovala a rozšiřovala. 

    [![](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-synonyms.png "Snímek obrazovky s přidáváním synonym")](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-synonyms.png#lightbox)

## <a name="train-the-luis-app"></a>Trénování aplikace LUIS
Služba LUIS nemá informace o změnách záměrů a entit (tedy modelu), dokud se nenatrénuje. 

1. V pravé horní části webu LUIS vyberte tlačítko **Train** (Trénovat).

    ![Trénování aplikace](./media/luis-quickstart-intent-and-list-entity/train-button.png)

2. Trénování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

    ![Trénování proběhlo úspěšně](./media/luis-quickstart-intent-and-list-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikování aplikace a získání adresy URL koncového bodu
Abyste mohli využít předpověď služby LUIS v chatbotu nebo jiné aplikaci, musíte aplikaci publikovat. 

1. V pravé horní části webu LUIS vyberte tlačítko **Publish** (Publikovat). 

    [![](media/luis-quickstart-intent-and-list-entity/publish.png "Snímek obrazovky s výběrem tlačítka Publish (Publikovat)")](media/luis-quickstart-intent-and-list-entity/publish.png#lightbox)

2. Vyberte slot Production (Produkční) a tlačítko **Publish** (Publikovat). 

    [![](media/luis-quickstart-intent-and-list-entity/publish-to-production.png "Snímek obrazovky s výběrem tlačítka Publish to production slot (Publikovat do produkčního slotu)")](media/luis-quickstart-intent-and-list-entity/publish-to-production.png#lightbox)

3. Publikování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Odeslání dotazu na koncový bod s jinou promluvou
1. V dolní části stránky **Publish** (Publikovat) vyberte odkaz na **koncový bod**. Tato akce otevře další okno prohlížeče s adresou URL koncového bodu v adresním řádku. 

    [![](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png "Snímek obrazovky s adresou URL koncového bodu na stránce Publish (Publikovat)")](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png#lightbox)

2. Na konec adresy URL zadejte `2 cokes and 3 waters`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. Tato promluva není stejná jako žádná z označených promluv, proto je to dobrý test a měl by se vrátit záměr `OrderDrinks` se dvěma typy nápojů `cokes` a `waters`.

```
{
  "query": "2 cokes and 3 waters",
  "topScoringIntent": {
    "intent": "OrderDrinks",
    "score": 0.999998569
  },
  "intents": [
    {
      "intent": "OrderDrinks",
      "score": 0.999998569
    },
    {
      "intent": "None",
      "score": 0.23884207
    }
  ],
  "entities": [
    {
      "entity": "cokes",
      "type": "Drink",
      "startIndex": 2,
      "endIndex": 6,
      "resolution": {
        "values": [
          "Soda pop"
        ]
      }
    },
    {
      "entity": "waters",
      "type": "Drink",
      "startIndex": 14,
      "endIndex": 19,
      "resolution": {
        "values": [
          "h20"
        ]
      }
    }
  ]
}
```

## <a name="where-is-the-natural-language-processing-in-the-list-entity"></a>Kde se v entitě seznamu provádí zpracování přirozeného jazyka? 
Vzhledem k tomu, že entita seznamu hledá přesnou shodu textu, nespoléhá se na zpracování přirozeného jazyka (ani strojové učení). Služba LUIS však využívá zpracování přirozeného jazyka (nebo strojové učení) k výběru správného záměru s nejvyšším skóre. Promluva navíc může být kombinací více než jedné entity nebo dokonce více než jednoho typu entity. V každé promluvě se zpracovávají všechny entity v aplikaci, včetně entit zpracování přirozeného jazyka (nebo strojově naučených entit), jako je například **jednoduchá** entita.

## <a name="what-has-this-luis-app-accomplished"></a>Co tato aplikace LUIS udělala?
Tato aplikace s pouhými dvěma záměry a entitou seznamu identifikovala záměr dotazu v přirozeném jazyce a vrátila extrahovaná data. 

Váš chatbot má teď dostatek informací, aby z entity seznamu nápojů určil primární akci `OrderDrinks` a typ objednaných nápojů. 

## <a name="where-is-this-luis-data-used"></a>Kde se tato data služby LUIS používají? 
Služba LUIS s tímto požadavkem skončila. Volající aplikace, například chatbot, může převzít výsledek topScoringIntent a data z entity a provést další krok. Služba LUIS neprovádí tuto programovou práci za chatbota ani nevolá aplikaci. Služba LUIS pouze určuje, co je záměrem uživatele. 

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už aplikaci LUIS nepotřebujete, odstraňte ji. Provedete to tak, že vyberete nabídku se třemi tečkami (...) vpravo od názvu aplikace v seznamu aplikací a vyberete **Delete** (Odstranit). V automaticky otevíraném dialogovém okně **Delete app?** (Odstranit aplikaci?) vyberte **Ok**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Informace o postupu při přidání entity regulárního výrazu](luis-quickstart-intents-regex-entity.md)

Přidání [předem připravené entity](luis-how-to-add-entities.md#add-prebuilt-entity) **čísla** pro extrakci čísla. 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
