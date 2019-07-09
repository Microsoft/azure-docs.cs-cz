---
title: Application Insights Node.js
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
ms.openlocfilehash: cfed5477df75350f24e77786117e85b9c728c49a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657758"
---
# <a name="add-luis-results-to-application-insights-from-a-bot-in-nodejs"></a>Přidání LUIS výsledky do Application Insights z robota v Node.js
V tomto kurzu přidá robotů a Language Understanding informace, které [Application Insights](https://azure.microsoft.com/services/application-insights/) úložiště dat telemetrie. Až budete mít data, můžete ji dotazovat s Kusto jazyka nebo Power BI k analýze, agregovat a vytváření sestav o záměry a entity utterance v reálném čase. Tato analýza pomůže zároveň pomáhá určit, pokud by měl přidat nebo upravit záměry a entity aplikace LUIS.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zaznamenání bot a Language understanding dat ve službě Application Insights
> * Application Insights dotazovat na data Language Understanding

## <a name="prerequisites"></a>Požadavky

* Azure bot service robota, vytvořené pomocí Application Insights aktivovaných.
* Stáhnout kód bot z předchozí bot  **[kurzu](luis-nodejs-tutorial-bf-v4.md)** . 
* [Emulátor robota](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

Veškerý kód v tomto kurzu je k dispozici na [úložiště GitHub Azure-Samples Language Understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-nodejs-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Přidejte Application Insights do projektu webové aplikace robota
V současné době služba Application Insights, použít v tomto použijete web app bot shromažďuje telemetrii celkový stav pro robota. Neshromažďuje informace LUIS. 

Aby bylo možné zachytit informace LUIS, použijete web app bot potřebuje **[Application Insights](https://www.npmjs.com/package/applicationinsights)** balíčku NPM nainstalovaná a nakonfigurovaná.  

1. V integrovaném terminálu VSCode v kořenovém adresáři projektu robota, přidejte následující balíčky NPM pomocí příkazu zobrazí: 

    ```console
    npm install applicationinsights && npm install underscore
    ```
    
    **Podtržítka** balíčku se používá k vyrovnání strukturu LUIS JSON, takže jdou snadněji zobrazovat a používat ve službě Application Insights.
    


## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Zaznamenávat a odesílat LUIS výsledků dotazu do Application Insights

1. Ve VSCode, vytvořte nový soubor **appInsightsLog.js** a přidejte následující kód:

    ```javascript
    const appInsights = require('applicationinsights');
    const _ = require("underscore");
    
    // Log LUIS results to Application Insights
    // must flatten as name/value pairs
    var appInsightsLog = (botContext,luisResponse) => {

        appInsights.setup(process.env.MicrosoftApplicationInsightsInstrumentationKey).start();
        const appInsightsClient = appInsights.defaultClient;

        // put bot context and LUIS results into single object
        var data = Object.assign({}, {'botContext': botContext._activity}, {'luisResponse': luisResponse});
    
        // Flatten data into name/value pairs
        flatten = (x, result, prefix) => {
            if(_.isObject(x)) {
                _.each(x, (v, k) => {
                    flatten(v, result, prefix ? prefix + '_' + k : k)
                })
            } else {
                result["LUIS_" + prefix] = x
            }
            return result;
        }
    
        // call fn to flatten data
        var flattenedData = flatten(data, {});
    
        // ApplicationInsights Trace 
        console.log(JSON.stringify(flattenedData));
    
        // send data to Application Insights
        appInsightsClient.trackTrace({message: "LUIS", severity: appInsights.Contracts.SeverityLevel.Information, properties: flattenedData});
    }
    
    module.exports.appInsightsLog = appInsightsLog;
    ```

    Tento soubor přijímá robota kontextu a odpovědi služby luis, sloučí oba objekty a vloží je do **trasování** událostí ve službě application insights. Název události je **LUIS**. 

1. Otevřít **dialogová okna** složku, pak bude **luisHelper.js** souboru. Zahrnout nové **appInsightsLog.js** jako požadovaný soubor a zachycení bot kontextu a LUIS odpovědi. Kompletní kód pro tento soubor je: 

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { LuisRecognizer } = require('botbuilder-ai');
    const { appInsightsLog } = require('../appInsightsLog');
    
    class LuisHelper {
        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {*} logger
         * @param {TurnContext} context
         */
        static async executeLuisQuery(logger, context) {
            const bookingDetails = {};
    
            try {
                const recognizer = new LuisRecognizer({
                    applicationId: process.env.LuisAppId,
                    endpointKey: process.env.LuisAPIKey,
                    endpoint: `https://${ process.env.LuisAPIHostName }`
                }, {}, true);
    
                const recognizerResult = await recognizer.recognize(context);
    
                // APPINSIGHT: Log results to Application Insights
                appInsightsLog(context,recognizerResult);
    
    
                const intent = LuisRecognizer.topIntent(recognizerResult);
    
                bookingDetails.intent = intent;
    
                if (intent === 'Book_flight') {
                    // We need to get the result from the LUIS JSON which at every level returns an array
    
                    bookingDetails.destination = LuisHelper.parseCompositeEntity(recognizerResult, 'To', 'Airport');
                    bookingDetails.origin = LuisHelper.parseCompositeEntity(recognizerResult, 'From', 'Airport');
    
                    // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                    // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                    bookingDetails.travelDate = LuisHelper.parseDatetimeEntity(recognizerResult);
                }
            } catch (err) {
                logger.warn(`LUIS Exception: ${ err } Check your LUIS configuration`);
            }
            return bookingDetails;
        }
    
        static parseCompositeEntity(result, compositeName, entityName) {
            const compositeEntity = result.entities[compositeName];
            if (!compositeEntity || !compositeEntity[0]) return undefined;
    
            const entity = compositeEntity[0][entityName];
            if (!entity || !entity[0]) return undefined;
    
            const entityValue = entity[0][0];
            return entityValue;
        }
    
        static parseDatetimeEntity(result) {
            const datetimeEntity = result.entities['datetime'];
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;
    
            const timex = datetimeEntity[0]['timex'];
            if (!timex || !timex[0]) return undefined;
    
            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }
    
    module.exports.LuisHelper = LuisHelper;
    ```

## <a name="add-application-insights-instrumentation-key"></a>Přidat Instrumentační klíč Application Insights 

Chcete-li přidat data do application insights, budete potřebovat Instrumentační klíč.

1. V prohlížeči v [webu Azure portal](https://portal.azure.com), najít váš robot **Application Insights** prostředků. Její název bude mít maximálně bodu robotů také název a potom náhodných znaků na konci názvu, jako například `luis-nodejs-bot-johnsmithxqowom`. 
1. U prostředku Application Insights na **přehled** stránky, zkopírujte **Instrumentační klíč**.
1. Ve VSCode, otevřete **.env** soubor v kořenové složce projektu robota. Tento soubor obsahuje všechny proměnné prostředí.  
1. Přidejte novou proměnnou `MicrosoftApplicationInsightsInstrumentationKey` s hodnotou svůj Instrumentační klíč. Provést žádné put hodnoty v uvozovkách. 

## <a name="start-the-bot"></a>Spuštění robota

1. V integrovaném terminálu VSCode spusťte robota:
    
    ```console
    npm start
    ```

1. Spustit emulátor robotů a otevřete robota. To [krok](luis-nodejs-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) je k dispozici v předchozím kurzu.

1. Zeptejte se robota. To [krok](luis-nodejs-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent) je k dispozici v předchozím kurzu.

## <a name="view-luis-entries-in-application-insights"></a>Zobrazení LUIS položky ve službě Application Insights

Otevřete službu Application Insights zobrazíte položky LUIS. Může trvat několik minut, než se data zobrazí ve službě Application Insights.

1. V [webu Azure portal](https://portal.azure.com), otevřete prostředek Application Insights bodu robotů také. 
1. Pokud prostředek se otevře, vyberte **hledání** a hledejte veškerá data za posledních **30 minut** s typem události **trasování**. Vyberte položku trasování s názvem **LUIS**. 
1. Bot a LUIS informace jsou k dispozici v rámci **vlastní vlastnosti**. 

    ![Zkontrolujte LUIS vlastní vlastnosti uložené ve službě Application Insights](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-nodejs.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Dotaz Application Insights pro záměr, skóre a utterance
Služba Application Insights poskytuje výkonné nástroje pro dotazování dat pomocí služby [Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use) jazyka, stejně jako export umožňuje [Power BI](https://powerbi.microsoft.com). 

1. Vyberte **protokolu (Analytics)** . Otevře se okno nové okno dotazu v horní části a okno tabulky dat pod ním. Pokud jste použili databází před, je toto uspořádání zkušenosti. Dotaz představuje váš předchozí filtrovaná data. **CustomDimensions** sloupec má robotů a LUIS informace.
1. Vyžádá si hlavní záměr, skóre a utterance, přidejte následující přímo nad poslední řádek ( `|top...` řádku) v okně dotazu:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_intent)
    | extend score = todouble(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_score)
    | extend utterance = tostring(customDimensions.LUIS_luisResponse_text)
    ```

1. Spusťte dotaz. Nové sloupce topIntent, skóre a utterance jsou k dispozici. Vyberte topIntent sloupec pro řazení.

Další informace o [Kusto dotazovací jazyk](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) nebo [export dat do Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>Další postup

Další informace, které chcete přidat do application insights data zahrnuje ID aplikace, ID verze, datum poslední změny modelu, datum posledního trénování, datum posledního publikování. Tyto hodnoty můžete buď být načtena z adresy URL koncového bodu (app ID a verzi) nebo pro vytváření volání rozhraní API pak v nastavení web app bot a získaná z něj.  

Pokud používáte stejné předplatné koncový bod pro více než jednu aplikaci LUIS, by měl také obsahovat ID předplatného a vlastnost s informacemi o tom, že se jedná o sdílený klíč. 

> [!div class="nextstepaction"]
> [Další informace o příklad projevy](luis-how-to-add-example-utterances.md)
