---
title: Kurz vytvoření aplikace LUIS pro extrakci dat – Azure | Microsoft Docs
description: V tomto kurzu zjistíte, jak vytvořit jednoduchou aplikaci LUIS využívající záměry a jednoduchou entitu k extrakci strojově naučených dat.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 03/29/2018
ms.author: v-geberr
ms.openlocfilehash: 1e8647e34da3d34946a4f6ac298017f6d4c99de6
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265356"
---
# <a name="tutorial-create-app-that-uses-simple-entity"></a>Kurz: Vytvoření aplikace využívající jednoduchou entitu
V tomto kurzu vytvoříte aplikaci, která ukazuje extrakci strojově naučených dat z promluvy pomocí **jednoduché** entity.

<!-- green checkmark -->
> [!div class="checklist"]
> * Vysvětlení jednoduchých entit 
> * Vytvoření nové aplikace LUIS pro doménu komunikace se záměrem SendMessage (Odeslat zprávu)
> * Přidání záměru _None_ (Žádný) a přidání ukázkových promluv
> * Přidání jednoduché entity pro extrakci obsahu zprávy z promluvy
> * Trénování a publikování aplikace
> * Odeslání dotazu na koncový bod aplikace a zobrazení odpovědi JSON ze služby LUIS

Pro účely tohoto článku potřebujete bezplatný účet [LUIS][LUIS], abyste mohli vytvořit svou aplikaci LUIS.

## <a name="purpose-of-the-app"></a>Účel aplikace
Tato aplikace ukazuje, jak získat data z promluvy. Představte si následující promluvu z chatbota:

```JSON
Send a message telling them to stop
```

Záměrem je odeslat zprávu. Důležitá data z promluvy jsou samotná zpráva `telling them to stop`.  

## <a name="purpose-of-the-simple-entity"></a>Účel jednoduché entity
Účelem jednoduché entity je naučit službu LUIS, co je zpráva a kde ji v promluvě najít. Část promluvy, která představuje zprávu, se může u jednotlivých promluv lišit na základě volby slov a délky promluvy. Služba LUIS potřebuje příklady zpráv v různých promluvách napříč všemi záměry.  

Pro účely této jednoduché aplikace bude zpráva na konci promluvy. 

## <a name="create-a-new-app"></a>Vytvoření nové aplikace
1. Přihlaste se k webu [LUIS][LUIS]. Nezapomeňte se přihlásit k oblasti, ve které potřebujete publikovat koncové body služby LUIS.

2. Na webu [LUIS][LUIS] vyberte **Create new app** (Vytvořit novou aplikaci).  

    ![Seznam aplikací LUIS](./media/luis-quickstart-primary-and-secondary-data/app-list.png)

3. V automaticky otevíraném dialogovém okně zadejte název `MyCommunicator`. 

    ![Seznam aplikací LUIS](./media/luis-quickstart-primary-and-secondary-data/create-new-app-dialog.png)

