---
title: Application Insights Node.js
titleSuffix: Azure Cognitive Services
description: Vytvořte robota, integruje se službou LUIS aplikace a Application Insights s využitím Node.js.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 4461ac71ba5f9ab5901c5d7b72c15c0c165621e3
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53103961"
---
# <a name="add-luis-results-to-application-insights-and-azure-functions"></a>Přidání služby LUIS výsledky do Application Insights a Azure functions
V tomto kurzu přidá LUIS požadavku a odpovědi informace, které [Application Insights](https://azure.microsoft.com/services/application-insights/) úložiště dat telemetrie. Až budete mít data, můžete ji dotazovat s Kusto jazyk nebo Power BI k analýze, agregovat a vytváření sestav o záměry a entity utterance v reálném čase. Tato analýza pomůže zároveň pomáhá určit, pokud by měl přidat nebo upravit záměry a entity aplikace LUIS.

Robot využívá rozhraní Bot Framework 3.x a použijete Azure Web app bot.

V tomto kurzu se naučíte:

> [!div class="checklist"]
* Přidejte Application Insights knihovnu použijete web app bot
* Zaznamenávat a odesílat LUIS výsledků dotazu do Application Insights
* Dotaz Application Insights pro hlavní záměr, skóre a utterance

## <a name="prerequisites"></a>Požadavky

* Váš robot LUIS webové aplikace z **[předchozí kurz o službě](luis-nodejs-tutorial-build-bot-framework-sample.md)** pomocí Application Insights zapnuté. 

> [!Tip]
> Pokud ještě nemáte předplatné, si můžete zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/).

Veškerý kód v tomto kurzu je k dispozici na [úložišti github s ukázkami LUIS](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/nodejs) a každý řádek spojený s tímto kurzem je zakomentovaný s `//APPINSIGHT:`. 

## <a name="web-app-bot-with-luis"></a>Web app bot služba luis
Tento kurz předpokládá, že budete mít kód vypadá podobně jako následující nebo, že jste dokončili [další kurz](luis-nodejs-tutorial-build-bot-framework-sample.md): 

   [!code-javascript[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/nodejs/app.js "Web app bot with LUIS")]

## <a name="add-application-insights-library-to-web-app-bot"></a>Přidejte Application Insights knihovnu použijete web app bot
V současné době služba Application Insights, použít v tomto použijete web app bot shromažďuje telemetrii celkový stav pro robota. Neshromažďuje LUIS požadavku a odpovědi informace, které je potřeba zkontrolovat a opravit záměry a entity. 

