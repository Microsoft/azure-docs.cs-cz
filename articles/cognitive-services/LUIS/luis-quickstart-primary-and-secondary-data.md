---
title: Jednoduché entity, seznam frází
titleSuffix: Azure Cognitive Services
description: V tomto kurzu se extrakce dat zjištěné počítače pracovní úlohy pro název z utterance pomocí jednoduchého entity. Za účelem zvýšení přesnosti extrakce přidáte seznam termínů, které jsou pro jednoduchou entitu specifické.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 9da2f8fa345ad3447d7c3344772458c451cf427e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867235"
---
# <a name="tutorial-extract-names-with-simple-entity-and-a-phrase-list"></a>Kurz: Extrahovat názvy s jednoduchou entitu a seznam fráze

V tomto kurzu extrahujete strojově naučená data názvu pracovní pozice z promluvy pomocí **jednoduché** entity. Za účelem zvýšení přesnosti extrakce přidáte seznam termínů, které jsou pro jednoduchou entitu specifické.

Jednoduchá entita rozpozná jeden koncept dat obsažený ve slovech nebo frázích.

**V tomto kurzu se naučíte:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importovat ukázková aplikace
> * Přidat jednoduchou entitu 
> * Přidat seznam frázi a zvýšit tak signál slova
> * Trénování 
> * Publikování 
> * Zjistit záměry a entity z koncového bodu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]


## <a name="simple-entity"></a>Jednoduchá entita

V tomto kurzu přidáte novou jednoduchou entitu k extrahování názvu pracovní pozice. Účelem jednoduché entity v této aplikaci LUIS je naučit službu LUIS, co je název pracovní pozice a kde ho v promluvě najít. Část promluvy, která představuje název pracovní pozice, se může u jednotlivých promluv lišit na základě volby slov a délky promluvy. LUIS potřebuje příklady názvů pracovních pozic ve všech záměrech, které používají názvy pracovních pozic.  

Jednoduchá entita je vhodná pro tento typ dat, když platí následující:

* Data představují jeden koncept.
* Data nejsou správně naformátovaná, jako je tomu například u regulárních výrazů.
* Data nejsou běžná, nejedná se například o předem připravenou entitu telefonních čísel nebo dat.
* Data přesně neodpovídají seznamu známých slov, jako je tomu například u entity seznamu.
* Data neobsahují jiné datové položky, nejedná se například o složenou nebo hierarchickou entitu.

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

## <a name="import-example-app"></a>Importovat ukázková aplikace

1.  Stáhněte a uložte [souboru JSON aplikace](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/intentonly.json) z kurzu záměry.

2. Naimportujte soubor JSON do nové aplikace.

3. V části **Manage** (Správa) na kartě **Versions** (Verze) naklonujte verzi a pojmenujte ji `simple`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. Název verze je součástí cesty URL, a proto smí obsahovat jenom znaky, které jsou platné v adresách URL.

## <a name="mark-entities-in-example-utterances-of-an-intent"></a>Označování entit v příkladu projevy záměru

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Na stránce **Intents** (Záměry) vyberte záměr **ApplyForJob** (Přihláška na pracovní pozici). 

