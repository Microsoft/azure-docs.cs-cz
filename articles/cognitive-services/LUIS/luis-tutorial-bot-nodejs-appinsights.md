---
title: Application Insights, Node. js – LUIS
titleSuffix: Azure Cognitive Services
description: V tomto kurzu se pro Application Insights úložiště dat telemetrie přidají informace o robotech a Language Understanding.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/16/2019
ms.author: diberry
ms.openlocfilehash: 72ce681b6b0e4109151b987a5f8cc4bc050aafa0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563298"
---
# <a name="add-luis-results-to-application-insights-from-a-bot-in-nodejs"></a>Přidání výsledků LUIS do Application Insights z robota v Node. js
V tomto kurzu se pro [Application Insights](https://azure.microsoft.com/services/application-insights/) úložiště dat telemetrie přidají informace o robotech a Language Understanding. Jakmile tato data máte, můžete je pomocí jazyka Kusto nebo Power BI analyzovat, agregovat a sestavovat v reálném čase a entity utterance. Tato analýza pomůže zároveň pomáhá určit, pokud by měl přidat nebo upravit záměry a entity aplikace LUIS.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zachytávání dat o robotech a jazycích v Application Insights
> * Dotaz na Application Insights pro Language Understanding data

## <a name="prerequisites"></a>Požadavky

* Robot služby Azure bot Service se vytvořil s povoleným Application Insights.
* Stáhli jsme kód robota z předchozího **[kurzu](luis-nodejs-tutorial-bf-v4.md)** bot. 
* [Emulátor robota](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

Veškerý kód v tomto kurzu je k dispozici v [úložišti Azure-samples Language Understanding GitHubu](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-nodejs-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Přidat Application Insights do projektu bot webové aplikace
V současné době služba Application Insights, použít v tomto použijete web app bot shromažďuje telemetrii celkový stav pro robota. Neshromažďuje informace o LUIS. 

Aby bylo možné zachytit informace LUIS, robot webové aplikace musí mít nainstalovaný a nakonfigurovaný balíček npm pro **[Application Insights](https://www.npmjs.com/package/applicationinsights)** .  

1. V VSCode Integrated Terminal v kořenovém adresáři pro projekt bot přidejte následující balíčky NPM pomocí zobrazeného příkazu: 

    ```console
    npm install applicationinsights && npm install underscore
    ```
    
    Balíček **podtržítka** se používá k sloučení struktury Luis JSON, aby bylo snazší zobrazit a používat v Application Insights.
    


## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Zaznamenávat a odesílat LUIS výsledků dotazu do Application Insights

1. V VSCode vytvořte nový soubor **appInsightsLog. js** a přidejte následující kód:

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

    Tento soubor Získá kontext robota a odpověď Luis, sloučí oba objekty a vloží je do události **trasování** v Application Insights. Název události je **Luis**. 

1. Otevřete složku **dialogy** a pak soubor **luisHelper. js** . Zahrňte nový **appInsightsLog. js** jako požadovaný soubor a zachyťte kontext robota a Luis odpověď. Úplný kód tohoto souboru je: 

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

## <a name="add-application-insights-instrumentation-key"></a>Přidat Application Insights klíč instrumentace 

Aby bylo možné přidat data do Application Insights, budete potřebovat klíč instrumentace.

1. V prohlížeči v [Azure Portal](https://portal.azure.com)najděte prostředek **Application Insights** robota. Jeho název bude mít většinu názvu bot, potom náhodně znaky na konci názvu, například `luis-nodejs-bot-johnsmithxqowom`. 
1. Na Application Insights prostředku na stránce **Přehled** zkopírujte **klíč instrumentace**.
1. V VSCode otevřete soubor **. env** v kořenu projektu bot. Tento soubor obsahuje všechny proměnné prostředí.  
1. Přidejte novou proměnnou `MicrosoftApplicationInsightsInstrumentationKey` s hodnotou klíče instrumentace. Nevkládat hodnotu do uvozovek. 

## <a name="start-the-bot"></a>Spuštění robota

1. Z integrovaného terminálu VSCode spusťte robot:
    
    ```console
    npm start
    ```

1. Spusťte emulátor bot a otevřete robota. Tento [Krok](luis-nodejs-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) je k dispozici v předchozím kurzu.

1. Zeptejte se robota na otázku. Tento [Krok](luis-nodejs-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent) je k dispozici v předchozím kurzu.

## <a name="view-luis-entries-in-application-insights"></a>Zobrazení LUIS položky ve službě Application Insights

Otevřete službu Application Insights zobrazíte položky LUIS. Může trvat několik minut, než se data zobrazí v Application Insights.

1. V [Azure Portal](https://portal.azure.com)otevřete prostředek Application Insights robota. 
1. Po otevření prostředku vyberte **Hledat** a vyhledejte všechna data za posledních **30 minut** pomocí typu události **Trace**. Vyberte trasování s názvem **Luis**. 
1. Informace robota a LUIS jsou k dispozici v části **vlastní vlastnosti**. 

    ![Kontrola vlastních vlastností LUIS uložených v Application Insights](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-nodejs.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Dotaz Application Insights pro záměr, skóre a utterance
Application Insights vám dává oprávnění k dotazování dat pomocí jazyka [Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use) a také k jejich exportu do [Power BI](https://powerbi.microsoft.com). 

1. Vyberte **protokol (Analytics)** . Otevře se okno nové okno dotazu v horní části a okno tabulky dat pod ním. Pokud jste použili databází před, je toto uspořádání zkušenosti. Dotaz představuje vaše předchozí filtrovaná data. Ve sloupci **CustomDimensions** jsou informace robot a Luis.
1. Pokud chcete načíst nejvyšší záměr, skóre a utterance, přidejte následující těsně nad poslední řádek ( `|top...` řádek) v okně dotazu:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_intent)
    | extend score = todouble(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_score)
    | extend utterance = tostring(customDimensions.LUIS_luisResponse_text)
    ```

1. Spusťte dotaz. Nové sloupce topIntent, skóre a utterance jsou k dispozici. Vyberte sloupec topIntent, který chcete seřadit.

Přečtěte si další informace o [dotazovacím jazyku Kusto](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) nebo [exportujte data do Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>Další postup

Další informace, které chcete přidat do application insights data zahrnuje ID aplikace, ID verze, datum poslední změny modelu, datum posledního trénování, datum posledního publikování. Tyto hodnoty můžete načíst buď z adresy URL koncového bodu (ID aplikace a ID verze), nebo z volání rozhraní API pro vytváření obsahu, které pak nastavíte v nastavení robota webové aplikace a odtud z něj načtete.  

Pokud používáte stejné předplatné koncový bod pro více než jednu aplikaci LUIS, by měl také obsahovat ID předplatného a vlastnost s informacemi o tom, že se jedná o sdílený klíč. 

> [!div class="nextstepaction"]
> [Další informace o příklad projevy](luis-how-to-add-example-utterances.md)
