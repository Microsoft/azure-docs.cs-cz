---
title: 'Kurz 1: Vyhledání záměrů ve vlastní aplikaci LUIS'
titleSuffix: Azure Cognitive Services
description: Vytvořte si vlastní aplikaci, která předpovídá záměr uživatele. Tato aplikace představuje nejjednodušší typ aplikace LUIS, protože neextrahuje různé datové prvky z textu promluvy, jako jsou e-mailové adresy nebo kalendářní data.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 30c9f572d77caacbeecf5f15d74fd8517e9fa883
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52426855"
---
# <a name="tutorial-1-build-custom-app-to-determine-user-intentions"></a>Kurz 1: Vytvoření vlastní aplikace k určení záměrů uživatele

V tomto kurzu vytvoříte vlastní aplikaci pro lidské zdroje, která předpovídá záměr uživatele na základě promluvy (textu). Až budete hotovi, budete mít koncový bod služby LUIS spuštěný v cloudu.

Účelem této aplikace je určit záměr v textu konverzačního přirozeného jazyka. Tyto záměry jsou zařazené do kategorie **Intents** (Záměry). Tato aplikace má několik záměrů. První záměr **`GetJobInformation`** identifikuje, když chce uživatel získat informace o volných pracovních pozicích ve společnosti. Druhý záměr **`None`** se používá pro všechny promluvy uživatele, které jsou mimo _doménu_ (obor) této aplikace. Později se přidá třetí záměr **`ApplyForJob`** pro všechny promluvy týkající se žádostí o pracovní pozici. Tento třetí záměr se liší od záměru `GetJobInformation`, protože informace o pracovní pozici jsou v případě, že někdo žádá o pracovní místo, už známé. V závislosti na výběru slov ale může být určení správného záměru matoucí, protože oba záměry se týkají pracovní pozice.

Poté, co LUIS vrátí odpověď JSON, služba LUIS s touto žádostí skončila. Služba LUIS neposkytuje odpovědi na promluvy uživatelů, pouze identifikuje, na jaký typ informací směřuje dotaz v přirozeném jazyce. 

**V tomto kurzu se naučíte:**

> [!div class="checklist"]
> * Vytvoření nové aplikace 
> * Vytvořit záměry
> * Přidat ukázkové promluvy
> * Vytrénovat aplikaci
> * Publikování aplikace
> * Získat záměr z koncového bodu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="create-a-new-app"></a>Vytvoření nové aplikace

1. Přihlaste se k portálu LUIS s adresou URL [https://www.luis.ai](https://www.luis.ai). 

2. Vyberte **Create new app** (Vytvořit novou aplikaci).  

    [![](media/luis-quickstart-intents-only/app-list.png "Snímek obrazovky se stránkou My Apps (Moje aplikace) ve službě LUIS (Language Understanding)")](media/luis-quickstart-intents-only/app-list.png#lightbox)

3. V automaticky otevíraném dialogovém okně zadejte název `HumanResources` a ponechejte nastavenou výchozí jazykovou verzi – **English**. Popis nechejte prázdný.

    ![Nová aplikace LUIS](./media/luis-quickstart-intents-only/create-app.png)

    Dále aplikace zobrazí stránku **Intents** (Záměry) se záměrem **None** (Žádný).

## <a name="getjobinformation-intent"></a>Záměr GetJobInformation (Informace o pracovní pozici)

1. Vyberte **Create new intent** (Vytvořit nový záměr). Zadejte název nového záměru `GetJobInformation`. Tento záměr se předpoví pokaždé, když chce uživatel získat informace o volných pracovních pozicích ve společnosti.

    ![](media/luis-quickstart-intents-only/create-intent.png "Snímek obrazovky s dialogem New intent (Nový záměr) ve službě LUIS (Language Understanding)")

2. Poskytnutím _ukázkových promluv_ trénujete službu LUIS na druhy výroků, které by pro tento záměr měla předvídat. Přidejte do tohoto záměru několik ukázkových promluv, které očekáváte, že uživatel zadá, například:

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

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    


## <a name="none-intent"></a>Žádný záměr 
Klientská aplikace potřebuje vědět, zda je promluva mimo doménu předmětu aplikace. Pokud LUIS vrátí pro promluvu záměr **None** (Žádný), klientská aplikace se může zeptat, jestli chce uživatel ukončit konverzaci. Pokud uživatel nechce ukončit konverzaci, může klientská aplikace také nabídnout možnosti jejího dalšího směřování. 

Tyto ukázkové promluvy mimo doménu předmětu jsou seskupeny do záměru **None** (Žádný). Nenechávejte ho prázdný. 

1. Na levém panelu vyberte **Intents** (Záměry).

2. Vyberte záměr **None** (Žádný). Přidejte tři promluvy, které může uživatel zadat, ale které nejsou pro aplikaci Human Resources relevantní. Pokud se aplikace týká nabídek pracovních pozic, mezi příklady promluv se záměrem **None** (Žádný) patří:

    | Ukázkové promluvy|
    |--|
    |Barking dogs are annoying (Štěkající psi jsou otravní)|
    |Order a pizza for me (Objednej mi pizzu)|
    |Penguins in the ocean (Tučňáci v oceánech)|


## <a name="train"></a>Trénování 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publikování

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent"></a>Získání záměru

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Přejděte na konec adresy URL v panelu adresy a zadejte `I'm looking for a job with Natural Language Processing`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. Tato promluva není stejná jako některá z ukázkových promluv. Jde o vhodný test a jako záměr s nejvyšším skóre by se měl vrátit záměr `GetJobInformation`. 

    ```JSON
    {
      "query": "I'm looking for a job with Natural Language Processing",
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

    Výsledky zahrnují **všechny záměry** v aplikaci, přičemž aktuálně jsou 2. Pole entit je prázdné, protože tato aplikace momentálně nemá žádné entity. 

    Výsledek JSON identifikuje záměr s nejvyšším skóre jako vlastnost **`topScoringIntent`**. Všechna skóre jsou v rozmezí 1 až 0, přičemž čím blíže je skóre hodnotě 1, tím je lepší. 

## <a name="applyforjob-intent"></a>Záměr ApplyForJob (Přihláška na pracovní pozici)
Vraťte se na web služby LUIS a vytvořte nový záměr pro určení, zda se promluva uživatele týká žádosti o pracovní pozici.

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

## <a name="train-again"></a>Nové trénování

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>Nové publikování

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-again"></a>Nové získání záměru

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. V novém okně prohlížeče zadejte na konec adresy URL `Can I submit my resume for job 235986`. 

    ```JSON
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

    Výsledky zahrnují nový záměr **ApplyForJob** i už existující záměry. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili aplikaci Human Resources, dva záměry, přidali jste ukázkové promluvy jednotlivých záměrů, přidali jste ukázkové promluvy do záměru None a vytrénovali, publikovali a otestovali jste aplikaci na koncovém bodu. Toto jsou základní kroky vytvoření modelu LUIS. 

> [!div class="nextstepaction"]
> [Přidání předem připravených záměrů a entit do této aplikace](luis-tutorial-prebuilt-intents-entities.md)
