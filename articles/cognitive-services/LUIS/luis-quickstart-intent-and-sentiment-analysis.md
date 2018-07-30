---
title: Kurz vytvoření aplikace LUIS vracející analýzu mínění – Azure | Microsoft Docs
description: V tomto kurzu zjistíte, jak do své aplikace LUIS přidat analýzu mínění umožňující analýzu pozitivních, negativních a neutrálních pocitů v promluvách.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: v-geberr
ms.openlocfilehash: 8dc6b8d4dd4cbe64841c4b36c498cf9021b4196f
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37930602"
---
# <a name="tutorial-9--add-sentiment-analysis"></a>Kurz: 9.  Přidání analýzy mínění
V tomto kurzu vytvoříte aplikaci, která ukazuje, jak z promluv extrahovat pozitivní, negativní a neutrální mínění.

<!-- green checkmark -->
> [!div class="checklist"]
> * Vysvětlení analýzy mínění
> * Použití aplikace LUIS v doméně lidských zdrojů 
> * Přidání analýzy mínění
> * Trénování a publikování aplikace
> * Odeslání dotazu na koncový bod aplikace a zobrazení odpovědi JSON ze služby LUIS 

Pro účely tohoto článku potřebujete bezplatný účet [LUIS](luis-reference-regions.md#luis-website), abyste mohli vytvořit svou aplikaci LUIS.

## <a name="before-you-begin"></a>Než začnete
Pokud nemáte aplikaci pro lidské zdroje z kurzu k [předdefinované entitě klíčové fráze](luis-quickstart-intent-and-key-phrase.md), [naimportujte](luis-how-to-start-new-app.md#import-new-app) JSON do nové aplikace na webu služby [LUIS](luis-reference-regions.md#luis-website). Aplikaci k importování najdete v úložišti [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-keyphrase-HumanResources.json) na Githubu.

Pokud chcete zachovat původní aplikaci pro lidské zdroje, naklonujte verzi na stránce [Settings](luis-how-to-manage-versions.md#clone-a-version) (Nastavení) a pojmenujte ji `sentiment`. Klonování představuje skvělý způsob, jak si můžete vyzkoušet různé funkce služby LUIS, aniž by to mělo vliv na původní verzi. 

## <a name="sentiment-analysis"></a>Analýza mínění
Analýza mínění představuje možnost určit, jestli je promluva uživatele pozitivní, negativní nebo neutrální. 

Následující promluvy ukazují příklady mínění:

|Mínění|Skóre|Promluva|
|:--|:--|:--|
|pozitivní|0,91 |John W. Smith did a great job on the presentation in Paris (John W. Smith udělal skvělou práci na prezentaci v Paříži).|
|pozitivní|0,84 |jill-jones@mycompany.com did fabulous work on the Parker sales pitch (jill-jones@mycompany.com odvedla vynikající práci při prodejní prezentaci pro firmu Parker).|

Analýza mínění je nastavení aplikace, které se vztahuje na všechny promluvy. Nemusíte vyhledávat slova indikující mínění v promluvě a označovat je, protože analýza mínění se používá u celé promluvy. 

## <a name="add-employeefeedback-intent"></a>Přidání záměru EmployeeFeedback (Zpětná vazba o zaměstnancích) 
Přidejte nový záměr, který bude zachycovat zpětnou vazbu o zaměstnancích od členů společnosti. 

1. Ujistěte se, že je vaše aplikace pro lidské zdroje uvedená v části **Build** (Sestavení) služby LUIS. Do této části můžete přejít výběrem možnosti **Build** (Sestavit) v pravém horním řádku nabídek. 

    [ ![Snímek obrazovky aplikace LUIS se zvýrazněnou možností Build (Sestavit) na pravém horním navigačním panelu](./media/luis-quickstart-intent-and-sentiment-analysis/hr-first-image.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-first-image.png#lightbox)

2. Vyberte **Create new intent** (Vytvořit nový záměr).

    [ ![Snímek obrazovky aplikace LUIS se zvýrazněnou možností Build (Sestavit) na pravém horním navigačním panelu](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent.png#lightbox)

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

## <a name="train-the-luis-app"></a>Trénování aplikace LUIS
Aplikace LUIS nebude o novém záměru a příkladech promluv vědět, dokud nebude natrénována. 

1. V pravé horní části webu LUIS vyberte tlačítko **Train** (Trénovat).

    ![Snímek obrazovky se zvýrazněným tlačítkem Train (Trénovat)](./media/luis-quickstart-intent-and-sentiment-analysis/train-button.png)

2. Trénování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

    ![Snímek obrazovky s pruhem oznamujícím úspěšné trénování ](./media/luis-quickstart-intent-and-sentiment-analysis/hr-trained-inline.png)

## <a name="configure-app-to-include-sentiment-analysis"></a>Konfigurace aplikace pro zahrnutí analýzy mínění
Analýza mínění se konfiguruje na stránce **Publish** (Publikovat). 

1. Na pravém horním navigačním panelu vyberte **Publish** (Publikovat).

    ![Snímek obrazovky se stránkou záměru a rozbaleným tlačítkem Publish (Publikovat) ](./media/luis-quickstart-intent-and-sentiment-analysis/hr-publish-button-in-top-nav-highlighted.png)

2. Vyberte **Enable Sentiment Analysis** (Povolit analýzu mínění). Vyberte slot Production (Produkční) a tlačítko **Publish** (Publikovat).

    [![](media/luis-quickstart-intent-and-sentiment-analysis/hr-publish-to-production-expanded.png "Snímek obrazovky se stránkou Publish (Publikovat) a zvýrazněným tlačítkem Publish to production slot (Publikovat do produkčního slotu)")](media/luis-quickstart-intent-and-sentiment-analysis/hr-publish-to-production-expanded.png#lightbox)

4. Publikování je dokončené, když se v horní části webu zobrazí zelený stavový řádek potvrzující úspěch.

## <a name="query-the-endpoint-with-an-utterance"></a>Odeslání dotazu na koncový bod s promluvou

1. V dolní části stránky **Publish** (Publikovat) vyberte odkaz na **koncový bod**. Tato akce otevře další okno prohlížeče s adresou URL koncového bodu v adresním řádku. 

    ![Snímek obrazovky se stránkou Publish (Publikovat) a zvýrazněnou adresou URL koncového bodu](media/luis-quickstart-intent-and-sentiment-analysis/hr-endpoint-url-inline.png)

2. Na konec adresy URL zadejte `Jill Jones work with the media team on the public portal was amazing`. Poslední parametr řetězce dotazu je `q`, což je **dotaz** promluvy. Tato promluva není stejná jako žádná z označených promluv, proto je to dobrý test a měl by se vrátit záměr `EmployeeFeedback` s extrahovanou analýzou mínění.

```
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

## <a name="what-has-this-luis-app-accomplished"></a>Co tato aplikace LUIS udělala?
Tato aplikace s povolenou analýzou mínění identifikovala záměr dotazu v přirozeném jazyce a vrátila extrahovaná data, včetně celkového mínění v podobě skóre. 

Váš chatbot má teď dostatek informací k určení dalšího kroku v konverzaci. 

## <a name="where-is-this-luis-data-used"></a>Kde se tato data služby LUIS používají? 
Služba LUIS s tímto požadavkem skončila. Volající aplikace, například chatbot, může převzít výsledek topScoringIntent a data o mínění z promluvy a provést další krok. Služba LUIS neprovádí tuto programovou práci za chatbota ani nevolá aplikaci. Služba LUIS pouze určuje, co je záměrem uživatele. 

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už aplikaci LUIS nepotřebujete, odstraňte ji. V nabídce vlevo nahoře vyberte **My apps** (Moje aplikace). Vyberte tři tečky (***...***) vpravo od názvu aplikace v seznamu aplikací a potom vyberte **Delete** (Odstranit). V automaticky otevíraném dialogovém okně **Delete app?** (Odstranit aplikaci?) vyberte **Ok**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Kontrola promluv koncového bodu v aplikaci HR](luis-tutorial-review-endpoint-utterances.md) 

