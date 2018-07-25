---
title: Přidat LUIS data do Application Insights pomocí jazyka C# | Dokumentace Microsoftu
titleSuffix: Azure
description: Vytvořte robota, integruje se službou LUIS aplikace a služby Application Insights pomocí jazyka C#.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/07/2018
ms.author: diberry
ms.openlocfilehash: f1efe305f5659bfab50cee13ac30d56531cc6093
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237787"
---
# <a name="add-luis-results-to-application-insights-from-a-web-app-bot"></a>Přidání služby LUIS výsledky do Application Insights z použijete web app bot
V tomto kurzu přidá informace o odpovědi LUIS k [Application Insights](https://azure.microsoft.com/services/application-insights/) úložiště dat telemetrie. Až budete mít data, můžete ji dotazovat s Kusto jazyk nebo Power BI k analýze, agregovat a vytváření sestav o záměry a entity utterance v reálném čase. Tato analýza pomůže zároveň pomáhá určit, pokud by měl přidat nebo upravit záměry a entity aplikace LUIS.

V tomto kurzu se naučíte:

> [!div class="checklist"]
* Přidejte Application Insights do webové aplikace robota
* Zaznamenávat a odesílat LUIS výsledků dotazu do Application Insights
* Dotaz Application Insights pro hlavní záměr, skóre a utterance

## <a name="prerequisites"></a>Požadavky

* Váš robot LUIS webové aplikace z **[předchozí kurz o službě](luis-csharp-tutorial-build-bot-framework-sample.md)** pomocí Application Insights zapnuté. 
* [Visual Studio 2017](https://www.visualstudio.com/downloads/) nainstalovány místně na vašem počítači.

> [!Tip]
> Pokud ještě nemáte předplatné, si můžete zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/).

Veškerý kód v tomto kurzu je k dispozici na [úložišti github s ukázkami LUIS](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/csharp) a každý řádek spojený s tímto kurzem je zakomentovaný s `//LUIS Tutorial:`. 

## <a name="review-luis-web-app-bot"></a>Zkontrolujte LUIS web app bot
Tento kurz předpokládá, že budete mít kód vypadá podobně jako následující nebo, že jste dokončili [další kurz](luis-csharp-tutorial-build-bot-framework-sample.md): 

   [!code-csharp[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs "Web app bot with LUIS")]

## <a name="application-insights-in-web-app-bot"></a>Application Insights v použijete web app bot
V současné době služba Application Insights přidané jako součást vytváření služby web app bot, shromažďuje telemetrii celkový stav pro robota. Neshromažďuje informace o odpovědi služby LUIS. K analýze a vylepšení služby LUIS, budete potřebovat informace LUIS odpovědi.  

Abyste mohli zaznamenat odpovědi LUIS, použijete web app bot musí **[Application Insights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** nainstalovaná a nakonfigurovaná pro projekt. 

## <a name="download-web-app-bot"></a>Stáhněte si web app bot
Použití [Visual Studio 2017](https://www.visualstudio.com/downloads/) přidat a nakonfigurovat Application Insights pro webové aplikace robota. Chcete-li použít použijete web app bot v sadě Visual Studio, stáhněte si kód webové aplikace robota.

1. Na webu Azure Portal, pro web app bot, vyberte **sestavení**.

    ![Výběr sestavení na portálu](./media/luis-tutorial-bot-csharp-appinsights/download-build-menu.png)

2. Vyberte **soubor zip ke stažení** a počkejte, dokud tento soubor je připravený.

    ![Stáhnout soubor zip](./media/luis-tutorial-bot-csharp-appinsights/download-link.png)

3. Vyberte **soubor zip ke stažení** v místním okně. Zapamatujte si umístění v počítači, budete ji potřebovat v další části.

    ![Stáhnout soubor zip – místní nabídka](./media/luis-tutorial-bot-csharp-appinsights/download-popup.png)

## <a name="open-solution-in-visual-studio-2017"></a>Otevřete řešení v sadě Visual Studio 2017

1. Rozbalte soubor do nějaké složky. 

2. Otevřít Visual Studio 2017 a otevřete soubor řešení `Microsoft.Bot.Sample.LuisBot.sln`. Pokud se zobrazí upozornění zabezpečení, vyberte "OK".

    ![Otevřete řešení v sadě Visual Studio 2017](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-security-warning.png)

3. Visual Studio je potřeba přidat závislosti do řešení. V **Průzkumníka řešení**, klikněte pravým tlačítkem na **odkazy**a vyberte **spravovat balíčky NuGet...** . 

    ![Správa balíčků NuGet](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-manage-nuget-packages.png)

4. Správce balíčků NuGet se zobrazí seznam nainstalovaných balíčků. Vyberte **obnovení** v žlutý pruh. Počkejte na dokončení procesu obnovení.

    ![Obnovení balíčků NuGet](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-restore-packages.png)

## <a name="add-application-insights-to-the-project"></a>Přidejte Application Insights do projektu
Nainstalujte a nakonfigurujte Application Insights v sadě Visual Studio. 

1. V sadě Visual Studio 2017, v horní nabídce vyberte **projektu**a pak vyberte **přidat Telemetrii Application Insights...** .

2. V **konfigurace Application Insights** okně **začít zdarma**

    ![Spusťte konfiguraci Application Insights](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configure-app-insights.png)

3. Registrace aplikace pomocí Application Insights. Může se stát, že je nutné zadat přihlašovací údaje Azure portal. 

4. Visual Studio přidá Application Insights do projektu, zobrazení stavu, jak to dělá. 

    ![Stav Application Insights](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-adding-application-insights-to-project.png)

    Po dokončení procesu **konfigurace Application Insights** zobrazuje stav průběhu. 

    ![Stav průběhu Application Insights](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configured-application-insights-to-project.png)

    **Povolit shromažďování trasování** je červené, což znamená, není povolený. V tomto kurzu nepoužívá funkci. 

## <a name="build-and-resolve-errors"></a>Sestavení a řešení chyb

1. Sestavte řešení tak, že vyberete **sestavení** nabídce pak vyberte **znovu sestavit řešení**. Počkejte na dokončení sestavení. 

2. Pokud se sestavení nezdaří s `CS0104` chyby, je potřeba je opravit. V `Controllers` složky v `MessagesController.cs file`, opravte nejednoznačný využití `Activity` typu jsou typ aktivity typu konektoru. Chcete-li to provést, změňte název `Activity` na řádcích 22 a 36 z `Activity` k `Connector.Activity`. Znovu sestavte řešení. Měla by existovat žádné další chyby buildu.

    Úplný zdroj tohoto souboru je:

    [!code-csharp[MessagesController.cs file](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/MessagesController.cs "MessagesController.cs file")]

## <a name="publish-project-to-azure"></a>Publikování projektu do Azure
**Application Insights** balíčku je nyní v projektu a správně nakonfigurovaný pro svoje přihlašovací údaje na webu Azure Portal. Změny pro projekt je třeba publikovat zpět do Azure.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na název projektu a pak vyberte **publikovat**.

    ![Publikovat na portál projektu](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish.png)

2. V **publikovat** okně **vytvořit nový profil**.

    ![Publikovat na portál projektu](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-1.png)

3. Vyberte **importovat profil**a vyberte **OK**.

    ![Publikovat na portál projektu](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-2.png)

4. V **importovat soubor nastavení publikování** windows, přejděte do složky vašeho projektu, přejděte `PostDeployScripts` složky, vyberte soubor, který končí na `.PublishSettings`a vyberte `Open`. Nyní jste nakonfigurovali publikování pro tento projekt. 

5. Publikování místního zdrojového kódu do služby Bot Service tak, že vyberete **publikovat** tlačítko. **Výstup** okno zobrazuje stav. Zbývající část tohoto kurzu je dokončit na webu Azure Portal. Zavřete Visual Studio 2017. 

## <a name="open-three-browser-tabs"></a>Otevřete tři karty prohlížeče
Na webu Azure Portal najít použijete web app bot a otevřete jej. Následující kroky používají tři různá zobrazení použijete web app bot. Může být jednodušší mají tři samostatné karty otevřete v prohlížeči: 
  
>  * Testování webového chatu
>  * Editor kódu online sestavení/Open-> App Service Editor
>  * Konzola Kudu Editor/Open služba aplikace -> konzole diagnostiky

## <a name="modify-basicluisdialogcs-code"></a>Úprava kódu BasicLuisDialog.cs

1. V **App Service Editor** záložku prohlížeče, otevřete `BasicLuisDialog.cs` souboru.

2. Přidejte následující závislost NuGet v rámci existující `using` řádky:

   [!code-csharp[Add using statement](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=11-12 "Add using statement")]

3. Přidat `LogToApplicationInsights` funkce:

   [!code-csharp[Add the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=61-92 "Add the LogToApplicationInsights function")]

    Instrumentační klíč Application Insights se už bot app webové aplikace nastavení s názvem `BotDevInsightsKey`. 

    Poslední řádek funkce přidá data do Application Insights. Je název pro trasovacího `LUIS`, jedinečný název kromě další telemetrická data shromážděná tento web app bot. Všechny vlastnosti jsou také s předponou `LUIS_` tak uvidíte, jaká data v tomto kurzu přidá do informace, které je dán použijete web app bot porovnání.

4. Volání `LogToApplicationInsights` funkce v horní části `ShowLuisResult` funkce:

   [!code-csharp[Use the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=114-115 "Use the LogToApplicationInsights function")]

## <a name="build-web-app-bot"></a>Sestavit web app bot
1. Sestavte robota webové aplikace v jednom ze dvou způsobů. Prvním způsobem je klikněte pravým tlačítkem na `build.cmd` v **App Service Editor**a pak vyberte **spustit z konzoly**. Výstup konzoly zobrazí a dokončí se `Finished successfully.`

2. Pokud to úspěšně nedokončí, musíte otevřete konzolu, přejděte na skript a spusťte ho pomocí následujících kroků. V **App Service Editor**, na horním panelu modrá, vyberte název vašeho robota a potom vyberte **otevřete konzoly Kudu** v rozevíracím seznamu.

    ![Konzola Kudu otevřít](./media/luis-tutorial-bot-csharp-appinsights/open-kudu-console.png)

3. V okně konzoly zadejte následující příkaz:

    ```
    cd site\wwwroot && build.cmd
    ```

    Počkejte na dokončení se sestavení `Finished successfully.`

## <a name="test-the-web-app-bot"></a>Testování použijete web app bot

1. Chcete-li otestovat použijete web app bot, otevřete **testování ve Web Chat** funkce na portálu. 

2. Zadejte větu `Coffee bar on please`.  

    ![Testování použijete web app bot chatu](./media/luis-tutorial-bot-csharp-appinsights/test-in-web-chat.png)

3. Měli byste vidět žádné rozdíly v odpovědi chatovací robot. Změna odesílá data do Application Insights, není v robota odpovědi. Zadejte několik další projevů je trochu více dat ve službě Application Insights:

```
Please deliver a pizza
Turn off all the lights
Turn on the hall light
```

## <a name="view-luis-entries-in-application-insights"></a>Zobrazení LUIS položky ve službě Application Insights
Otevřete službu Application Insights zobrazíte položky LUIS. 

1. Na portálu vyberte **všechny prostředky** pak filtrovat podle názvu webové aplikace robota. Klikněte na prostředek s typem **Application Insights**. Ikona pro službu Application Insights je žárovky. 

    ![Vyhledávání pro službu app insights](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights.png)

2. Pokud prostředek se otevře, klikněte na **hledání** ikonu na lupu v pravém panelu. Nový panel zobrazuje správné. V závislosti na tom, kolik dat telemetrie nenajde, panelu může chvíli trvat, chcete-li zobrazit. Vyhledejte `LUIS`. V seznamu je zúžit jen LUIS výsledky dotazu přidat v tomto kurzu.

    ![Hledat trasování](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace.png)

3. Vyberte na horní položku. Nové okno zobrazí podrobnější data, včetně vlastních dat pro dotaz LUIS úplně vpravo. Data obsahují hlavní záměr a jeho skóre.

    ![Projděte si položku trasování](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace-item.png)

    Jakmile budete hotovi, vyberte úplně vpravo nahoře **X** se vraťte do seznamu položek závislostí. 


> [!Tip]
> Pokud chcete uložit seznam závislostí a vrátit se k němu později, klikněte na **... Další** a klikněte na tlačítko **uložit oblíbenou**.

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Dotaz Application Insights pro záměr, skóre a utterance
Služba Application Insights poskytuje výkonné nástroje pro dotazování dat pomocí služby [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) jazyka, stejně jako export umožňuje [PowerBI](https://powerbi.microsoft.com). 

1. Klikněte na **Analytics** v horní části závislosti výpis výše pole filtru. 

    ![Tlačítko Analytics](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-analytics-button.png)

2. Otevře se okno nové okno dotazu v horní části a okno tabulky dat pod ním. Pokud jste použili databází před, je toto uspořádání zkušenosti. Dotaz obsahuje všechny položky z posledních 24 hodin, počínaje název `LUIS`. **CustomDimensions** sloupec má LUIS výsledky dotazu jako dvojice název/hodnota.

    ![Výchozí analytická sestava](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-1.png)

3. Vyžádá si hlavní záměr, skóre a utterance, přidejte následující přímo nad poslední řádek v okně dotazu:

    ```SQL
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

4. Spusťte dotaz. Posuňte se úplně vpravo v datové tabulce. Nové sloupce topIntent, skóre a utterance jsou k dispozici. Klikněte na sloupec topIntent seřadit.

    ![Vlastní analytická sestava](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-2.png)


Další informace o [Kusto dotazovací jazyk](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-queries) nebo [export dat do Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 


## <a name="learn-more-about-bot-framework"></a>Další informace o rozhraní Bot Framework
Další informace o [Bot Framework](https://dev.botframework.com/).

## <a name="next-steps"></a>Další postup

Další informace, které chcete přidat do application insights data zahrnuje ID aplikace, ID verze, datum poslední změny modelu, datum posledního trénování, datum posledního publikování. Tyto hodnoty lze získat buď z adresu URL koncového bodu (ID aplikace a ID verze) nebo z [vytváření rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) volání pak v nastavení web app bot a získaná z něj.  

Pokud používáte stejné předplatné koncový bod pro více než jednu aplikaci LUIS, by měl také obsahovat ID předplatného a vlastnost s informacemi o tom, že se jedná o sdílený klíč. 

> [!div class="nextstepaction"]
> [Další informace o příklad projevy](luis-how-to-add-example-utterances.md)
