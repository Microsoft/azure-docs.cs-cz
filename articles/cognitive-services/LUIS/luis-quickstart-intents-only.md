---
title: Vytvoření jednoduché aplikace se dvěma záměry – Azure | Microsoft Docs
description: V tomto rychlém startu zjistíte, jak vytvořit jednoduchou aplikaci LUIS využívající dva záměry a žádné entity k identifikaci promluv uživatelů.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 06/27/2018
ms.author: diberry
ms.openlocfilehash: 4be36e9d5b34c46138a657429680689014d0fd3d
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237770"
---
# <a name="tutorial-1-build-app-with-custom-domain"></a>Kurz: 1. Vytvoření aplikace s vlastní doménou
V tomto kurzu vytvoříte aplikaci předvádějící použití **záměrů** k určení _záměru_ uživatele na základě promluvy (textu), kterou do aplikace odešlou. Až budete hotovi, budete mít koncový bod služby LUIS spuštěný v cloudu.

Tato aplikace je nejjednodušší typ aplikace LUIS, protože z promluv neextrahuje data. Na základě promluvy pouze určuje záměr uživatele.

<!-- green checkmark -->
> [!div class="checklist"]
> * Vytvoření nové aplikace pro doménu lidských zdrojů (HR) 
> * Přidání záměru GetJobInformation (Informace o pracovní pozici)
> * Přidání ukázkových promluv do záměru GetJobInformation (Informace o pracovní pozici) 
> * Trénování a publikování aplikace
> * Odeslání dotazu na koncový bod aplikace a zobrazení odpovědi JSON ze služby LUIS
> * Přidání záměru ApplyForJob (Přihláška na pracovní pozici)
> * Přidání ukázkových promluv do záměru ApplyForJob (Přihláška na pracovní pozici) 
> * Opětovné trénování, publikování a dotazování koncového bodu 

