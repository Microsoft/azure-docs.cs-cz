---
title: 'Kurz 7: Jednoduchá entita se seznamem frází ve službě LUIS'
titleSuffix: Azure Cognitive Services
description: Extrahování strojově naučených dat z promluvy
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 941c29506aa8f17dcb6262495b28dd26e78194d5
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47036048"
---
# <a name="tutorial-7-extract-names-with-simple-entity-and-phrase-list"></a>Kurz 7: Extrahování názvů pomocí jednoduché entity a seznamu frází

V tomto kurzu extrahujete strojově naučená data názvu pracovní pozice z promluvy pomocí **jednoduché** entity. Za účelem zvýšení přesnosti extrakce přidáte seznam termínů, které jsou pro jednoduchou entitu specifické.

V tomto kurzu přidáte novou jednoduchou entitu k extrahování názvu pracovní pozice. Účelem jednoduché entity v této aplikaci LUIS je naučit službu LUIS, co je název pracovní pozice a kde ho v promluvě najít. Část promluvy, která představuje název pracovní pozice, se může u jednotlivých promluv lišit na základě volby slov a délky promluvy. LUIS potřebuje příklady názvů pracovních pozic ve všech záměrech, které používají názvy pracovních pozic.  

Jednoduchá entita je vhodná pro tento typ dat, když platí následující:

* Data představují jeden koncept.
* Data nejsou správně naformátovaná, jako je tomu například u regulárních výrazů.
* Data nejsou běžná, nejedná se například o předem připravenou entitu telefonních čísel nebo dat.
* Data přesně neodpovídají seznamu známých slov, jako je tomu například u entity seznamu.
* Data neobsahují jiné datové položky, nejedná se například o složenou nebo hierarchickou entitu.

**V tomto kurzu se naučíte:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Použít existující ukázkovou aplikaci
> * Přidání jednoduché entity k extrahování pracovních pozic z aplikace
> * Přidání seznamu frází ke zlepšení signalizování slov týkajících se pracovních pozic
> * Trénování 
> * Publikování 
> * Získat záměry a entity z koncového bodu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Použití existující aplikace

Pokračujte s aplikací **HumanResources**, kterou jste vytvořili v posledním kurzu. 

Pokud tuto aplikaci nemáte, proveďte tyto kroky:

1.  Stáhněte a uložte si [soubor JSON aplikace](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-composite-HumanResources.json).

2. Naimportujte JSON do nové aplikace.

3. V části **Manage** (Správa) na kartě **Versions** (Verze) naklonujte verzi a pojmenujte ji `simple`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. Název verze je součástí cesty URL, a proto může obsahovat jenom znaky podporované v adresách URL.

## <a name="simple-entity"></a>Jednoduchá entita
Jednoduchá entita rozpozná jeden koncept dat obsažený ve slovech nebo frázích.

Představte si následující promluvy z chatbota:

|Promluva|Extrahovatelný název pracovní pozice|
|:--|:--|
|I want to apply for the new accounting job (Chci se přihlásit na novou pozici v účetnictví).|accounting (účetnictví)|
|Submit my resume for the engineering position (Odešlete můj životopis pro pozici technický pracovník).|engineering (technický pracovník)|
|Fill out application for job 123456 (Vyplňte přihlášku pro pozici 123456).|123456|

Název pracovní pozice je obtížné určit, protože název může být podstatné jméno, sloveso nebo fráze složená z několika slov. Příklad:

|Úlohy|
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

Tato aplikace LUIS obsahuje názvy pracovních pozic v několika záměrech. Díky označování těchto slov v promluvách všech těchto záměrů služba LUIS lépe porozumí tomu, co je název pracovní pozice a kde se v promluvách nachází.

Jakmile označíte entity v ukázkových promluvách, je důležité přidat seznam frází, abyste zesílili signál jednoduché entity. Seznam frází **neslouží** jako přesná shoda a nemusí zahrnovat všechny možné očekávané hodnoty. 

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Na stránce **Intents** (Záměry) vyberte záměr **ApplyForJob** (Přihláška na pracovní pozici). 

