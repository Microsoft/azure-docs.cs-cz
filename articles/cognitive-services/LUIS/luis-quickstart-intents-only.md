---
title: Předpověď záměry
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvořte vlastní aplikaci, který bude předpovídat záměru uživatele. Tato aplikace představuje nejjednodušší typ aplikace LUIS, protože neextrahuje různé datové prvky z textu promluvy, jako jsou e-mailové adresy nebo kalendářní data.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: b4ac0ccb249bac7149014861056c10f9093d6759
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878153"
---
# <a name="tutorial-build-luis-app-to-determine-user-intentions"></a>Kurz: Sestavení aplikace LUIS k určení záměry uživatele

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

## <a name="user-intentions-as-intents"></a>Uživatel záměry jako záměrů

Účelem aplikace je určit záměr konverzační, přirozený jazyk textu: 

`Are there any new positions in the Seattle office?`

Tyto záměry jsou zařazené do kategorie **Intents** (Záměry). 

Tato aplikace má několik záměrů. 

|Záměr|Účel|
|--|--|
|ApplyForJob|Určete, pokud uživatel žádá o úlohu.|
|GetJobInformation|Určete, pokud uživatel hledající informace o úlohy v obecné nebo konkrétní úlohy.|
|Žádný|Určit, pokud uživatel požaduje něco aplikace by neměl odpovědět. Pokud záměru poskytnutého jako součást vytváření aplikací a nelze ji odstranit. |

## <a name="create-a-new-app"></a>Vytvoření nové aplikace

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-job-information"></a>Vytvořit záměr pro informace o úloze

1. Vyberte **Create new intent** (Vytvořit nový záměr). Zadejte název nového záměru `GetJobInformation`. Cílem tohoto je očekává se, že když uživatel chce, aby se informace o úlohách otevřít ve společnosti. 

    ![Snímek obrazovky z Language Understanding (LUIS) dialogové okno záměru nového](media/luis-quickstart-intents-only/create-intent.png "nové záměru dialogové okno snímek obrazovky z Language Understanding (LUIS)")

1. Vyberte **Done** (Hotovo).

