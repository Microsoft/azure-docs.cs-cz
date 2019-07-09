---
title: Application Insights, C#
titleSuffix: Azure Cognitive Services
description: V tomto kurzu přidá robotů a Language Understanding informace do úložiště dat telemetrie Application Insights.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/16/2019
ms.author: diberry
ms.openlocfilehash: 720352403fd5f5937669f9838f3974cb0d3f8797
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657783"
---
# <a name="add-luis-results-to-application-insights-from-a-bot-in-c"></a>Přidání LUIS výsledky do Application Insights z robota vC#

V tomto kurzu přidá robotů a Language Understanding informace, které [Application Insights](https://azure.microsoft.com/services/application-insights/) úložiště dat telemetrie. Až budete mít data, můžete ji dotazovat s Kusto jazyka nebo Power BI k analýze, agregovat a vytváření sestav o záměry a entity utterance v reálném čase. Tato analýza pomůže zároveň pomáhá určit, pokud by měl přidat nebo upravit záměry a entity aplikace LUIS.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zaznamenání bot a Language understanding dat ve službě Application Insights
> * Application Insights dotazovat na data Language Understanding

## <a name="prerequisites"></a>Požadavky

* Azure bot service robota, vytvořené pomocí Application Insights aktivovaných.
* Stáhnout kód bot z předchozí bot  **[kurzu](luis-csharp-tutorial-bf-v4.md)** . 
* [Emulátor robota](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

Veškerý kód v tomto kurzu je k dispozici na [úložiště GitHub Azure-Samples Language Understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-csharp-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Přidejte Application Insights do projektu webové aplikace robota

V současné době služba Application Insights, použít v tomto použijete web app bot shromažďuje telemetrii celkový stav pro robota. Neshromažďuje informace LUIS. 

Aby bylo možné zachytit informace LUIS, použijete web app bot potřebuje **[Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** balíček NuGet nainstalovaná a nakonfigurovaná.  

1. Ze sady Visual Studio přidejte závislosti do řešení. V **Průzkumníka řešení**, klikněte pravým tlačítkem na název projektu a vyberte **spravovat balíčky NuGet...** . Správce balíčků NuGet se zobrazí seznam nainstalovaných balíčků. 
1. Vyberte **Procházet** vyhledejte **Microsoft.ApplicationInsights**.
1. Nainstalujte balíček. 

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Zaznamenávat a odesílat LUIS výsledků dotazu do Application Insights

1. Otevřít `LuisHelper.cs` souboru a nahraďte jeho obsah následujícím kódem. **LogToApplicationInsights** metody zaznamenání robotů a LUIS dat a odesílá je do Application Insights jako událost trasování s názvem `LUIS`.

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

## <a name="add-application-insights-instrumentation-key"></a>Přidat Instrumentační klíč Application Insights 

Chcete-li přidat data do application insights, budete potřebovat Instrumentační klíč.

1. V prohlížeči v [webu Azure portal](https://portal.azure.com), najít váš robot **Application Insights** prostředků. Její název bude mít maximálně bodu robotů také název a potom náhodných znaků na konci názvu, jako například `luis-csharp-bot-johnsmithxqowom`. 
1. U prostředku Application Insights na **přehled** stránky, zkopírujte **Instrumentační klíč**.
1. V sadě Visual Studio, otevřete **appsettings.json** soubor v kořenové složce projektu robota. Tento soubor obsahuje všechny proměnné prostředí.
1. Přidejte novou proměnnou `BotDevAppInsightsKey` s hodnotou svůj Instrumentační klíč. Hodnota v by měl být v uvozovkách. 

## <a name="build-and-start-the-bot"></a>Sestavit a spustit robota

1. V sadě Visual Studio sestavte a spusťte robota. 
1. Spustit emulátor robotů a otevřete robota. To [krok](luis-csharp-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) je k dispozici v předchozím kurzu.

1. Zeptejte se robota. To [krok](luis-csharp-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent) je k dispozici v předchozím kurzu.

## <a name="view-luis-entries-in-application-insights"></a>Zobrazení LUIS položky ve službě Application Insights

Otevřete službu Application Insights zobrazíte položky LUIS. Může trvat několik minut, než se data zobrazí ve službě Application Insights.

1. V [webu Azure portal](https://portal.azure.com), otevřete prostředek Application Insights bodu robotů také. 
1. Pokud prostředek se otevře, vyberte **hledání** a hledejte veškerá data za posledních **30 minut** s typem události **trasování**. Vyberte položku trasování s názvem **LUIS**. 
1. Bot a LUIS informace jsou k dispozici v rámci **vlastní vlastnosti**. 

    ![Zkontrolujte LUIS vlastní vlastnosti uložené ve službě Application Insights](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-csharp.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Dotaz Application Insights pro záměr, skóre a utterance
Služba Application Insights poskytuje výkonné nástroje pro dotazování dat pomocí služby [Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use) jazyka, stejně jako export umožňuje [Power BI](https://powerbi.microsoft.com). 

1. Vyberte **protokolu (Analytics)** . Otevře se okno nové okno dotazu v horní části a okno tabulky dat pod ním. Pokud jste použili databází před, je toto uspořádání zkušenosti. Dotaz představuje váš předchozí filtrovaná data. **CustomDimensions** sloupec má robotů a LUIS informace.
1. Vyžádá si hlavní záměr, skóre a utterance, přidejte následující přímo nad poslední řádek ( `|top...` řádku) v okně dotazu:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent_Name)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

1. Spusťte dotaz. Nové sloupce topIntent, skóre a utterance jsou k dispozici. Vyberte topIntent sloupec pro řazení.

Další informace o [Kusto dotazovací jazyk](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) nebo [export dat do Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 


## <a name="learn-more-about-bot-framework"></a>Další informace o Bot Frameworku

Další informace o [Bot Framework](https://dev.botframework.com/).

## <a name="next-steps"></a>Další postup

Další informace, které chcete přidat do application insights data zahrnuje ID aplikace, ID verze, datum poslední změny modelu, datum posledního trénování, datum posledního publikování. Tyto hodnoty můžete buď být načtena z adresy URL koncového bodu (app ID a verzi) nebo pro vytváření volání rozhraní API pak v nastavení web app bot a získaná z něj.  

Pokud používáte stejné předplatné koncový bod pro více než jednu aplikaci LUIS, by měl také obsahovat ID předplatného a vlastnost s informacemi o tom, že se jedná o sdílený klíč.

> [!div class="nextstepaction"]
> [Další informace o příklad projevy](luis-how-to-add-example-utterances.md)
