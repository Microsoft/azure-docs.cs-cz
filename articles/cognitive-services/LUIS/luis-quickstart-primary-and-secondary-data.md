---
title: Kurz vytvoření aplikace LUIS pro extrakci dat – Azure | Microsoft Docs
description: V tomto kurzu zjistíte, jak vytvořit jednoduchou aplikaci LUIS využívající záměry a jednoduchou entitu k extrakci strojově naučených dat.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/26/2018
ms.author: v-geberr
ms.openlocfilehash: b718ed505babd2df6487aecd3a87f17590aef2b9
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061243"
---
# <a name="tutorial-create-app-that-uses-simple-entity"></a>Kurz: Vytvoření aplikace využívající jednoduchou entitu
V tomto kurzu vytvoříte aplikaci, která ukazuje extrakci strojově naučených dat z promluvy pomocí **jednoduché** entity.

<!-- green checkmark -->
> [!div class="checklist"]
> * Vysvětlení jednoduchých entit 
> * Vytvoření nové aplikace LUIS pro doménu lidských zdrojů 
> * Přidání jednoduché entity k extrahování pracovních pozic z aplikace
> * Trénování a publikování aplikace
> * Odeslání dotazu na koncový bod aplikace a zobrazení odpovědi JSON ze služby LUIS
> * Přidání seznamu frází ke zlepšení signalizování slov týkajících se pracovních pozic
> * Trénování, publikování aplikace a opětovný dotaz na koncový bod

