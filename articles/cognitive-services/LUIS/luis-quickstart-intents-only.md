---
title: 'Kurz: Předpověď záměrů – LUIS'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvoříte vlastní aplikaci, která předpovídá záměr uživatele. Tato aplikace představuje nejjednodušší typ aplikace LUIS, protože neextrahuje různé datové prvky z textu promluvy, jako jsou e-mailové adresy nebo kalendářní data.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 7139876f64841a877e688ec6faf03597c527d1f2
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375823"
---
# <a name="tutorial-build-luis-app-to-determine-user-intentions"></a>Kurz: Sestavování aplikace LUIS pro určení záměrů uživatelů

V tomto kurzu vytvoříte vlastní aplikaci pro lidské zdroje, která předpovídá záměr uživatele na základě promluvy (textu). 

**V tomto kurzu se naučíte:**

> [!div class="checklist"]
> * Vytvoření nové aplikace 
> * Vytvořit záměry
> * Přidat ukázkové promluvy
> * Vytrénovat aplikaci
> * Publikování aplikace
> * Získat záměr z koncového bodu


[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="user-intentions-as-intents"></a>Záměry uživatelů jako záměrů

Účelem aplikace je určit záměr konverzací, text přirozeného jazyka: 

`Are there any new positions in the Seattle office?`

Tyto záměry jsou zařazené do kategorie **Intents** (Záměry). 

Tato aplikace má několik záměrů. 

|Záměr|Účel|
|--|--|
|ApplyForJob|Určete, jestli se uživatel pro úlohu používá.|
|GetJobInformation|Určete, jestli uživatel hledá informace o úlohách v obecné nebo konkrétní úloze.|
|Žádné|Určení, jestli uživatel žádá o nějakou aplikaci, nemá odpovídat. Tento záměr, pokud je k dispozici jako součást vytváření aplikace a nelze jej odstranit. |

## <a name="create-a-new-app"></a>Vytvoření nové aplikace

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-job-information"></a>Vytvořit záměr pro informace o úloze

1. Vyberte **Create new intent** (Vytvořit nový záměr). Zadejte název nového záměru `GetJobInformation`. Předpokládá se, že pokud uživatel potřebuje informace o otevřených úlohách ve společnosti, bude tento záměr předpovězen. 

    ![Snímek obrazovky z Language Understanding (LUIS) dialogové okno záměru nového](media/luis-quickstart-intents-only/create-intent.png "nové záměru dialogové okno snímek obrazovky z Language Understanding (LUIS)")

1. Vyberte **Done** (Hotovo).

2. Do tohoto záměru přidejte několik příkladů projevy, u kterých očekáváte, že se uživatel zeptá:

    | Ukázkové promluvy|
    |--|
    |Any new jobs posted today? (Nějaké nové nabídky práce?)|
    |Are there any new positions in the Seattle office? (Nejsou na pobočce v Seattlu nějaké nové pozice?)|
    |Jsou pro inženýry otevřené nějaké úlohy vzdáleného pracovního procesu nebo zaměstnání?|
    |Is there any work with databases? (Nabízíte nějakou práci se s databázemi?)|
    |Hledám spolupracovní situaci v kanceláři Tampa.|
    |Je k dispozici interně pobočka v síti San Francisco?|
    |Je pro lidi v rámci školy nějaká pracovní doba?|
    |Looking for a new situation with responsibilities in accounting (Hledám něco nového v oboru účetnictví).|
    |Hledání úlohy v New York City pro dvojjazyčné reproduktory.|
    |Hledáte novou situaci s odpovědností v účetnictví.|
    |New jobs? (Nějaké nové pracovní pozice?)|
    |Zobrazit všechny úlohy pro inženýry, kteří byli přidáni za poslední 2 dny.|
    |Dnešní příspěvky na úlohy?|
    |Jaké jsou v pobočce v Londýně otevřené pozice pro monitorování účtů?|
    |What positions are available for Senior Engineers? (Jaké pozice jsou otevřené na úrovni inženýr senior?)|
    |Where is the job listings (Kde najdu seznam pracovních pozic)|

    [![Snímek obrazovky zadání nového projevy pro MyStore záměr](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "snímek obrazovky zadání nového projevy pro MyStore záměr")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

    Poskytnutím _ukázkového projevyu_budete školením Luis o tom, jaké druhy projevyy by se měly pro tento záměr odhadnout. 

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    

## <a name="add-example-utterances-to-the-none-intent"></a>Přidat příklad projevy k záměru None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>Výuka aplikace před testováním nebo publikováním

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publikování aplikace pro dotaz z koncového bodu

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-from-the-endpoint"></a>Získání předpovědi záměru z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Přejděte na konec adresy URL v panelu adresy a zadejte `I'm looking for a job with Natural Language Processing`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. Tato promluva není stejná jako některá z ukázkových promluv. Jde o vhodný test a jako záměr s nejvyšším skóre by se měl vrátit záměr `GetJobInformation`. 

    ```JSON
    {
      "query": "I'm looking for a job with Natural Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.9923871
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.9923871
        },
        {
          "intent": "None",
          "score": 0.007810574
        }
      ],
      "entities": []
    }
    ```

    Parametr QueryString znamená, že zahrnuje **všechny záměry** ve výsledcích dotazu aplikace. `verbose=true` Pole entit je prázdné, protože tato aplikace momentálně nemá žádné entity. 

    Výsledek JSON identifikuje záměr s nejvyšším skóre jako vlastnost **`topScoringIntent`** . Všechna skóre jsou v rozmezí 1 až 0, přičemž čím blíže je skóre hodnotě 1, tím je lepší. 

## <a name="create-intent-for-job-applications"></a>Vytvoření záměru pro aplikace úloh

Vraťte se na portál LUIS a vytvořte nový záměr, abyste zjistili, jestli se uživatel utterance o použití pro úlohu.

1. V nabídce vpravo nahoře vyberte **Build** (Sestavení) a vraťte se k vytváření aplikace.

1. V nabídce vlevo můžete vybrat **záměry** a získat tak seznam záměrů.

1. Vyberte **Create new intent** (Vytvořit nový záměr) a zadejte název `ApplyForJob`. 

    ![Dialogové okno služby LUIS pro vytvoření nového záměru](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

1. Přidejte do tohoto záměru několik promluv, které očekáváte, že uživatel bude požadovat, například:

    | Ukázkové promluvy|
    |--|
    |Fill out application for Job 123456 (Vyplňte přihlášku pro pozici 123456)|
    |Here is my c.v. (Tady je můj životopis) for position 654234 (na pozici 654234)|
    |Tady je moje obnovení pro recepční příspěvek na částečný úvazek.|
    |Mám v rámci této paperwork použít pro úlohu s uměleckými pracovníky.|
    |Používám pro školní školní účely v oblasti výzkumu a vývoje v San Diegu|
    |Žádám se o odeslání životopisu na dočasnou pozici v stravování.|
    |Odesílám novou Autocar týmu v Columbus.|
    |I want to apply for the new accounting job (Chci se přihlásit na novou pozici v účetnictví)|
    |Paperwork v účtování úlohy 456789 účtů|
    |Job 567890 and my paperwork (Pracovní pozice 567890 a moje doklady)|
    |Připojí se moje publikace pro učně účetní tulsay.|
    |Můj paperwork pro pozici svátků|
    |Odešlete prosím nové úlohy monitorování účtů v Seattlu.|
    |Submit resume for engineering position (Odeslat životopis pro pozici technického pracovníka)|
    |This is my c.v. pro post 234123 v Tampa.|

<!--

    [![Screenshot of entering new utterances for ApplyForJob intent](media/luis-quickstart-intents-only/utterance-applyforjob.png "Screenshot of entering new utterances for ApplyForJob intent")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    The labeled intent is outlined in red because LUIS is currently uncertain the intent is correct. Training the app tells LUIS the utterances are on the correct intent. 

-->

## <a name="train-again"></a>Nové trénování

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>Nové publikování

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-again"></a>Získat předpověď záměru

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. V novém okně prohlížeče zadejte na konec adresy URL `Can I submit my resume for job 235986`. 

    ```json
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9634406
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9634406
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0171300638
        },
        {
          "intent": "None",
          "score": 0.00670867041
        }
      ],
      "entities": []
    }
    ```

    Výsledky zahrnují nový záměr **ApplyForJob** i už existující záměry. 

## <a name="client-application-next-steps"></a>Klient-aplikace – další kroky

Poté, co LUIS vrátí odpověď JSON, služba LUIS s touto žádostí skončila. Služba LUIS neposkytuje odpovědi na promluvy uživatelů, pouze identifikuje, na jaký typ informací směřuje dotaz v přirozeném jazyce. Následná následné konverzace poskytuje klientská aplikace, jako je třeba robot Azure. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Související informace

* [Typy entit](luis-concept-entity-types.md)
* [Postup výuky](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Testování na portálu LUIS](luis-interactive-test.md)
* [Robot Azure bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili aplikaci Human Resources, dva záměry, přidali jste ukázkové promluvy jednotlivých záměrů, přidali jste ukázkové promluvy do záměru None a vytrénovali, publikovali a otestovali jste aplikaci na koncovém bodu. Toto jsou základní kroky vytvoření modelu LUIS. 

Pokračujte v této aplikaci a [přidejte jednoduchý seznam entit a frází](luis-quickstart-primary-and-secondary-data.md).

> [!div class="nextstepaction"]
> [Přidání předem připravených záměrů a entit do této aplikace](luis-tutorial-prebuilt-intents-entities.md)