K zachycení LUIS žádost a odpověď, použijete web app bot potřebuje **[Application Insights](https://www.npmjs.com/package/applicationinsights)** balíčku NPM ve nainstalovaná a nakonfigurovaná **app.js** souboru. Obslužné rutiny záměru dialogového okna je pak potřeba odesílání informací o požadavku a odpovědi LUIS Application Insights. 

1. Na webu Azure Portal, ve službě web app bot vyberte **sestavení** pod **Bot správu** oddílu. 

    ![Vyhledávání pro službu app insights](./media/luis-tutorial-appinsights/build.png)

2. S App Service editoru se otevře na nové kartě prohlížeče. V horním panelu vyberte název aplikace a pak vyberte **otevřete konzoly Kudu**. 

    ![Vyhledávání pro službu app insights](./media/luis-tutorial-appinsights/kudu-console.png)

3. V konzole zadejte následující příkaz k instalaci služby Application Insights a podtržítka balíčky:

    ```console
    cd site\wwwroot && npm install applicationinsights && npm install underscore
    ```

    ![Vyhledávání pro službu app insights](./media/luis-tutorial-appinsights/npm-install.png)

    Vyčkat, než instalace balíčků:

    ```console
    luisbot@1.0.0 D:\home\site\wwwroot
    `-- applicationinsights@1.0.1 
      +-- diagnostic-channel@0.2.0 
      +-- diagnostic-channel-publishers@0.2.1 
      `-- zone.js@0.7.6 
    
    npm WARN luisbot@1.0.0 No repository field.
    luisbot@1.0.0 D:\home\site\wwwroot
    +-- botbuilder-azure@3.0.4
    | `-- azure-storage@1.4.0
    |   `-- underscore@1.4.4 
    `-- underscore@1.8.3 
    ```

    Na kartě prohlížeče konzoly kudu dokončíte.

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Zaznamenávat a odesílat LUIS výsledků dotazu do Application Insights
1. V App Service Editor záložku prohlížeče, otevřete **app.js** souboru.

2. Přidejte následující knihovny NPM v rámci existující `requires` řádky:

   [!code-javascript[Add NPM packages to app.js](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=12-16 "Add NPM packages to app.js")]

3. Vytvořit objekt služby Application Insights a použijte nastavení aplikace web app bot **BotDevInsightsKey**: 

   [!code-javascript[Create the Application Insights object](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=68-80 "Create the Application Insights object")]

4. Přidat **appInsightsLog** funkce:

   [!code-javascript[Add the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=82-109 "Add the appInsightsLog function")]

    Poslední řádek funkce je, kde data je přidána do Application Insights. Název události je **LUIS výsledky**, jedinečný název kromě další telemetrická data shromážděná tento web app bot. 

5. Použití **appInsightsLog** funkce. Přidejte do každé záměru dialogového okna:

   [!code-javascript[Use the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=117-118 "Use the appInsightsLog function")]

6. Chcete-li otestovat použijete web app bot, použijte **testování ve Web Chat** funkce. Protože veškerá práce není ve službě Application Insights v odpovědi robotů, měli byste vidět žádné rozdíly.

## <a name="view-luis-entries-in-application-insights"></a>Zobrazení LUIS položky ve službě Application Insights
Otevřete službu Application Insights zobrazíte položky LUIS. 

1. Na portálu vyberte **všechny prostředky** pak filtrovat podle názvu webové aplikace robota. Klikněte na prostředek s typem **Application Insights**. Ikona pro službu Application Insights je žárovky. 

    ![Vyhledávání pro službu app insights](./media/luis-tutorial-appinsights/search-for-app-insights.png)



2. Pokud prostředek se otevře, klikněte na **hledání** ikonu na lupu v pravém panelu. Nový panel zobrazuje správné. V závislosti na tom, kolik dat telemetrie nenajde, panelu může chvíli trvat, chcete-li zobrazit. Vyhledejte `LUIS-results` a stiskněte klávesu enter na klávesnici. V seznamu je zúžit jen LUIS výsledky dotazu přidat v tomto kurzu.

    ![Filtrování závislostí](./media/luis-tutorial-appinsights/app-insights-filter.png)

3. Vyberte na horní položku. Nové okno zobrazí podrobnější data, včetně vlastních dat pro dotaz LUIS úplně vpravo. Data obsahují hlavní záměr a jeho skóre.

    ![Podrobnosti o závislosti](./media/luis-tutorial-appinsights/app-insights-detail.png)

    Jakmile budete hotovi, vyberte úplně vpravo nahoře **X** se vraťte do seznamu položek závislostí. 


> [!Tip]
> Pokud chcete uložit seznam závislostí a vrátit se k němu později, klikněte na **... Další** a klikněte na tlačítko **uložit oblíbenou**.

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Dotaz Application Insights pro záměr, skóre a utterance
Služba Application Insights poskytuje výkonné nástroje pro dotazování dat pomocí služby [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) jazyka, stejně jako export umožňuje [PowerBI](https://powerbi.microsoft.com). 

1. Klikněte na **Analytics** v horní části závislosti výpis výše pole filtru. 

    ![Tlačítko Analytics](./media/luis-tutorial-appinsights/analytics-button.png)

2. Otevře se okno nové okno dotazu v horní části a okno tabulky dat pod ním. Pokud jste použili databází před, je toto uspořádání zkušenosti. Dotaz obsahuje všechny položky z posledních 24 hodin, počínaje název `LUIS-results`. **CustomDimensions** sloupec má LUIS výsledky dotazu jako dvojice název/hodnota.

    ![Okno dotazu Analytics](./media/luis-tutorial-appinsights/analytics-query-window.png)

3. Vyžádá si hlavní záměr, skóre a utterance, přidejte následující přímo nad poslední řádek v okně dotazu:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_intent_intent)
    | extend score = todouble(customDimensions.LUIS_intent_score)
    | extend utterance = tostring(customDimensions.LUIS_text)
    ```

4. Spusťte dotaz. Posuňte se úplně vpravo v datové tabulce. Nové sloupce topIntent, skóre a utterance jsou k dispozici. Klikněte na sloupec topIntent seřadit.

    ![Analýza hlavní záměr](./media/luis-tutorial-appinsights/app-insights-top-intent.png)


Další informace o [Kusto dotazovací jazyk](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) nebo [export dat do Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>Další postup

Další informace, které chcete přidat do application insights data zahrnuje ID aplikace, ID verze, datum poslední změny modelu, datum posledního trénování, datum posledního publikování. Tyto hodnoty lze získat buď z adresu URL koncového bodu (ID aplikace a ID verze) nebo z [vytváření rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) volání pak v nastavení web app bot a získaná z něj.  

Pokud používáte stejné předplatné koncový bod pro více než jednu aplikaci LUIS, by měl také obsahovat ID předplatného a vlastnost s informacemi o tom, že se jedná o sdílený klíč. 

> [!div class="nextstepaction"]
> [Další informace o příklad projevy](luis-how-to-add-example-utterances.md)
