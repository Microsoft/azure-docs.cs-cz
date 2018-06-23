---
title: Přidat LEOŠ data do služby Application Insights pomocí Node.js | Microsoft Docs
titleSuffix: Azure
description: Sestavení robotu integrované s LEOŠ aplikace a služby Application Insights pomocí Node.js.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 01/18/2018
ms.author: v-geberr
ms.openlocfilehash: 929b6e1cc980d7215f91a616820e257aed26bab7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343726"
---
# <a name="add-luis-results-to-application-insights-from-a-web-app-bot"></a>Přidat LEOŠ výsledky do služby Application Insights z robotu webové aplikace
V tomto kurzu přidá LEOŠ žádosti a odpovědi informace, které [Application Insights](https://azure.microsoft.com/services/application-insights/) telemetrická data úložiště. Jakmile máte tato data, můžete ho dotazování pomocí jazyka Kusto nebo Power BI a analyzovat, agregaci a vytváření sestav o záměry a entity utterance v reálném čase. Tato analysis pomáhá při určení, pokud by měla přidat nebo upravit tříd Intent a entity LEOŠ aplikace.

V tomto kurzu se naučíte:

> [!div class="checklist"]
* Přidejte Application Insights knihovnu robotu webové aplikace
* Zaznamenání a odesílání LEOŠ výsledky dotazu Application Insights
* Dotaz pro horní záměr, skóre a utterance Application Insights

## <a name="prerequisites"></a>Požadavky

* Vaše robota LEOŠ webové aplikace z **[předchozí kurzu](luis-nodejs-tutorial-build-bot-framework-sample.md)** s Application Insights zapnutý. 

> [!Tip]
> Pokud již nemáte předplatné, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/).

Kód v tomto kurzu je k dispozici na [úložiště github LEOŠ-Samples](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/nodejs) a každý řádek přidružený tento kurz je označeno jako komentář s `//APPINSIGHT:`. 

## <a name="web-app-bot-with-luis"></a>Webové aplikace robota s LEOŠ
Tento kurz předpokládá, že máte kódu vypadá podobně jako následující nebo že jste dokončili [další kurz](luis-nodejs-tutorial-build-bot-framework-sample.md): 

   [!code-javascript[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/nodejs/app.js "Web app bot with LUIS")]

## <a name="add-application-insights-library-to-web-app-bot"></a>Přidání knihovny Application Insights pro webové aplikace robota
V současné době službu Application Insights, používanou v této webové aplikace robota, shromažďuje celkový stav telemetrii robota. Neshromažďuje LEOŠ žádosti a odpovědi informace, které je třeba kontrola a oprava tříd Intent a entity. 