1. V promluvě `I want to apply for the new accounting job` vyberte `accounting`, zadejte `Job` do horního pole v místní nabídce a pak v této místní nabídce vyberte **Create new entity** (Vytvořit novou entitu). 

    [![Snímek obrazovky služby LUIS se záměrem "ApplyForJob" s vytvořit entitu kroky zvýrazněnou](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "snímek obrazovky služby LUIS se záměrem \"ApplyForJob\" s vytvořit entitu kroky zvýrazněnou")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

1. V automaticky otevíraném okně ověřte název a typ entity a vyberte **Done** (Hotovo).

    ![Modální automaticky otevírané okno pro vytvoření jednoduché entity s názvem pracovní pozice a typem Simple (Jednoduchá)](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

1. Ve zbývající projevy označit slov úlohy související s **úlohy** entity, když vyberete slovo nebo frázi, poté vyberete **úlohy** v místní nabídce. 

    [![Snímek obrazovky LUIS označování entit úlohy zvýrazněný](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "snímek obrazovky LUIS označování entit úlohy zvýrazněný")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)


## <a name="add-more-example-utterances-and-mark-entity"></a>Přidejte další příklad projevy a označte entity

Jednoduché entity musí mnoho příkladů abyste měli vysokou spolehlivostí předpovědí. 
 
1. Přidejte další promluvy a označte slova a fráze označující pracovní pozice jako entitu **Job** (Pracovní pozice). 

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

## <a name="mark-job-entity-in-other-intents"></a>Označit úlohu entity v jiných záměry

1. V levé nabídce vyberte **Intents** (Záměry).

1. V seznamu záměrů vyberte **GetJobInformation** (Informace o pracovní pozici). 

1. Popisek úlohy v příkladu projevy

    Pokud jsou v jedné záměr než jiné záměr projevů další příklad, tohoto záměru má vyšší pravděpodobnost, že se nejvyšší předpokládané intext. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Trénování aplikace, takže můžete otestovat změny k příslušnému záměru 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publikování aplikace, tedy dotazovatelné z koncového bodu trénovaného modelu

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Získání předpovědi záměr a entity z koncového bodu 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Na konec adresy URL zadejte `Here is my c.v. for the engineering job`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. Tato promluva není stejná jako žádná z označených promluv, proto je to dobrý test a měly by se vrátit promluvy `ApplyForJob`.

    ```json
    {
      "query": "Here is my c.v. for the engineering job",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.98052007
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.98052007
        },
        {
          "intent": "GetJobInformation",
          "score": 0.03424581
        },
        {
          "intent": "None",
          "score": 0.0015820954
        }
      ],
      "entities": [
        {
          "entity": "engineering",
          "type": "Job",
          "startIndex": 24,
          "endIndex": 34,
          "score": 0.668959737
        }
      ]
    }
    ```
    
    Služba LUIS našla správný záměr, **ApplyForJob**, a extrahovala správnou entitu, **Job**, s hodnotou `engineering`.


## <a name="names-are-tricky"></a>S názvy je to složitější
Aplikace LUIS našla s vysokou spolehlivostí správný záměr a extrahovala název pracovní pozice, ale s názvy je to složitější. Vyzkoušejte promluvu `This is the lead welder paperwork`.  

V následujícím souboru JSON vrátí služba LUIS správný záměr `ApplyForJob`, ale neextrahuje název pracovní pozice `lead welder`. 

```json
{
  "query": "This is the lead welder paperwork",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.860295951
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.860295951
    },
    {
      "intent": "GetJobInformation",
      "score": 0.07265678
    },
    {
      "intent": "None",
      "score": 0.00482481951
    }
  ],
  "entities": []
}
```

Protože názvem může být cokoli, predikuje služba LUIS entity přesněji, pokud má seznam frází se slovy pro zlepšení signalizování.

## <a name="to-boost-signal-of-the-job-related-words-add-a-phrase-list-of-job-related-words"></a>Chcete-li boost signál slov souvisejících s úlohami přidejte frázi seznam souvisejících s úlohami slov

Otevřít [úlohy. frázi list.csv](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) z úložiště GitHub Azure-Samples. Seznam je více než 1 000 úloh slova a slovní spojení. Seznam si prohlédněte a vyhledejte slova označující pracovní pozice, která pro vás mají smysl. Pokud potřebná slova nebo fráze v seznamu nenajdete, přidejte si vlastní.

1. V části **Build** (Sestavit) aplikace LUIS vyberte možnost **Phrase lists** (Seznamy frází) v nabídce **Improve app performance** (Zvýšení výkonu aplikací).

1. Vyberte **Create new phrase list** (Vytvořit nový seznam frází). 

1. Nový seznam frází pojmenujte `JobNames` a zkopírujte seznam ze souboru jobs-phrase-list.csv do textového pole **Values** (Hodnoty). Stiskněte klávesu ENTER. 

    [![Snímek obrazovky vytvořit dialogové okno Nový seznam frázi rozbalovací](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "snímek obrazovky vytvořit dialogové okno Nový seznam frázi místní")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Pokud chcete do seznamu frází přidat další slova, prohlédněte si část **Related Values** (Související hodnoty) a přidejte všechna slova, která jsou relevantní. 

1. Výběrem tlačítka **Save** (Uložit) seznam frází aktivujte.

    [![Snímek obrazovky vytvořit dialogové okno Nový seznam frázi místní s slova v seznamu hodnot frázi](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "snímek obrazovky s novou frázi seznamu dialogové okno Vytvořit místní obsahující slova v seznamu hodnot fráze")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

1. Trénování a publikování aplikace znovu a použijte seznam frázi.

1. Znovu zadejte dotaz na koncovém bodě pomocí stejné promluvy: `This is the lead welder paperwork.`

    Odpověď JSON obsahuje extrahovanou entitu:

    ```json
      {
      "query": "This is the lead welder paperwork.",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.983076453
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.983076453
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0120766377
        },
        {
          "intent": "None",
          "score": 0.00248388131
        }
      ],
      "entities": [
        {
          "entity": "lead welder",
          "type": "Job",
          "startIndex": 12,
          "endIndex": 22,
          "score": 0.8373154
        }
      ]
    }
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Související informace

* [Záměry bez kurzu entity](luis-quickstart-intents-only.md)
* [Jednoduché entity](luis-concept-entity-types.md) koncepční informace
* [Seznam frází](luis-concept-feature.md) koncepční informace
* [Trénování](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Testování v portálu služby LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Další postup

V tomto kurzu aplikace Human Resources používá strojově naučenou jednoduchou entitu k vyhledání názvů pracovních pozic v promluvách. Vzhledem k tomu, že názvy pracovních pozic mohou obsahovat celou škálu slov a frází, potřebovala aplikace seznam frází, pomocí nichž lépe rozpozná slova názvu pracovní pozice. 

> [!div class="nextstepaction"]
> [Přidání předem připravené entity klíčové fráze](luis-quickstart-intent-and-key-phrase.md)