4. Po dokončení tohoto procesu aplikace zobrazí stránku **Intents** (Záměry) se záměrem **None** (Žádný). 

    [![](media/luis-quickstart-primary-and-secondary-data/intents-list.png "Snímek obrazovky se stránkou Intents (Záměry) služby LUIS se záměrem None (Žádný)")](media/luis-quickstart-primary-and-secondary-data/intents-list.png#lightbox)

## <a name="create-a-new-intent"></a>Vytvoření nového záměru

1. Na stránce **Intents** (Záměry) vyberte **Create new intent** (Vytvořit nový záměr). 

    [![](media/luis-quickstart-primary-and-secondary-data/create-new-intent-button.png "Snímek obrazovky služby LUIS se zvýrazněným tlačítkem Create new intent (Vytvořit nový záměr)")](media/luis-quickstart-primary-and-secondary-data/create-new-intent-button.png#lightbox)

2. Zadejte název nového záměru `SendMessage`. Tento záměr by se měl vybrat vždy, když chce uživatel odeslat zprávu.

    Vytvořením záměru vytváříte primární kategorii informací, které chcete identifikovat. Pojmenováním kategorie umožníte všem ostatním aplikacím, které využívají výsledky dotazů ze služby LUIS, použít název této kategorie k vyhledání vhodné odpovědi nebo provedení odpovídající akce. Služba LUIS tyto dotazy nezodpoví, pouze identifikuje, na jaký typ informací směřuje dotaz v přirozeném jazyce. 

    ![Zadejte název záměru SendMessage (Odeslat zprávu).](./media/luis-quickstart-primary-and-secondary-data/create-new-intent-popup-dialog.png)

3. Do záměru `SendMessage` přidejte sedm promluv, které očekáváte, že uživatel bude požadovat, například:

    | Ukázkové promluvy|
    |--|
    |Reply with I got your message, I will have the answer tomorrow (Odpovědět zprávou „Dostal/a jsem vaši zprávu, odpovím vám zítra“)|
    |Send message of When will you be home? (Odeslat zprávu „Kdy budeš doma?“)|
    |Text that I am busy (Odeslat zprávu, že jsem zaneprázdněn/a)|
    |Tell them that it needs to be done today (Řekněte jim, že se to musí udělat ještě dnes)|
    |IM that I am driving and will respond later (Odeslat rychlou zprávu, že řídím a odpovím později)|
    |Compose message to David that says When was that? (Napsat zprávu Davidovi „Co to bylo?“)|
    |say greg hello (pozdravovat grega)|

    [![](media/luis-quickstart-primary-and-secondary-data/enter-utterances-on-intent-page.png "Snímek obrazovky služby LUIS se zadanými promluvami")](media/luis-quickstart-primary-and-secondary-data/enter-utterances-on-intent-page.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>Přidání promluv do záměru None (Žádný)

Aplikace LUIS aktuálně neobsahuje žádné promluvy pro záměr **None** (Žádný). Aplikace potřebuje promluvy, na které nechcete, aby odpovídala, takže potřebuje mít promluvy v záměru **None** (Žádný). Nenechávejte ho prázdný. 
    
1. Na levém panelu vyberte **Intents** (Záměry). 

    [![](media/luis-quickstart-primary-and-secondary-data/select-intent-link.png "Snímek obrazovky služby LUIS se zvýrazněným tlačítkem Intents (Záměry)")](media/luis-quickstart-primary-and-secondary-data/select-intent-link.png#lightbox)

2. Vyberte záměr **None** (Žádný). 

    [![](media/luis-quickstart-primary-and-secondary-data/select-none-intent.png "Snímek obrazovky s výběrem záměru None (Žádný)")](media/luis-quickstart-primary-and-secondary-data/select-none-intent.png#lightbox)

3. Přidejte tři promluvy, které může uživatel zadat, ale které nejsou pro aplikaci relevantní. Mezi dobré příklady promluv se záměrem **None** (Žádný) patří:

    | Ukázkové promluvy|
    |--|
    |Cancel! (Zrušit!)|
    |Good bye (Na shledanou)|
    |What is going on? (Co se děje?)|
    
    Pokud služba LUIS vrátí pro promluvu záměr **None** (Žádný), v aplikaci volající službu LUIS, jako je například chatbot, se chatbot může zeptat, jestli chce uživatel ukončit konverzaci. Pokud uživatel nechce ukončit konverzaci, chatbot může také nabídnout možnosti jejího dalšího směřování. 

    [![](media/luis-quickstart-primary-and-secondary-data/utterances-for-none-intent.png "Snímek obrazovky služby LUIS s promluvami pro záměr None (Žádný)")](media/luis-quickstart-primary-and-secondary-data/utterances-for-none-intent.png#lightbox)

## <a name="create-a-simple-entity-to-extract-message"></a>Vytvoření jednoduché entity pro extrakci zprávy 
1. V levé nabídce vyberte **Intents** (Záměry).

    ![Výběr odkazu Intents (Záměry)](./media/luis-quickstart-primary-and-secondary-data/select-intents-from-none-intent.png)

2. V seznamu záměrů vyberte `SendMessage`.

    ![Výběr záměru SendMessage (Odeslat zprávu)](./media/luis-quickstart-primary-and-secondary-data/select-sendmessage-intent.png)

3. V promluvě `Reply with I got your message, I will have the answer tomorrow` vyberte první slovo textu zprávy (`I`) a poslední slovo textu zprávy (`tomorrow`). Vyberou se všechna slova zprávy a v horní části se zobrazí rozevírací nabídka s textovým polem.

    [![](media/luis-quickstart-primary-and-secondary-data/select-words-in-utterance.png "Snímek obrazovky s vybranými slovy zprávy v promluvě")](media/luis-quickstart-primary-and-secondary-data/select-words-in-utterance.png#lightbox)

4. Do textového pole zadejte název entity `Message`.

    [![](media/luis-quickstart-primary-and-secondary-data/enter-entity-name-in-box.png "Snímek obrazovky se zadaným názvem entity v poli")](media/luis-quickstart-primary-and-secondary-data/enter-entity-name-in-box.png#lightbox)

5. V rozevírací nabídce vyberte **Create new entity** (Vytvořit novou entitu). Účelem entity je získat text, který představuje text zprávy. V této aplikaci LUIS je textová zpráva na konci promluvy, ale promluva i zpráva může mít libovolnou délku. 

    [![](media/luis-quickstart-primary-and-secondary-data/create-message-entity.png "Snímek obrazovky s vytvářením nové entity z promluvy")](media/luis-quickstart-primary-and-secondary-data/create-message-entity.png#lightbox)

6. V automaticky otevíraném okně je výchozí typ entity **Simple** (Jednoduchá) a název entity je `Message`. Ponechte tato nastavení a vyberte **Done** (Hotovo).

    ![Ověření typu entity](./media/luis-quickstart-primary-and-secondary-data/entity-type.png)

7. Když je teď entita vytvořená a jedna promluva označená, označte s použitím této entity i zbývající promluvy. Vyberte promluvu a pak vyberte první a poslední slovo zprávy. V rozevírací nabídce vyberte entitu `Message`. V entitě je teď označená zpráva. Pokračujte a označte všechny zprávy ve zbývajících promluvách.

    [![](media/luis-quickstart-primary-and-secondary-data/all-labeled-utterances.png "Snímek obrazovky s označenými zprávami ve všech promluvách")](media/luis-quickstart-primary-and-secondary-data/all-labeled-utterances.png#lightbox)

    Výchozí zobrazení promluv je zobrazení entit (**Entities view**). Vyberte ovládací prvek **Entities view** (Zobrazení entit) nad promluvami. V zobrazení tokenů (**Tokens view**) se zobrazí texty promluv. 

    [![](media/luis-quickstart-primary-and-secondary-data/tokens-view-of-utterances.png "Snímek obrazovky s promluvami v zobrazení tokenů")](media/luis-quickstart-primary-and-secondary-data/tokens-view-of-utterances.png#lightbox)

## <a name="train-the-luis-app"></a>Trénování aplikace LUIS
Služba LUIS nemá informace o změnách záměrů a entit (tedy modelu), dokud se nenatrénuje. 

1. V pravé horní části webu LUIS vyberte tlačítko **Train** (Trénovat).

    ![Výběr tlačítka Train (Trénovat)](./media/luis-quickstart-primary-and-secondary-data/train-button.png)

2. Trénování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

    ![Oznámení o úspěšném trénování](./media/luis-quickstart-primary-and-secondary-data/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Publikování aplikace a získání adresy URL koncového bodu
Abyste mohli využít předpověď služby LUIS v chatbotu nebo jiné aplikaci, musíte aplikaci publikovat. 

1. V pravé horní části webu LUIS vyberte tlačítko **Publish** (Publikovat). 

2. Vyberte slot Production (Produkční) a tlačítko **Publish** (Publikovat).

    [![](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png "Snímek obrazovky se stránkou Publish (Publikovat) a zvýrazněným tlačítkem Publish to production slot (Publikovat do produkčního slotu)")](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png#lightbox)

3. Publikování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Odeslání dotazu na koncový bod s jinou promluvou
V dolní části stránky **Publish** (Publikovat) vyberte odkaz na **koncový bod**. 

[![](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png "Snímek obrazovky se stránkou Publish (Publikovat) a zvýrazněným koncovým bodem")](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png#lightbox)

Tato akce otevře další okno prohlížeče s adresou URL koncového bodu v adresním řádku. Na konec adresy URL zadejte `text I'm driving and will be 30 minutes late to the meeting`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. Tato promluva není stejná jako žádná z označených promluv, proto je to dobrý test a měly by se vrátit promluvy `SendMessage`.

```
{
  "query": "text I'm driving and will be 30 minutes late to the meeting",
  "topScoringIntent": {
    "intent": "SendMessage",
    "score": 0.987501
  },
  "intents": [
    {
      "intent": "SendMessage",
      "score": 0.987501
    },
    {
      "intent": "None",
      "score": 0.111048922
    }
  ],
  "entities": [
    {
      "entity": "i ' m driving and will be 30 minutes late to the meeting",
      "type": "Message",
      "startIndex": 5,
      "endIndex": 58,
      "score": 0.162995353
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>Co tato aplikace LUIS udělala?
Tato aplikace s pouhými dvěma záměry a jednou entitou identifikovala záměr dotazu v přirozeném jazyce a vrátila data zprávy. 

Výsledek JSON identifikuje záměr `SendMessage` s nejvyšším skóre 0,987501. Všechna skóre jsou v rozmezí 1 až 0, přičemž čím blíže je skóre hodnotě 1, tím je lepší. Skóre záměru `None` je 0,111048922, což je mnohem blíže nule. 

Data zprávy obsahují typ (`Message`) i hodnotu (`i ' m driving and will be 30 minutes late to the meeting`). 

Váš chatbot má teď dostatek informací k určení primární akce `SendMessage` a parametru této akce, což je text zprávy. 

## <a name="where-is-this-luis-data-used"></a>Kde se tato data služby LUIS používají? 
Služba LUIS s tímto požadavkem skončila. Volající aplikace, například chatbot, může převzít výsledek topScoringIntent a data z entity a odeslat zprávu přes rozhraní API třetí strany. Pokud chatbot nebo volající aplikace nabízí další programové možnosti, služba LUIS tuto práci nedělá. Služba LUIS pouze určuje, co je záměrem uživatele. 

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už aplikaci LUIS nepotřebujete, odstraňte ji. Provedete to tak, že vyberete nabídku se třemi tečkami (...) vpravo od názvu aplikace v seznamu aplikací a vyberete **Delete** (Odstranit). V automaticky otevíraném dialogovém okně **Delete app?** (Odstranit aplikaci?) vyberte **Ok**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Informace o postupu při přidání hierarchické entity](luis-quickstart-intent-and-hier-entity.md)


<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
