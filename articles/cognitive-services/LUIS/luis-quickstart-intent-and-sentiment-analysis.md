---
title: Analýza mínění
titleSuffix: Azure Cognitive Services
description: V tomto kurzu vytvoříte aplikaci, která ukazuje, jak z promluv extrahovat pozitivní, negativní a neutrální mínění. Mínění se určuje z celé promluvy.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: e556b63fbe6bf1fe89672ee3aad4b7c9191233c5
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53727628"
---
# <a name="tutorial-9--extract-sentiment-of-overall-utterance"></a>Kurz 9:  Extrahovat zabarvení celkové utterance
V tomto kurzu vytvoříte aplikaci, která ukazuje, jak z promluv extrahovat pozitivní, negativní a neutrální mínění. Mínění se určuje z celé promluvy.

Analýza mínění představuje možnost určit, jestli je promluva uživatele pozitivní, negativní nebo neutrální. 

Následující promluvy ukazují příklady mínění:

|Mínění|Skóre|Promluva|
|:--|:--|:--|
|pozitivní|0,91 |John W. Smith did a great job on the presentation in Paris (John W. Smith udělal skvělou práci na prezentaci v Paříži).|
|pozitivní|0,84 |jill-jones@mycompany.com did fabulous work on the Parker sales pitch (jill-jones@mycompany.com odvedla vynikající práci při prodejní prezentaci pro firmu Parker).|

Analýza mínění je nastavení publikování, které se vztahuje na všechny promluvy. Nemusíte vyhledávat slova indikující mínění v promluvě a označovat je, protože analýza mínění se používá u celé promluvy. 