3. V promluvě `I want to apply for the new accounting job` vyberte `accounting`, zadejte `Job` do horního pole v místní nabídce a pak v této místní nabídce vyberte **Create new entity** (Vytvořit novou entitu). 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "Snímek obrazovky aplikace LUIS se záměrem ApplyForJob (Přihláška na pracovní pozici) se zvýrazněným postupem vytvoření entity")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

4. V automaticky otevíraném okně ověřte název a typ entity a vyberte **Done** (Hotovo).

    ![Modální automaticky otevírané okno pro vytvoření jednoduché entity s názvem pracovní pozice a typem Simple (Jednoduchá)](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

5. V promluvě `Submit resume for engineering position` označte slovo `engineering` jako entitu Job (Pracovní pozice). Vyberte slovo `engineering` a pak v místní nabídce vyberte **Job** (Pracovní pozice). 

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

## <a name="label-entity-in-example-utterances"></a>Popisování entity v ukázkových promluvách

Popisování, neboli _označení_, entity ukáže službě LUIS, kde se entita v ukázkových promluvách nachází.

1. V levé nabídce vyberte **Intents** (Záměry).

2. V seznamu záměrů vyberte **GetJobInformation** (Informace o pracovní pozici). 

3. Označte pracovní pozice v příkladech promluv:

    |Promluva|Entita pracovní pozice|
    |:--|:--|
    |Is there any work in databases? (Nabízíte nějakou práci se zaměřením na databáze?)|databáze|
    |Looking for a new situation with responsibilities in accounting (Hledám něco nového v oboru účetnictví).|accounting (účetnictví)|
    |What positions are available for senior engineers? (Jaké pozice jsou otevřené na úrovni inženýr senior?)|senior engineers (inženýr senior)|

    K dispozici jsou další příklady promluv, ale neobsahují slova týkající se pracovních pozic.

## <a name="train"></a>Trénování

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publikování

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Získání záměru a entit z koncového bodu 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Na konec adresy URL zadejte `Here is my c.v. for the programmer job`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. Tato promluva není stejná jako žádná z označených promluv, proto je to dobrý test a měly by se vrátit promluvy `ApplyForJob`.

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
    
    Služba LUIS našla správný záměr, **ApplyForJob**, a extrahovala správnou entitu, **Job**, s hodnotou `programmer`.


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

## <a name="to-boost-signal-add-phrase-list"></a>Zesílení signálu přidáním seznamu frází

Otevřete soubor [jobs-phrase-list.csv](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) z úložiště LUIS-Samples na Githubu. V seznamu je více než tisíc slov a frází označujících pracovní pozice. Seznam si prohlédněte a vyhledejte slova označující pracovní pozice, která pro vás mají smysl. Pokud potřebná slova nebo fráze v seznamu nenajdete, přidejte si vlastní.

1. V části **Build** (Sestavit) aplikace LUIS vyberte možnost **Phrase lists** (Seznamy frází) v nabídce **Improve app performance** (Zvýšení výkonu aplikací).

2. Vyberte **Create new phrase list** (Vytvořit nový seznam frází). 

3. Nový seznam frází pojmenujte `Job` a zkopírujte seznam ze souboru jobs-phrase-list.csv do textového pole **Values** (Hodnoty). Stiskněte klávesu ENTER. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "Snímek obrazovky s automaticky otevíraným oknem pro vytvoření nového seznamu frází")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Pokud chcete do seznamu frází přidat další slova, prohlédněte si část **Related Values** (Související hodnoty) a přidejte všechna slova, která jsou relevantní. 

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

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu aplikace Human Resources používá strojově naučenou jednoduchou entitu k vyhledání názvů pracovních pozic v promluvách. Vzhledem k tomu, že názvy pracovních pozic mohou obsahovat celou škálu slov a frází, potřebovala aplikace seznam frází, pomocí nichž lépe rozpozná slova názvu pracovní pozice. 

> [!div class="nextstepaction"]
> [Přidání předem připravené entity klíčové fráze](luis-quickstart-intent-and-key-phrase.md)