2. Přidání projevů několik příklad na toto záměr, že očekáváte, že uživatel požádat:

    | Ukázkové promluvy|
    |--|
    |Any new jobs posted today? (Nějaké nové nabídky práce?)|
    |Are there any new positions in the Seattle office? (Nejsou na pobočce v Seattlu nějaké nové pozice?)|
    |Existují jakékoli vzdálených pracovníků nebo práce z domu open úlohy pro techniky?|
    |Is there any work with databases? (Nabízíte nějakou práci se s databázemi?)|
    |Hledám situace společné práci v aplikaci tampa office.|
    |Existuje lékařského v kalifornském san franciscu office?|
    |Je k dispozici žádné úvazku osobám ve škole?|
    |Looking for a new situation with responsibilities in accounting (Hledám něco nového v oboru účetnictví).|
    |Hledáte dvojjazyčné přednášející pro úlohy v new Yorku.|
    |Hledání pro novou situaci s odpovědnosti v monitorování účtů.|
    |New jobs? (Nějaké nové pracovní pozice?)|
    |Zobrazte všechny úlohy pro techniky, které byly přidány v posledních 2 dnech.|
    |Dnešní úlohy příspěvky?|
    |Jaké pozice monitorování účtů jsou otevřeny v londýnské pobočce?|
    |What positions are available for Senior Engineers? (Jaké pozice jsou otevřené na úrovni inženýr senior?)|
    |Where is the job listings (Kde najdu seznam pracovních pozic)|

    [![Snímek obrazovky zadání nového projevy pro MyStore záměr](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "snímek obrazovky zadání nového projevy pro MyStore záměr")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

    Tím, že poskytuje _příklad projevy_, jsou školení LUIS, o jaký druh projevy musí předpovídat pro tohoto záměru. 

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    

## <a name="add-example-utterances-to-the-none-intent"></a>Přidání projevů příklad na hodnotu None záměru 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>Trénování před testování a publikování aplikace

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Publikujte aplikaci do dotaz z koncového bodu

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-from-the-endpoint"></a>Získání záměru předpovědi z koncového bodu

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

    `verbose=true` Parametr querystring znamená, že zahrnují **všechny záměry** ve výsledcích dotazu aplikaci. Pole entit je prázdné, protože tato aplikace momentálně nemá žádné entity. 

    Výsledek JSON identifikuje záměr s nejvyšším skóre jako vlastnost **`topScoringIntent`**. Všechna skóre jsou v rozmezí 1 až 0, přičemž čím blíže je skóre hodnotě 1, tím je lepší. 

## <a name="create-intent-for-job-applications"></a>Vytvořit záměr pro úlohy aplikací

Vraťte se na portál služby LUIS a vytvořit nové záměr k určení, zda je uživatel utterance o použití pro úlohu.

1. V nabídce vpravo nahoře vyberte **Build** (Sestavení) a vraťte se k vytváření aplikace.

1. Vyberte **záměry** v levé nabídce zobrazíte seznam záměry.

1. Vyberte **Create new intent** (Vytvořit nový záměr) a zadejte název `ApplyForJob`. 

    ![Dialogové okno služby LUIS pro vytvoření nového záměru](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

1. Přidejte do tohoto záměru několik promluv, které očekáváte, že uživatel bude požadovat, například:

    | Ukázkové promluvy|
    |--|
    |Fill out application for Job 123456 (Vyplňte přihlášku pro pozici 123456)|
    |Here is my c.v. (Tady je můj životopis) for position 654234 (na pozici 654234)|
    |Tady je Moje pokračovat na částečný úvazek recepční příspěvek.|
    |Můžu jsem použití pro úlohu helpdesku obrázky s této doklady.|
    |Žádající lékařského Fakultě léto pro výzkum a vývoj v San Diegu|
    |Požaduji odeslání svůj životopis dočasné umístění v stravování.|
    |Můžu jsem odesílání Moje pokračovat pro nový tým Autocar v Kolumbův, raději neříkám:|
    |I want to apply for the new accounting job (Chci se přihlásit na novou pozici v účetnictví)|
    |Tady je doklady lékařského 456789 úlohy monitorování účtů|
    |Job 567890 and my paperwork (Pracovní pozice 567890 a moje doklady)|
    |Moje lístky pro monitorování účtů lékařského tulsa jsou připojené.|
    |Moje doklady pozice doručování svátek|
    |Odešlete své obnovit pro nové úlohy monitorování účtů v Praze|
    |Submit resume for engineering position (Odeslat životopis pro pozici technického pracovníka)|
    |This is my c.v. pro metodu post 234123 v Tampa.|

    [![Snímek obrazovky zadání nového projevy pro ApplyForJob záměr](media/luis-quickstart-intents-only/utterance-applyforjob.png "snímek obrazovky zadání nového projevy pro ApplyForJob záměr")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    Označený záměr má červený rámeček, protože si služba LUIS zatím není jistá, jestli je záměr správný. Trénováním aplikace sdělíte službě LUIS, že je záměr promluv správný. 

## <a name="train-again"></a>Nové trénování

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>Nové publikování

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-again"></a>Získání záměru předpovědi znovu

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

## <a name="client-application-next-steps"></a>Další kroky klientské aplikace

Poté, co LUIS vrátí odpověď JSON, služba LUIS s touto žádostí skončila. Služba LUIS neposkytuje odpovědi na promluvy uživatelů, pouze identifikuje, na jaký typ informací směřuje dotaz v přirozeném jazyce. Klientská aplikace, jako jsou Azure Bot poskytuje konverzační následnou akci. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Související informace

* [Typy entit](luis-concept-entity-types.md)
* [Trénování](luis-how-to-train.md)
* [Jak publikovat](luis-how-to-publish-app.md)
* [Testování v portálu služby LUIS](luis-interactive-test.md)
* [Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili aplikaci Human Resources, dva záměry, přidali jste ukázkové promluvy jednotlivých záměrů, přidali jste ukázkové promluvy do záměru None a vytrénovali, publikovali a otestovali jste aplikaci na koncovém bodu. Toto jsou základní kroky vytvoření modelu LUIS. 

Pokračovat v této aplikaci [přidání jednoduchý seznam entit a frází](luis-quickstart-primary-and-secondary-data.md).

> [!div class="nextstepaction"]
> [Přidání předem připravených záměrů a entit do této aplikace](luis-tutorial-prebuilt-intents-entities.md)