Jedná se o nastavení publikování, takže se nezobrazuje na stránkách záměrů a entit. Zobrazuje se v podokně [interaktivního testu](luis-interactive-test.md#view-sentiment-results) nebo při testování na adrese URL koncového bodu. 

**Co se v tomto kurzu naučíte:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Použití existující ukázkové aplikace 
> * Přidání analýzy mínění jako nastavení publikování
> * Trénování
> * Publikování
> * Získání mínění promluvy z koncového bodu

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Použití existující aplikace

Pokračujte s aplikací **HumanResources**, kterou jste vytvořili v posledním kurzu. 

Pokud aplikaci HumanResources z předchozího kurzu nemáte, postupujte takto:

1.  Stáhněte si [soubor JSON aplikace](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-keyphrase-HumanResources.json) a uložte si ho.

2. Naimportujte soubor JSON do nové aplikace.

3. V části **Manage** (Správa) na kartě **Versions** (Verze) naklonujte verzi a pojmenujte ji `sentiment`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. Název verze je součástí cesty URL, a proto smí obsahovat jenom znaky, které jsou platné v adresách URL.

## <a name="employeefeedback-intent"></a>Záměr EmployeeFeedback (Zpětná vazba o zaměstnancích) 
Přidejte nový záměr, který bude zachycovat zpětnou vazbu o zaměstnancích od členů společnosti. 

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Vyberte **Create new intent** (Vytvořit nový záměr).

3. Nový záměr pojmenujte `EmployeeFeedback`.

    ![Dialogové okno Create new intent (Vytvořit nový záměr) s názvem EmployeeFeedback (Zpětná vazba o zaměstnancích)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. Přidejte několik promluv, které budou indikovat, že zaměstnanec udělal něco dobře, nebo nějakou oblast, ve které se potřebuje zlepšit:

    Mějte na paměti, že v této aplikaci pro lidské zdroje jsou zaměstnanci definováni v entitě seznamu `Employee` pomocí jména, e-mailu, čísla telefonní linky, čísla mobilního telefonu a čísla amerického federálního sociálního pojištění. 

    |Projevy|
    |--|
    |425-555-1212 did a nice job of welcoming back a co-worker from maternity leave (425-555-1212 udělala dobrou práci, když přivítala spolupracovnici, která se vrátila z mateřské dovolené).|
    |234-56-7891 did a great job of comforting a co-worker in their time of grief (234. 56 7891 se hezky zachoval, když utěšoval spolupracovníka v době smutku).|
    |jill-jones@mycompany.com didn't have all the required invoices for the paperwork (jill-jones@mycompany.com neměla všechny požadované faktury pro zpracování dokladů).|
    |john.w.smith@mycompany.com turned in the required forms a month late with no signatures (john.w.smith@mycompany.com odevzdal požadované formuláře s měsíčním zpožděním a bez podpisů).|
    |x23456 didn't make it to the important marketing off-site meeting (x23456 se včas nedostavil na důležitou externí schůzku o marketingu).|
    |x12345 missed the meeting for June reviews (x12345 nepřišla na schůzku hodnocení za červen).|
    |Jill Jones rocked the sales pitch at Harvard (Jill Jonesová fantasticky zvládla prodejní prezentaci na Harvardu).|
    |John W. Smith did a great job on the presentation at Stanford (John W. Smith udělal skvělou práci na prezentaci na Standfordu).|

    [ ![Snímek obrazovky aplikace LUIS s příklady promluv v záměru EmployeeFeedback (Zpětná vazba o zaměstnancích)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="train"></a>Trénování

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Konfigurace aplikace pro zahrnutí analýzy mínění
1. Na pravém horním navigačním panelu vyberte **Manage** (Správa) a pak v levé nabídce vyberte **Publish settings** (Nastavení publikování).

2. Přepnutím možnosti **Sentiment Analysis** (Analýza mínění) povolte toto nastavení. 

    ![Zapnout analýzu subjektivního hodnocení jako nastavení publikování](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish"></a>Publikování

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-sentiment-of-utterance-from-endpoint"></a>Získání mínění promluvy z koncového bodu

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Na konec adresy URL zadejte `Jill Jones work with the media team on the public portal was amazing`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. Tato promluva není stejná jako žádná z označených promluv, proto je to dobrý test a měl by se vrátit záměr `EmployeeFeedback` s extrahovanou analýzou mínění.
    
    ```json
    {
      "query": "Jill Jones work with the media team on the public portal was amazing",
      "topScoringIntent": {
        "intent": "EmployeeFeedback",
        "score": 0.4983256
      },
      "intents": [
        {
          "intent": "EmployeeFeedback",
          "score": 0.4983256
        },
        {
          "intent": "MoveEmployee",
          "score": 0.06617523
        },
        {
          "intent": "GetJobInformation",
          "score": 0.04631853
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0103248553
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.007531875
        },
        {
          "intent": "FindForm",
          "score": 0.00344597152
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00337914471
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.0026357458
        },
        {
          "intent": "None",
          "score": 0.00214573368
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00157622492
        },
        {
          "intent": "Utilities.Confirm",
          "score": 7.379545E-05
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "Employee",
          "startIndex": 0,
          "endIndex": 9,
          "resolution": {
            "values": [
              "Employee-45612"
            ]
          }
        },
        {
          "entity": "media team",
          "type": "builtin.keyPhrase",
          "startIndex": 25,
          "endIndex": 34
        },
        {
          "entity": "public portal",
          "type": "builtin.keyPhrase",
          "startIndex": 43,
          "endIndex": 55
        },
        {
          "entity": "jill jones",
          "type": "builtin.keyPhrase",
          "startIndex": 0,
          "endIndex": 9
        }
      ],
      "sentimentAnalysis": {
        "label": "positive",
        "score": 0.8694164
      }
    }
    ```

    Hodnota SentimentAnalysis je kladná a má skóre 0,86. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Další postup
V tomto kurzu se přidá analýza mínění jako nastavení publikování tak, aby se extrahovaly hodnoty mínění z promluvy jako celku.

> [!div class="nextstepaction"] 
> [Kontrola promluv koncového bodu v aplikaci HR](luis-tutorial-review-endpoint-utterances.md) 

