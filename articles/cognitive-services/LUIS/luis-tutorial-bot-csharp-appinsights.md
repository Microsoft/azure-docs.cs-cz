---
title: Přidat LEOŠ data do služby Application Insights pomocí jazyka C# | Microsoft Docs
titleSuffix: Azure
description: Sestavení robotu integrované s LEOŠ aplikace a služby Application Insights pomocí jazyka C#.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/07/2018
ms.author: v-geberr
ms.openlocfilehash: 52b6ae224b0e8da12eb4903f5100a6e5cc39704d
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "35343929"
---
# <a name="add-luis-results-to-application-insights-from-a-web-app-bot"></a>Přidat LEOŠ výsledky do služby Application Insights z robotu webové aplikace
V tomto kurzu přidá LEOŠ odpovědi informace, které [Application Insights](https://azure.microsoft.com/services/application-insights/) telemetrická data úložiště. Jakmile máte tato data, můžete ho dotazování pomocí jazyka Kusto nebo Power BI a analyzovat, agregaci a vytváření sestav o záměry a entity utterance v reálném čase. Tato analysis pomáhá při určení, pokud by měla přidat nebo upravit tříd Intent a entity LEOŠ aplikace.

V tomto kurzu se naučíte:

> [!div class="checklist"]
* Přidejte Application Insights do robotu webové aplikace
* Zaznamenání a odesílání LEOŠ výsledky dotazu Application Insights
* Dotaz pro horní záměr, skóre a utterance Application Insights

## <a name="prerequisites"></a>Požadavky

* Vaše robota LEOŠ webové aplikace z **[předchozí kurzu](luis-csharp-tutorial-build-bot-framework-sample.md)** s Application Insights zapnutý. 
* [Visual Studio 2017](https://www.visualstudio.com/downloads/) nainstalovány místně na vašem počítači.

> [!Tip]
> Pokud již nemáte předplatné, můžete si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/).

Kód v tomto kurzu je k dispozici na [úložiště github LEOŠ-Samples](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/csharp) a každý řádek přidružený tento kurz je označeno jako komentář s `//LUIS Tutorial:`. 

## <a name="review-luis-web-app-bot"></a>Zkontrolujte LEOŠ webové aplikace robota
Tento kurz předpokládá, že máte kódu vypadá podobně jako následující nebo že jste dokončili [další kurz](luis-csharp-tutorial-build-bot-framework-sample.md): 

   [!code-csharp[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs "Web app bot with LUIS")]

## <a name="application-insights-in-web-app-bot"></a>Application Insights ve webové aplikaci robota
V současné době službu Application Insights, přidat jako součást služby vytvoření webové aplikace robota shromažďuje celkový stav telemetrii robota. Neshromažďuje informace LEOŠ odpovědi. Pro analýzy a vylepšení LEOŠ, budete potřebovat informace LEOŠ odpovědi.  

Aby bylo možné zachytit LEOŠ odpovědi, potřebuje robota aplikace webové **[Application Insights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** nainstalovaný a nakonfigurovaný pro projekt. 

## <a name="download-web-app-bot"></a>Stáhnout robota webové aplikace
Použití [Visual Studio 2017](https://www.visualstudio.com/downloads/) k přidání a konfigurace pro robota aplikace webové služby Application Insights. Chcete-li použít robota aplikaci web v sadě Visual Studio, stáhněte si robota kódu webové aplikace.

1. Na portálu Azure pro aplikaci robota web, vyberte **sestavení**.

    ![Výběr sestavení na portálu](./media/luis-tutorial-bot-csharp-appinsights/download-build-menu.png)

2. Vyberte **stažení souboru zip** a počkejte, až je připravený soubor.

    ![Stáhněte si soubor zip](./media/luis-tutorial-bot-csharp-appinsights/download-link.png)

3. Vyberte **stažení souboru zip** v místním okně. Mějte na paměti, umístění v počítači, budete ho potřebovat v další části.

    ![Stáhněte si soubor zip automaticky otevřeném okně.](./media/luis-tutorial-bot-csharp-appinsights/download-popup.png)

## <a name="open-solution-in-visual-studio-2017"></a>Otevřete řešení v Visual Studio 2017

1. Extrahujte soubor do složky. 

2. Otevřete Visual Studio 2017 a otevřete soubor řešení `Microsoft.Bot.Sample.LuisBot.sln`. Pokud se objeví upozornění zabezpečení, vyberte možnost "OK".

    ![Otevřete řešení v Visual Studio 2017](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-security-warning.png)

3. Visual Studio je potřeba do řešení přidat závislosti. V **Průzkumníku řešení**, klikněte pravým tlačítkem na **odkazy**a vyberte **spravovat balíčky NuGet...** . 

    ![Správa balíčků NuGet](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-manage-nuget-packages.png)

4. Správce balíčků NuGet zobrazuje seznam nainstalovaných balíčků. Vyberte **obnovení** v žlutý pruh. Počkejte na dokončení procesu obnovení.

    ![Obnovení balíčků NuGet](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-restore-packages.png)

## <a name="add-application-insights-to-the-project"></a>Do projektu přidejte Application Insights
Instalace a konfigurace Application Insights v sadě Visual Studio. 

1. V aplikaci Visual Studio 2017 v horní nabídce vyberte **projektu**, pak vyberte **přidat Telemetrii Application Insights...** .

2. V **konfiguraci statistiky aplikace** vyberte **spustit volné**

    ![Spuštění konfigurace Application Insights](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configure-app-insights.png)

3. Svou aplikaci zaregistrujte pomocí nástroje Application Insights. Budete muset zadat přihlašovací údaje Azure portálu. 

4. Visual Studio přidá Application Insights do projektu, zobrazení stavu, jako to dělá. 

    ![Přehled stavu aplikace.](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-adding-application-insights-to-project.png)

    Po dokončení procesu **konfiguraci statistiky aplikace** zobrazuje stav průběhu. 

    ![Application Insights průběh](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configured-application-insights-to-project.png)

    **Povolit trasování kolekce** je červená, což znamená, není povoleno. V tomto kurzu nepoužívá funkci. 

## <a name="build-and-resolve-errors"></a>Sestavení a řešení chyb

1. Sestavte řešení výběrem **sestavení** nabídce pak vyberte **znovu sestavit řešení**. Počkejte na dokončení sestavení. 

2. Když je sestavení neúspěšné s `CS0104` chyby, je třeba opravit. V `Controllers` složky v `MessagesController.cs file`, opravte nejednoznačný použití `Activity` typu pomocí prefixu typ aktivity s typem konektor. Chcete-li to provést, změňte název `Activity` na řádcích 22 a 36 z `Activity` k `Connector.Activity`. Znovu sestavte řešení. Měla by existovat žádné další chyby sestavení.

    Úplný zdrojový tohoto souboru je:

    [!code-csharp[MessagesController.cs file](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/MessagesController.cs "MessagesController.cs file")]

## <a name="publish-project-to-azure"></a>Publikování projektu v Azure
**Application Insights** balíčku je nyní v projektu a správně nakonfigurován pro svoje přihlašovací údaje na portálu Azure. Změny pro projekt je třeba publikovat zpět do Azure.

1. V **Průzkumníku řešení**, klikněte pravým tlačítkem na název projektu a pak vyberte **publikovat**.

    ![Publikování projektu v portálu](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish.png)

2. V **publikovat** vyberte **vytvořit nový profil**.

    ![Publikování projektu v portálu](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-1.png)

3. Vyberte **importovat profil**a vyberte **OK**.

    ![Publikování projektu v portálu](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-2.png)

4. V **soubor nastavení publikování importu** windows, přejděte do složky projektu, přejděte na `PostDeployScripts` složky, vyberte soubor, který končí v `.PublishSettings`a vyberte `Open`. Nyní máte nakonfigurovaný publikování pro tento projekt. 

5. Publikování místního zdrojového kódu službě robota výběrem **publikovat** tlačítko. **Výstup** okno zobrazuje stav. Zbytek tohoto kurzu je dokončit na portálu Azure. Zavřete Visual Studio 2017. 

## <a name="open-three-browser-tabs"></a>Otevřete tři karty prohlížeče
Na portálu Azure najít robota aplikaci web a otevřete jej. Následující kroky pomocí tří různých zobrazení robota aplikaci web. Může být snazší mít tři samostatné karty, otevřete v prohlížeči: 
  
>  * Testování v webového chatu
>  * Editor kódu online sestavení nebo Open-> Editor služby aplikace
>  * Aplikace služby Editor/otevřít Kudu konzoly -> konzole diagnostiky

## <a name="modify-basicluisdialogcs-code"></a>Upravit BasicLuisDialog.cs kódu

1. V **App Service Editor** kartu prohlížeče, otevřete `BasicLuisDialog.cs` souboru.

2. Přidejte následující závislost NuGet v rámci existující `using` řádky:

   [!code-csharp[Add using statement](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=11-12 "Add using statement")]

3. Přidat `LogToApplicationInsights` funkce:

   [!code-csharp[Add the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=61-92 "Add the LogToApplicationInsights function")]

    Klíč instrumentace Application Insights je již v aplikaci robota webové aplikace nastavení s názvem `BotDevInsightsKey`. 

    Poslední řádek funkce přidá data do služby Application Insights. Název je trasování je `LUIS`, jedinečný název kromě jiných telemetrická data shromažďovaná společností robota této webové aplikace. Všechny vlastnosti jsou také předponou `LUIS_` abyste viděli, jaká data v tomto kurzu přidá porovnání s informacemi, které je dán robota aplikace web.

4. Volání `LogToApplicationInsights` funkce v horní části `ShowLuisResult` funkce:

   [!code-csharp[Use the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=114-115 "Use the LogToApplicationInsights function")]

## <a name="build-web-app-bot"></a>Vytvoření webové aplikace robota
1. Sestavení robota aplikaci web v jednom ze dvou způsobů. První metoda má klikněte pravým tlačítkem na `build.cmd` v **App Service Editor**, pak vyberte **spustit z konzoly**. Výstup konzoly zobrazí a dokončí se `Finished successfully.`

2. Pokud to úspěšně nedokončí, budete muset otevřete konzolu, přejděte na skript a spusťte ji pomocí následujících kroků. V **App Service Editor**na horním panelu blue, vyberte název vaší robota a pak vyberte **otevřít konzolu Kudu** v rozevíracím seznamu.

    ![Otevřete Kudu konzoly](./media/luis-tutorial-bot-csharp-appinsights/open-kudu-console.png)

3. V okně konzoly zadejte následující příkaz:

    ```
    cd site\wwwroot && build.cmd
    ```

    Počkejte na dokončení s sestavení `Finished successfully.`

## <a name="test-the-web-app-bot"></a>Testování robota aplikaci web

1. Chcete-li otestovat robota vaší webové aplikace, otevřete **testů ve webové Chat** funkce na portálu. 

2. Zadejte fráze `Coffee bar on please`.  

    ![Test webové aplikace robota v chatu](./media/luis-tutorial-bot-csharp-appinsights/test-in-web-chat.png)

3. Měli byste vidět žádné rozdíly v chatbot odpovědi. Změna je odesílání dat do Application Insights, není v robota odpovědi. Zadejte pár další utterances, takže ještě více dat ve službě Application Insights:

```
Please deliver a pizza
Turn off all the lights
Turn on the hall light
```

## <a name="view-luis-entries-in-application-insights"></a>Zobrazení položek LEOŠ ve službě Application Insights
Otevřete službu Application Insights zobrazíte LEOŠ položky. 

1. Na portálu, vyberte **všechny prostředky** pak filtrovat podle robota název webové aplikace. Klikněte na prostředek s typem **Application Insights**. Ikona pro službu Application Insights je žárovky. 

    ![Hledání přehledů aplikace](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights.png)

2. Když prostředek otevře, klikněte na **vyhledávání** ikonu lupy v pravém panelu. Nový panel na správné zobrazí. V závislosti na tom, kolik telemetrická data nachází, panelu může trvat druhý k zobrazení. Vyhledejte `LUIS`. V seznamu je zúžit právě LEOŠ výsledky dotazu přidat v tomto kurzu.

    ![Vyhledejte trasování](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace.png)

3. Vyberte na horní položku. Nové okno zobrazí podrobnější data, včetně vlastních dat pro dotaz LEOŠ úplně vpravo. Data obsahují nejvyšší záměr a jeho skóre.

    ![Projděte si položku trasování](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace-item.png)

    Až skončíte, vyberte úplně vpravo nahoře **X** se vraťte do seznamu položek závislostí. 


> [!Tip]
> Pokud chcete uložit v seznamu závislostí a vraťte se k němu později, klikněte na **... Další** a klikněte na tlačítko **uložit oblíbené**.

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Dotaz Application Insights pro záměr, skóre a utterance
Application Insights vám dává k dotazování dat pomocí [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) jazyka, i jako export jeho [PowerBI](https://powerbi.microsoft.com). 

1. Klikněte na **Analytics** v horní části závislost výpis nad pole filtru. 

    ![Tlačítko Analytics](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-analytics-button.png)

2. Otevře se nové okno s okno dotazu v horní části a v okně data tabulky nižší, než je. Pokud jste použili databáze před, je dobře známé toto uspořádání. Dotaz obsahuje všechny položky z posledních 24 hodin od s názvem `LUIS`. **CustomDimensions** sloupec má LEOŠ výsledky dotazu jako dvojice název/hodnota.

    ![Sestava analýzy výchozí](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-1.png)

3. Vysunout nejvyšší záměr, skóre a utterance, přidejte následující nad poslední řádek v okně dotazu:

    ```SQL
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

4. Spusťte dotaz. Posuňte se úplně vpravo v tabulce data. Nové sloupce topIntent, skóre a utterance jsou k dispozici. Klikněte na tento sloupec topIntent seřadit.

    ![Sestava vlastní analytics](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-2.png)


Další informace o [Kusto dotazovací jazyk](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-queries) nebo [export dat do PowerBi](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 


## <a name="learn-more-about-bot-framework"></a>Další informace o robota Framework
Další informace o [robota Framework](https://dev.botframework.com/).

## <a name="next-steps"></a>Další postup

Další informace, které chcete přidat k datům přehledů aplikace obsahuje ID aplikace, ID verze, datum poslední změny modelu, datum posledního train, datum posledního publikování. Tyto hodnoty lze získat buď z adresu URL koncového bodu (ID aplikace a ID verze), nebo [vytváření rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) volat potom nastavit v nastavení webové aplikace robota a vyžádat odtud.  

Pokud používáte stejného předplatného. koncový bod pro více než jednu aplikaci LEOŠ, měli byste také zahrnout ID předplatného a vlastnost, že je sdílený klíč. 

> [!div class="nextstepaction"]
> [Další informace o příklad utterances](luis-how-to-add-example-utterances.md)
