---
title: 'Kurz: Application Insights C# – Luis'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu se pro Application Insights úložiště dat telemetrie přidají informace o robotech a Language Understanding.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: 93a75945025b2ab98541f1e1cf678145ddff0123
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70388000"
---
# <a name="tutorial-add-luis-results-to-application-insights-from-a-bot-in-c"></a>Kurz: Přidání výsledků LUIS do Application Insights z robota vC#

V tomto kurzu se pro [Application Insights](https://azure.microsoft.com/services/application-insights/) úložiště dat telemetrie přidají informace o robotech a Language Understanding. Jakmile tato data máte, můžete je pomocí jazyka Kusto nebo Power BI analyzovat, agregovat a sestavovat v reálném čase a entity utterance. Tato analýza pomůže zároveň pomáhá určit, pokud by měl přidat nebo upravit záměry a entity aplikace LUIS.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zachytávání dat o robotech a jazycích v Application Insights
> * Dotaz na Application Insights pro Language Understanding data

## <a name="prerequisites"></a>Požadavky

* Robot služby Azure bot Service se vytvořil s povoleným Application Insights.
* Stáhli jsme kód robota z předchozího **[kurzu](luis-csharp-tutorial-bf-v4.md)** bot. 
* [Emulátor robota](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

Veškerý kód v tomto kurzu je k dispozici v [úložišti Azure-samples Language Understanding GitHubu](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-csharp-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Přidat Application Insights do projektu bot webové aplikace

V současné době služba Application Insights, použít v tomto použijete web app bot shromažďuje telemetrii celkový stav pro robota. Neshromažďuje informace o LUIS. 

Aby bylo možné zachytit LUIS informace, robot webové aplikace musí mít nainstalovaný a nakonfigurovaný balíček NuGet **[Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** .  

1. V aplikaci Visual Studio přidejte závislost do řešení. V **Průzkumník řešení**klikněte pravým tlačítkem myši na název projektu a vyberte možnost **Spravovat balíčky NuGet...** . Správce balíčků NuGet se zobrazí seznam nainstalovaných balíčků. 
1. Vyberte **Procházet** a pak vyhledejte **Microsoft. ApplicationInsights**.
1. Nainstalujte balíček. 

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Zaznamenávat a odesílat LUIS výsledků dotazu do Application Insights

1. `LuisHelper.cs` Otevřete soubor a nahraďte jeho obsah následujícím kódem. Metoda **LogToApplicationInsights** zachytí data robota a Luis a pošle je Application Insights jako událost trasování s názvem `LUIS`.

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;
    using Microsoft.ApplicationInsights;
    using System.Collections.Generic;
    
    namespace Microsoft.BotBuilderSamples
    {
        public static class LuisHelper
        {
            public static async Task<BookingDetails> ExecuteLuisQuery(IConfiguration configuration, ILogger logger, ITurnContext turnContext, CancellationToken cancellationToken)
            {
                var bookingDetails = new BookingDetails();
    
                try
                {
                    // Create the LUIS settings from configuration.
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]
                    );
    
                    var recognizer = new LuisRecognizer(luisApplication);
    
                    // The actual call to LUIS
                    var recognizerResult = await recognizer.RecognizeAsync(turnContext, cancellationToken);
    
                    LuisHelper.LogToApplicationInsights(configuration, turnContext, recognizerResult);
    
                    var (intent, score) = recognizerResult.GetTopScoringIntent();
                    if (intent == "Book_flight")
                    {
                        // We need to get the result from the LUIS JSON which at every level returns an array.
                        bookingDetails.Destination = recognizerResult.Entities["To"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
                        bookingDetails.Origin = recognizerResult.Entities["From"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
    
                        // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                        // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                        bookingDetails.TravelDate = recognizerResult.Entities["datetime"]?.FirstOrDefault()?["timex"]?.FirstOrDefault()?.ToString().Split('T')[0];
                    }
                }
                catch (Exception e)
                {
                    logger.LogWarning($"LUIS Exception: {e.Message} Check your LUIS configuration.");
                }
    
                return bookingDetails;
            }
            public static void LogToApplicationInsights(IConfiguration configuration, ITurnContext turnContext, RecognizerResult result)
            {
                // Create Application Insights object
                TelemetryClient telemetry = new TelemetryClient();
    
                // Set Application Insights Instrumentation Key from App Settings
                telemetry.InstrumentationKey = configuration["BotDevAppInsightsKey"];
    
                // Collect information to send to Application Insights
                Dictionary<string, string> logProperties = new Dictionary<string, string>();
    
                logProperties.Add("BotConversation", turnContext.Activity.Conversation.Name);
                logProperties.Add("Bot_userId", turnContext.Activity.Conversation.Id);
    
                logProperties.Add("LUIS_query", result.Text);
                logProperties.Add("LUIS_topScoringIntent_Name", result.GetTopScoringIntent().intent);
                logProperties.Add("LUIS_topScoringIntentScore", result.GetTopScoringIntent().score.ToString());
    
    
                // Add entities to collected information
                int i = 1;
                if (result.Entities.Count > 0)
                {
                    foreach (var item in result.Entities)
                    {
                        logProperties.Add("LUIS_entities_" + i++ + "_" + item.Key, item.Value.ToString());
                    }
                }
    
                // Send to Application Insights
                telemetry.TrackTrace("LUIS", ApplicationInsights.DataContracts.SeverityLevel.Information, logProperties);
    
            }
        }
    }
    ```

## <a name="add-application-insights-instrumentation-key"></a>Přidat Application Insights klíč instrumentace 

Aby bylo možné přidat data do Application Insights, budete potřebovat klíč instrumentace.

1. V prohlížeči v [Azure Portal](https://portal.azure.com)najděte prostředek **Application Insights** robota. Jeho název bude mít většinu názvu bot, potom náhodně znaky na konci názvu, například `luis-csharp-bot-johnsmithxqowom`. 
1. Na Application Insights prostředku na stránce **Přehled** zkopírujte **klíč instrumentace**.
1. V aplikaci Visual Studio otevřete soubor **appSettings. JSON** v kořenovém adresáři projektu bot. Tento soubor obsahuje všechny proměnné prostředí.
1. Přidejte novou proměnnou `BotDevAppInsightsKey` s hodnotou klíče instrumentace. Hodnota v poli by měla být v uvozovkách. 

## <a name="build-and-start-the-bot"></a>Sestavení a spuštění robota

1. V aplikaci Visual Studio Sestavte a spusťte robot. 
1. Spusťte emulátor bot a otevřete robota. Tento [Krok](luis-csharp-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) je k dispozici v předchozím kurzu.

1. Zeptejte se robota na otázku. Tento [Krok](luis-csharp-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent) je k dispozici v předchozím kurzu.

## <a name="view-luis-entries-in-application-insights"></a>Zobrazení LUIS položky ve službě Application Insights

Otevřete službu Application Insights zobrazíte položky LUIS. Může trvat několik minut, než se data zobrazí v Application Insights.

1. V [Azure Portal](https://portal.azure.com)otevřete prostředek Application Insights robota. 
1. Po otevření prostředku vyberte **Hledat** a vyhledejte všechna data za posledních **30 minut** pomocí typu události **Trace**. Vyberte trasování s názvem **Luis**. 
1. Informace robota a LUIS jsou k dispozici v části **vlastní vlastnosti**. 

    ![Kontrola vlastních vlastností LUIS uložených v Application Insights](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-csharp.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Dotaz Application Insights pro záměr, skóre a utterance
Application Insights vám dává oprávnění k dotazování dat pomocí jazyka [Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use) a také k jejich exportu do [Power BI](https://powerbi.microsoft.com). 

1. Vyberte **protokol (Analytics)** . Otevře se okno nové okno dotazu v horní části a okno tabulky dat pod ním. Pokud jste použili databází před, je toto uspořádání zkušenosti. Dotaz představuje vaše předchozí filtrovaná data. Ve sloupci **CustomDimensions** jsou informace robot a Luis.
1. Pokud chcete načíst nejvyšší záměr, skóre a utterance, přidejte následující těsně nad poslední řádek ( `|top...` řádek) v okně dotazu:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent_Name)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

1. Spusťte dotaz. Nové sloupce topIntent, skóre a utterance jsou k dispozici. Vyberte sloupec topIntent, který chcete seřadit.

Přečtěte si další informace o [dotazovacím jazyku Kusto](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) nebo [exportujte data do Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 


## <a name="learn-more-about-bot-framework"></a>Další informace o Bot Frameworku

Další informace o [Bot Framework](https://dev.botframework.com/).

## <a name="next-steps"></a>Další postup

Další informace, které chcete přidat do application insights data zahrnuje ID aplikace, ID verze, datum poslední změny modelu, datum posledního trénování, datum posledního publikování. Tyto hodnoty můžete načíst buď z adresy URL koncového bodu (ID aplikace a ID verze), nebo z volání rozhraní API pro vytváření obsahu, které pak nastavíte v nastavení robota webové aplikace a odtud z něj načtete.  

Pokud používáte stejné předplatné koncový bod pro více než jednu aplikaci LUIS, by měl také obsahovat ID předplatného a vlastnost s informacemi o tom, že se jedná o sdílený klíč.

> [!div class="nextstepaction"]
> [Další informace o příklad projevy](luis-how-to-add-example-utterances.md)