Aby bylo možné zachytit LEOŠ žádosti a odpovědi, potřebuje robota aplikace web **[Application Insights](https://www.npmjs.com/package/applicationinsights)** NPM balíček nainstalován a nakonfigurován v **app.js** souboru. Obslužné rutiny záměrné dialogu je pak potřeba odesílat informace žádosti a odpovědi LEOŠ Application Insights. 

1. Na portálu Azure v rámci webové aplikace robota služby, vyberte **sestavení** pod **robota správu** části. 

    ![Hledání přehledů aplikace](./media/luis-tutorial-appinsights/build.png)

2. Pomocí editoru služby aplikace se otevře novou kartu prohlížeče. V horním panelu vyberte název aplikace a pak vyberte **otevřít konzolu Kudu**. 

    ![Hledání přehledů aplikace](./media/luis-tutorial-appinsights/kudu-console.png)

3. V konzole zadejte následující příkaz pro instalaci služby Application Insights a podtržítka balíčky:

    ```
    cd site\wwwroot && npm install applicationinsights && npm install underscore
    ```

    ![Hledání přehledů aplikace](./media/luis-tutorial-appinsights/npm-install.png)

    Počkejte balíčky určené k instalaci:

    ```
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

    Jste hotovi s kartu kudu konzoly prohlížeče.

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Zaznamenání a odesílání LEOŠ výsledky dotazu Application Insights
1. Na kartě aplikace služby Editor prohlížeče otevřete **app.js** souboru.

2. Přidejte následující NPM knihovny pod existující `requires` řádky:

   [!code-javascript[Add NPM packages to app.js](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=12-16 "Add NPM packages to app.js")]

3. Vytvoření objektu Application Insights a použijte nastavení webové aplikace robota aplikace **BotDevInsightsKey**: 

   [!code-javascript[Create the Application Insights object](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=68-80 "Create the Application Insights object")]

4. Přidat **appInsightsLog** funkce:

   [!code-javascript[Add the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=82-109 "Add the appInsightsLog function")]

    Poslední řádek funkce je, kde je přidat data do služby Application Insights. Název události je **LEOŠ výsledky**, jedinečný název kromě jiných telemetrická data shromažďovaná společností robota této webové aplikace. 

5. Použití **appInsightsLog** funkce. Přidejte do každé záměrné dialogového okna:

   [!code-javascript[Use the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=117-118 "Use the appInsightsLog function")]

6. Chcete-li otestovat robota vaší webové aplikace, použijte **testů ve webové Chat** funkce. Měli byste vidět žádné rozdíly, protože všechnu práci není ve službě Application Insights v odpovědi robota.

## <a name="view-luis-entries-in-application-insights"></a>Zobrazení položek LEOŠ ve službě Application Insights
Otevřete službu Application Insights zobrazíte LEOŠ položky. 

1. Na portálu, vyberte **všechny prostředky** pak filtrovat podle robota název webové aplikace. Klikněte na prostředek s typem **Application Insights**. Ikona pro službu Application Insights je žárovky. 

    ![Hledání přehledů aplikace](./media/luis-tutorial-appinsights/search-for-app-insights.png)



2. Když prostředek otevře, klikněte na **vyhledávání** ikonu lupy v pravém panelu. Nový panel na správné zobrazí. V závislosti na tom, kolik telemetrická data nachází, panelu může trvat druhý k zobrazení. Vyhledejte `LUIS-results` a stiskněte klávesu enter na klávesnici. V seznamu je zúžit právě LEOŠ výsledky dotazu přidat v tomto kurzu.

    ![Filtrovat, aby závislosti](./media/luis-tutorial-appinsights/app-insights-filter.png)

3. Vyberte na horní položku. Nové okno zobrazí podrobnější data, včetně vlastních dat pro dotaz LEOŠ úplně vpravo. Data obsahují nejvyšší záměr a jeho skóre.

    ![Podrobnosti o závislosti](./media/luis-tutorial-appinsights/app-insights-detail.png)

    Až skončíte, vyberte úplně vpravo nahoře **X** se vraťte do seznamu položek závislostí. 


> [!Tip]
> Pokud chcete uložit v seznamu závislostí a vraťte se k němu později, klikněte na **... Další** a klikněte na tlačítko **uložit oblíbené**.

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Dotaz Application Insights pro záměr, skóre a utterance
Application Insights vám dává k dotazování dat pomocí [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) jazyka, i jako export jeho [PowerBI](https://powerbi.microsoft.com). 

1. Klikněte na **Analytics** v horní části závislost výpis nad pole filtru. 

    ![Tlačítko Analytics](./media/luis-tutorial-appinsights/analytics-button.png)

2. Otevře se nové okno s okno dotazu v horní části a v okně data tabulky nižší, než je. Pokud jste použili databáze před, je dobře známé toto uspořádání. Dotaz obsahuje všechny položky z posledních 24 hodin od s názvem `LUIS-results`. **CustomDimensions** sloupec má LEOŠ výsledky dotazu jako dvojice název/hodnota.

    ![Okno dotazu Analytics](./media/luis-tutorial-appinsights/analytics-query-window.png)

3. Vysunout nejvyšší záměr, skóre a utterance, přidejte následující nad poslední řádek v okně dotazu:

    ```SQL
    | extend topIntent = tostring(customDimensions.LUIS_intent_intent)
    | extend score = todouble(customDimensions.LUIS_intent_score)
    | extend utterance = tostring(customDimensions.LUIS_text)
    ```

4. Spusťte dotaz. Posuňte se úplně vpravo v tabulce data. Nové sloupce topIntent, skóre a utterance jsou k dispozici. Klikněte na tento sloupec topIntent seřadit.

    ![Horní záměr Analytics](./media/luis-tutorial-appinsights/app-insights-top-intent.png)


Další informace o [Kusto dotazovací jazyk](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-queries) nebo [export dat do PowerBi](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>Další postup

Další informace, které chcete přidat k datům přehledů aplikace obsahuje ID aplikace, ID verze, datum poslední změny modelu, datum posledního train, datum posledního publikování. Tyto hodnoty lze získat buď z adresu URL koncového bodu (ID aplikace a ID verze), nebo [vytváření rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) volat potom nastavit v nastavení webové aplikace robota a vyžádat odtud.  

Pokud používáte stejného předplatného. koncový bod pro více než jednu aplikaci LEOŠ, měli byste také zahrnout ID předplatného a vlastnost, že je sdílený klíč. 

> [!div class="nextstepaction"]
> [Další informace o příklad utterances](luis-how-to-add-example-utterances.md)