Pro účely tohoto článku potřebujete bezplatný účet [LUIS](luis-reference-regions.md#luis-website), abyste mohli vytvořit svou aplikaci LUIS.

## <a name="before-you-begin"></a>Než začnete
Pokud nemáte aplikaci pro lidské zdroje z kurzu k [hierarchickým entitám](luis-quickstart-intent-and-hier-entity.md), [naimportujte](create-new-app.md#import-new-app) JSON do nové aplikace na webu služby [LUIS](luis-reference-regions.md#luis-website). Aplikaci k importování najdete v úložišti [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-hier-HumanResources.json) na Githubu.

Pokud chcete zachovat původní aplikaci pro lidské zdroje, naklonujte verzi na stránce [Settings](luis-how-to-manage-versions.md#clone-a-version) (Nastavení) a pojmenujte ji `simple`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi.  

## <a name="purpose-of-the-app"></a>Účel aplikace
Tato aplikace ukazuje, jak získat data z promluvy. Představte si následující promluvy z chatbota:

|Promluva|Extrahovatelný název pracovní pozice|
|:--|:--|
|I want to apply for the new accounting job (Chci se přihlásit na novou pozici v účetnictví).|accounting (účetnictví)|
|Please submit my resume for the engineering position (Odešlete můj životopis pro pozici technický pracovník).|engineering (technický pracovník)|
|Fill out application for job 123456 (Vyplňte přihlášku pro pozici 123456).|123456|

V tomto kurzu přidáte novou entitu k extrahování názvu pracovní pozice. Možnost extrahovat číslo specifické pracovní pozice je popsána v [kurzu](luis-quickstart-intents-regex-entity.md) k regulárním výrazům. 

## <a name="purpose-of-the-simple-entity"></a>Účel jednoduché entity
Účelem jednoduché entity v této aplikaci LUIS je naučit službu LUIS, co je název pracovní pozice a kde ho v promluvě najít. Část promluvy, která představuje pracovní pozici, se může u jednotlivých promluv lišit na základě volby slov a délky promluvy. Služba LUIS potřebuje příklady pracovních pozic v různých promluvách napříč všemi záměry.  

Název pracovní pozice je obtížné určit, protože název může být podstatné jméno, sloveso nebo fráze složená z několika slov. Příklady:

|Pracovní pozice|
|--|
|engineer (technik)|
|software engineer (softwarový inženýr)|
|senior software engineer (softwarový inženýr senior)|
|engineering team lead (vedoucí technického týmu) |
|air traffic controller (letecký dispečer)|
|motor vehicle operator (řidič motorového vozidla)|
|ambulance driver (řidič sanitky)|
|tender (ošetřovatel)|
|extruder (plastikář)|
|millwright (strojní zámečník)|

Tato aplikace LUIS obsahuje názvy pracovních pozic v několika záměrech. Díky označování těchto slov v promluvách všech těchto záměrů služba LUIS lépe porozumí tomu, co je pracovní pozice a kde se v promluvách nachází.

## <a name="create-job-simple-entity"></a>Vytvoření jednoduché entity pracovní pozice

1. Ujistěte se, že je vaše aplikace pro lidské zdroje uvedená v části **Build** (Sestavení) služby LUIS. Do této části můžete přejít výběrem možnosti **Build** (Sestavit) v pravém horním řádku nabídek. 

    [ ![Snímek obrazovky aplikace LUIS se zvýrazněnou možností Build (Sestavit) na pravém horním navigačním panelu](./media/luis-quickstart-primary-and-secondary-data/hr-first-image.png)](./media/luis-quickstart-primary-and-secondary-data/hr-first-image.png#lightbox)

2. Na stránce **Intents** (Záměry) vyberte záměr **ApplyForJob** (Přihláška na pracovní pozici). 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-select-applyforjob.png "Snímek obrazovky aplikace LUIS se zvýrazněným záměrem ApplyForJob (Přihláška na pracovní pozici)")](media/luis-quickstart-primary-and-secondary-data/hr-select-applyforjob.png#lightbox)

3. V promluvě `I want to apply for the new accounting job` vyberte `accounting`, zadejte `Job` do horního pole v místní nabídce a pak v této místní nabídce vyberte **Create new entity** (Vytvořit novou entitu). 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "Snímek obrazovky aplikace LUIS se záměrem ApplyForJob (Přihláška na pracovní pozici) se zvýrazněným postupem vytvoření entity")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

4. V automaticky otevíraném okně ověřte název a typ entity a vyberte **Done** (Hotovo).

    ![Modální automaticky otevírané okno pro vytvoření jednoduché entity s názvem pracovní pozice a typem Simple (Jednoduchá)](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

5. V promluvě `Submit resume for engineering position` označte slovo engineering jako entitu Job (Pracovní pozice). Vyberte slovo engineering a pak v místní nabídce vyberte Job (Pracovní pozice). 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "Snímek obrazovky aplikace LUIS se zvýrazněnou označenou entitou pracovní pozice")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)

    Všechny promluvy jsou označené, ale pět promluv není dostatečný počet k tomu, aby se služba LUIS naučila slova a fráze týkající se pracovních pozic. Pracovní pozice, které používají číselnou hodnotu, nepotřebují další příklady, protože se u nich používá entita regulárního výrazu. U pracovních pozic, které jsou zadány jako slova nebo fráze, je potřeba alespoň dalších 15 příkladů. 

6. Přidejte další promluvy a označte slova a fráze označující pracovní pozice jako entitu **Job** (Pracovní pozice). Typy pracovních pozic jsou obecné jako pozice používané pracovní agenturou. Pokud byste chtěli používat pracovní pozice, které se vztahují ke konkrétnímu odvětví, měla by to slova označující tyto pracovní pozice odrážet. 

    |Promluva|Entita pracovní pozice|
    |:--|:--|
    |I'm applying for the Program Manager desk in R&D (Ucházím se o pozici Programového manažera ve vývoji).|Program Manager (Programový manažer)|
    |Here is my line cook application (Tady je moje přihláška na pozici kuchaře).|Kuchař|
    |My resume for camp counselor is attached (Přikládám životopis pro pozici táborového vedoucího).|camp counselor (táborový vedoucí)|
    |This is my c.v. for administrative assistant (Toto je můj životopis pro pozici asistenta v administrativě).|administrative assistant (asistent v administrativě)|
    |I want to apply for the management job in sales (Chci se ucházet o pozici manažer v prodeji).|management, sales (manažer v prodeji)|
    |This is my resume for the new accounting position (Tady je můj životopis pro novou pozici v účetnictví).|accounting (účetnictví)|
    |My application for barback is included (Přikládám přihlášku pro pomocníka barmana).|barback (pomocník barmana)|
    |I'm submitting my application for roofer and framer (Posílám přihlášku pro pozici tesař pokrývač).|tesař pokrývač|
    |My c.v. for bus driver is here (Tady je můj životopis pro pozici řidič autobusu).|bus driver (řidič autobusu)|
    |I'm a registered nurse. Here is my resume. (Jsem registrovaná zdravotní sestra. Tady je můj životopis.)|registered nurse (registrovaná zdravotní sestra)|
    |I would like to submit my paperwork for the teaching position I saw in the paper (Chtěla bych vám poslat doklady pro pozici vyučující, kterou jsem našla v novinách).|teaching (vyučující)|
    |This is my c.v. for the stocker post in fruits and vegetables (Tady je můj životopis pro doplňování zboží v sekci ovoce a zeleniny).|stocker (doplňování zboží)|
    |Apply for tile work (Přihláška na práci obkladače).|tile (obkladač)|
    |Attached resume for landscape architect (V příloze je životopis pro pozici zahradní architekt).|landscape architect (zahradní architekt)|
    |My curriculum vitae for professor of biology is enclosed (Přikládám curriculum vitae pro pozici profesor biologie).|professor of biology (profesor biologie)|
    |I would like to apply for the position in photography (Chtěl bych se přihlásit na pozici fotograf).|photography (fotograf)|git 

## <a name="label-entity-in-example-utterances-for-getjobinformation-intent"></a>Označení entity v příkladech promluv pro záměr GetJobInformation (Informace o pracovní pozici)
1. V levé nabídce vyberte **Intents** (Záměry).

2. V seznamu záměrů vyberte **GetJobInformation** (Informace o pracovní pozici). 

3. Označte pracovní pozice v příkladech promluv:

    |Promluva|Entita pracovní pozice|
    |:--|:--|
    |Is there any work in databases? (Nabízíte nějakou práci se zaměřením na databáze?)|databases (databáze)|
    |Looking for a new situation with responsibilities in accounting (Hledám něco nového v oboru účetnictví).|accounting (účetnictví)|
    |What positions are available for senior engineers? (Jaké pozice jsou otevřené na úrovni inženýr senior?)|senior engineers (inženýr senior)|

    K dispozici jsou další příklady promluv, ale neobsahují slova týkající se pracovních pozic.

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

Tato akce otevře další okno prohlížeče s adresou URL koncového bodu v adresním řádku. Na konec adresy URL zadejte `Here is my c.v. for the programmer job`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. Tato promluva není stejná jako žádná z označených promluv, proto je to dobrý test a měly by se vrátit promluvy `ApplyForJob`.

```JSON
{
  "query": "Here is my c.v. for the programmer job",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.9826467
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.9826467
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0218927357
    },
    {
      "intent": "MoveEmployee",
      "score": 0.007849265
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00349470088
    },
    {
      "intent": "Utilities.Confirm",
      "score": 0.00348804821
    },
    {
      "intent": "None",
      "score": 0.00319909188
    },
    {
      "intent": "FindForm",
      "score": 0.00222647213
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00211193133
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00172086991
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.00138010911
    }
  ],
  "entities": [
    {
      "entity": "programmer",
      "type": "Job",
      "startIndex": 24,
      "endIndex": 33,
      "score": 0.5230502
    }
  ]
}
```

## <a name="names-are-tricky"></a>S názvy je to složitější
Aplikace LUIS našla s vysokou spolehlivostí správný záměr a extrahovala název pracovní pozice, ale s názvy je to složitější. Vyzkoušejte promluvu `This is the lead welder paperwork`.  

V následujícím souboru JSON vrátí služba LUIS správný záměr `ApplyForJob`, ale neextrahuje název pracovní pozice `lead welder`. 

```JSON
{
  "query": "This is the lead welder paperwork.",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.468558252
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.468558252
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0102701457
    },
    {
      "intent": "MoveEmployee",
      "score": 0.009442534
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00639619166
    },
    {
      "intent": "None",
      "score": 0.005859333
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.005087704
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00315379258
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00259344373
    },
    {
      "intent": "FindForm",
      "score": 0.00193389168
    },
    {
      "intent": "Utilities.Confirm",
      "score": 0.000420796918
    }
  ],
  "entities": []
}
```

Protože názvem může být cokoli, predikuje služba LUIS entity přesněji, pokud má seznam frází se slovy pro zlepšení signalizování.

## <a name="to-boost-signal-add-jobs-phrase-list"></a>Přidání seznamu frází pro zlepšení signalizování
Otevřete soubor [jobs-phrase-list.csv](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) z úložiště LUIS-Samples na Githubu. V seznamu je více než tisíc slov a frází označujících pracovní pozice. Seznam si prohlédněte a vyhledejte slova označující pracovní pozice, která pro vás mají smysl. Pokud potřebná slova nebo fráze v seznamu nenajdete, přidejte si vlastní.

1. V části **Build** (Sestavit) aplikace LUIS vyberte možnost **Phrase lists** (Seznamy frází) v nabídce **Improve app performance** (Zvýšení výkonu aplikací).

    [![](media/luis-quickstart-primary-and-secondary-data/hr-select-phrase-list-left-nav.png "Snímek obrazovky se zvýrazněným tlačítkem Phrase lists (Seznamy frází) na levém navigačním panelu")](media/luis-quickstart-primary-and-secondary-data/hr-select-phrase-list-left-nav.png#lightbox)

2. Vyberte **Create new phrase list** (Vytvořit nový seznam frází). 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-new-phrase-list.png "Snímek obrazovky se zvýrazněným tlačítkem Create new phrase list (Vytvořit nový seznam frází)")](media/luis-quickstart-primary-and-secondary-data/hr-create-new-phrase-list.png#lightbox)

3. Nový seznam frází pojmenujte `Jobs` a zkopírujte seznam ze souboru jobs-phrase-list.csv do textového pole **Values** (Hodnoty). Stiskněte klávesu ENTER. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "Snímek obrazovky s automaticky otevíraným oknem pro vytvoření nového seznamu frází")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Pokud chcete do seznamu frází přidat další slova, prohlédněte si doporučená slova a přidejte všechna, která jsou relevantní. 