Pro účely tohoto článku potřebujete bezplatný účet [LUIS](luis-reference-regions.md#luis-website), abyste mohli vytvořit svou aplikaci LUIS.

## <a name="purpose-of-the-app"></a>Účel aplikace
Tato aplikace má několik záměrů. První záměr **`GetJobInformation`** identifikuje, když chce uživatel získat informace o volných pracovních pozicích ve společnosti. Druhý záměr **`None`** identifikuje všechny ostatní typy promluv. V pozdější části tohoto rychlého startu se přidá třetí záměr `ApplyForJob`. 

## <a name="create-a-new-app"></a>Vytvoření nové aplikace
1. Přihlaste se k webu [LUIS](luis-reference-regions.md#luis-website). Nezapomeňte se přihlásit k [oblasti](luis-reference-regions.md#publishing-regions), ve které potřebujete publikovat koncové body služby LUIS.

2. Na webu [LUIS](luis-reference-regions.md#luis-website) vyberte **Create new app** (Vytvořit novou aplikaci).  

    [![](media/luis-quickstart-intents-only/app-list.png "Snímek obrazovky se stránkou My Apps (Moje aplikace)")](media/luis-quickstart-intents-only/app-list.png#lightbox)

3. V automaticky otevíraném dialogovém okně zadejte název `HumanResources`. Tato aplikace je určená pro dotazy týkající se oddělení lidských zdrojů ve vaší společnosti. Tento typ oddělení se stará o problémy související se zaměstnáním, jako jsou například pozice ve společnosti, které je potřeba obsadit.

    ![Nová aplikace LUIS](./media/luis-quickstart-intents-only/create-app.png)

4. Po dokončení tohoto procesu aplikace zobrazí stránku **Intents** (Záměry) se záměrem **None** (Žádný). 

    [![](media/luis-quickstart-intents-only/intents-list.png "Snímek obrazovky se stránkou se seznamem záměrů")](media/luis-quickstart-intents-only/intents-list.png#lightbox)

## <a name="create-getjobinformation-intention"></a>Vytvoření záměru GetJobInformation (Informace o pracovní pozici)
1. Vyberte **Create new intent** (Vytvořit nový záměr). Zadejte název nového záměru `GetJobInformation`. Tento záměr se předpoví pokaždé, když chce uživatel získat informace o volných pracovních pozicích ve vaší společnosti.

    ![](media/luis-quickstart-intents-only/create-intent.png "Snímek obrazovky s dialogovým oknem New intent (Nový záměr)")

    Vytvořením záměru vytváříte kategorii informací, které chcete identifikovat. Pojmenováním kategorie umožníte všem ostatním aplikacím, které využívají výsledky dotazů ze služby LUIS, použít název této kategorie k vyhledání vhodné odpovědi. Služba LUIS tyto dotazy nezodpovídá, pouze identifikuje, na jaký typ informací směřuje dotaz v přirozeném jazyce. 

2. Přidejte do tohoto záměru sedm promluv, které očekáváte, že uživatel bude požadovat, například:

    | Ukázkové promluvy|
    |--|
    |Any new jobs posted today? (Nějaké nové nabídky práce?)|
    |What positions are available for Senior Engineers? (Jaké pozice jsou otevřené na úrovni inženýr senior?)|
    |Is there any work with databases? (Nabízíte nějakou práci se s databázemi?)|
    |Looking for a new situation with responsibilities in accounting (Hledám něco nového v oboru účetnictví).|
    |Where is the job listings (Kde najdu seznam pracovních pozic)|
    |New jobs? (Nějaké nové pracovní pozice?)|
    |Are there any new positions in the Seattle office? (Nejsou na pobočce v Seattlu nějaké nové pozice?)|

    [![](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "Snímek obrazovky se zadáváním nových promluv pro záměr MyStore (Můj obchod)")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

3. Aplikace LUIS aktuálně neobsahuje žádné promluvy pro záměr **None** (Žádný). Aplikace potřebuje promluvy, na které neodpovídá. Nenechávejte ho prázdný. Na levém panelu vyberte **Intents** (Záměry). 

4. Vyberte záměr **None** (Žádný). Přidejte tři promluvy, které může uživatel zadat, ale které nejsou pro aplikaci relevantní. Pokud se aplikace týká nabídek pracovních pozic, mezi dobré příklady promluv se záměrem **None** (Žádný) patří:

    | Ukázkové promluvy|
    |--|
    |Barking dogs are annoying (Štěkající psi jsou otravní)|
    |Order a pizza for me (Objednej mi pizzu)|
    |Penguins in the ocean (Tučňáci v oceánech)|

    Pokud služba LUIS vrátí pro promluvu záměr **None** (Žádný), v aplikaci volající službu LUIS, jako je například chatbot, se chatbot může zeptat, jestli chce uživatel ukončit konverzaci. Pokud uživatel nechce ukončit konverzaci, chatbot může také nabídnout možnosti jejího dalšího směřování. 

## <a name="train-and-publish-the-app"></a>Trénování a publikování aplikace
1. V pravé horní části webu LUIS vyberte tlačítko **Train** (Trénovat). 

    ![Tlačítko Train (Trénovat)](./media/luis-quickstart-intents-only/train-button.png)

    Trénování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

    ![Stavový řádek trénování](./media/luis-quickstart-intents-only/trained.png)

2. V pravé horní části webu LUIS vyberte tlačítko **Publish** (Publikovat). Otevře se stránka Publish (Publikovat). Produkční slot je vybraný ve výchozím nastavení. Vedle volby produkčního slotu vyberte tlačítko **Publish** (Publikovat). Publikování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

    Před publikováním ani před testováním adresy URL koncového bodu není nutné na webu Azure Portal vytvářet klíč koncového bodu služby LUIS. Každá aplikace LUIS má bezplatný počáteční klíč pro vytváření obsahu. Nabízí neomezené vytváření obsahu a [několik požadavků přicházejících do koncového bodu](luis-boundaries.md#key-limits). 

## <a name="query-endpoint-for-getjobinformation-intent"></a>Odeslání dotazu na koncový bod pro záměr GetJobInformation (Informace o pracovní pozici)
1. V dolní části stránky **Publish** (Publikovat) vyberte odkaz na **koncový bod**. Tato akce otevře další okno prohlížeče s adresou URL koncového bodu v adresním řádku. 

2. Na konec adresy URL zadejte `I'm looking for a job with Natual Language Processing`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. Tato promluva není stejná jako žádná z ukázkových promluv v kroku 4, proto je to dobrý test a jako záměr s nejvyšším hodnocením by se měl vrátit záměr `GetJobInformation`. 

    ```
    {
      "query": "I'm looking for a job with Natual Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.8965092
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.8965092
        },
        {
          "intent": "None",
          "score": 0.147104025
        }
      ],
      "entities": []
    }
    ```

## <a name="create-applyforjob-intention"></a>Vytvoření záměru ApplyForJob (Přihláška na pracovní pozici)
Vraťte se na kartu prohlížeče s webem LUIS a vytvořte nový záměr pro přihlášku na pracovní pozici.

1. V nabídce vpravo nahoře vyberte **Build** (Sestavení) a vraťte se k vytváření aplikace.

2. V levé nabídce vyberte **Intents** (Záměry).

3. Vyberte **Create new intent** (Vytvořit nový záměr) a zadejte název `ApplyForJob`. 

    ![Dialogové okno služby LUIS pro vytvoření nového záměru](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

4. Přidejte do tohoto záměru několik promluv, které očekáváte, že uživatel bude požadovat, například:

    | Ukázkové promluvy|
    |--|
    |I want to apply for the new accounting job (Chci se přihlásit na novou pozici v účetnictví)|
    |Fill out application for Job 123456 (Vyplňte přihlášku pro pozici 123456)|
    |Submit resume for engineering position (Odeslat životopis pro pozici technického pracovníka)|
    |Here is my c.v. (Tady je můj životopis) for position 654234 (na pozici 654234)|
    |Job 567890 and my paperwork (Pracovní pozice 567890 a moje doklady)|

    [![](media/luis-quickstart-intents-only/utterance-applyforjob.png "Snímek obrazovky se zadáváním nových promluv pro záměr ApplyForJob (Přihláška na pracovní pozici)")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    Označený záměr má červený rámeček, protože si služba LUIS zatím není jistá, jestli je záměr správný. Trénováním aplikace sdělíte službě LUIS, že je záměr promluv správný. 

    Znovu proveďte [trénování a publikování](#train-and-publish-the-app). 

## <a name="query-endpoint-for-applyforjob-intent"></a>Odeslání dotazu na koncový bod pro záměr ApplyForJob (Přihláška na pracovní pozici)
V dolní části stránky **Publish** (Publikovat) vyberte odkaz na **koncový bod**. V novém okně prohlížeče zadejte na konec adresy URL `Can I submit my resume for job 235986`. 

    ```
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9166808
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9166808
        },
        {
          "intent": "GetJobInformation",
          "score": 0.07162977
        },
        {
          "intent": "None",
          "score": 0.0262826588
        }
      ],
      "entities": []
    }
    ```

## <a name="what-has-this-luis-app-accomplished"></a>Co tato aplikace LUIS udělala?
Tato aplikace pouze s několika záměry identifikovala dotaz v přirozeném jazyce se stejným záměrem ale jinými slovy. 

Výsledek JSON identifikuje záměr s nejvyšším skóre. Všechna skóre jsou v rozmezí 1 až 0, přičemž čím blíže je skóre hodnotě 1, tím je lepší. Skóre záměrů `GetJobInformation` a `None` jsou mnohem blíže nule. 

## <a name="where-is-this-luis-data-used"></a>Kde se tato data služby LUIS používají? 
Služba LUIS s tímto požadavkem skončila. Volající aplikace, například chabot, může převzít výsledek s nejvyšším skóre a buď vyhledat informace (které nejsou uložené ve službě LUIS) a zodpovědět dotaz, nebo konverzaci ukončit. Toto jsou programové možnosti pro chatbota nebo volající aplikaci. Služba LUIS tuto práci neprovádí. Služba LUIS pouze určuje, co je záměrem uživatele. 

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už aplikaci LUIS nepotřebujete, odstraňte ji. Provedete to tak, že v nabídce vlevo nahoře vyberete **My apps** (Moje aplikace). Vyberte tři tečky (***...***) vpravo od názvu aplikace v seznamu aplikací a potom vyberte **Delete** (Odstranit). V automaticky otevíraném dialogovém okně **Delete app?** (Odstranit aplikaci?) vyberte **Ok**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání předem připravených záměrů a entit do této aplikace](luis-tutorial-prebuilt-intents-entities.md)