4. Výběrem tlačítka **Save** (Uložit) seznam frází aktivujte.

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "Snímek obrazovky s automaticky otevíraným oknem pro vytvoření nového seznamu frází se slovy v polích s hodnotami seznamu frází")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

5. Aplikaci [natrénujte](#train-the-luis-app) a znovu [publikujte](#publish-the-app-to-get-the-endpoint-URL), aby tento seznam frází používala.

6. Znovu zadejte dotaz na koncovém bodě pomocí stejné promluvy: `This is the lead welder paperwork.`

    Odpověď JSON obsahuje extrahovanou entitu:

    ```JSON
    {
        "query": "This is the lead welder paperwork.",
        "topScoringIntent": {
            "intent": "ApplyForJob",
            "score": 0.920025647
        },
        "intents": [
            {
            "intent": "ApplyForJob",
            "score": 0.920025647
            },
            {
            "intent": "GetJobInformation",
            "score": 0.003800706
            },
            {
            "intent": "Utilities.StartOver",
            "score": 0.00299335527
            },
            {
            "intent": "MoveEmployee",
            "score": 0.0027167045
            },
            {
            "intent": "None",
            "score": 0.00259556063
            },
            {
            "intent": "FindForm",
            "score": 0.00224019377
            },
            {
            "intent": "Utilities.Stop",
            "score": 0.00200693542
            },
            {
            "intent": "Utilities.Cancel",
            "score": 0.00195913855
            },
            {
            "intent": "Utilities.Help",
            "score": 0.00162656687
            },
            {
            "intent": "Utilities.Confirm",
            "score": 0.0002851904
            }
        ],
        "entities": [
            {
            "entity": "lead welder",
            "type": "Job",
            "startIndex": 12,
            "endIndex": 22,
            "score": 0.8295959
            }
        ]
    }
    ```

## <a name="phrase-lists"></a>Seznamy frází
Přidání seznamu frází zlepšilo signalizování slov v seznamu, ale **nepoužívá** se jako přesná shoda. Seznam frází obsahuje několik pracovních pozic, kde je prvním slovem `lead` a také obsahuje pracovní pozici `welder`, ale neobsahuje pozici `lead welder`. Tento seznam frází s pracovními pozicemi nemusí být úplný. Když budete pravidelně [kontrolovat promluvy v koncovém bodě](label-suggested-utterances.md) a najdete další slova označující pracovní pozice, přidejte je do svého seznamu frází. Pak opakujte trénování a publikování.

## <a name="what-has-this-luis-app-accomplished"></a>Co tato aplikace LUIS udělala?
Tato aplikace s jednoduchou entitou a seznamem frází se slovy identifikovala záměr dotazu v přirozeném jazyce a vrátila data zprávy. 

Váš chatbot má teď dostatek informací k určení primární akce přihlášení se na pracovní pozici a parametru této akce – o jakou pozici se jedná. 

## <a name="where-is-this-luis-data-used"></a>Kde se tato data služby LUIS používají? 
Služba LUIS s tímto požadavkem skončila. Volající aplikace, například chatbot, může převzít výsledek topScoringIntent a data z entity a pomocí rozhraní API od jiného výrobce odeslat informace o pracovní pozici zástupci oddělení lidských zdrojů. Pokud chatbot nebo volající aplikace nabízí další programové možnosti, služba LUIS tuto práci nedělá. Služba LUIS pouze určuje, co je záměrem uživatele. 

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už aplikaci LUIS nepotřebujete, odstraňte ji. Provedete to tak, že vyberete nabídku se třemi tečkami (...) vpravo od názvu aplikace v seznamu aplikací a vyberete **Delete** (Odstranit). V automaticky otevíraném dialogovém okně **Delete app?** (Odstranit aplikaci?) vyberte **Ok**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Jak přidat předem připravenou entitu klíčové fráze](luis-quickstart-intent-and-key-phrase.md)