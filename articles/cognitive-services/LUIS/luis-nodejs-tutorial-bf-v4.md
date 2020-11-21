---
title: 'Kurz: Language Understanding robot Node.js v4'
description: Pomocí Node.js sestavíte v tomto kurzu v tomto kurzu integraci robota v rámci jazyka porozumění (LUIS). Tento chatovací robot používá k rychlé implementaci řešení robota aplikaci Human Resources. K vytvoření robota se používá Bot Framework ve verzi 4 a Web App Bot Azure.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: d070a03a81f70cdbf2c721cc67a3bc40c5d731fc
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018731"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Kurz: použití robota webové aplikace s povolenou službou Language Understanding v Node.js

Pomocí Node.js můžete vytvořit chatovací robot integrovaný s jazykem porozumění (LUIS). Robot má vytvořenou službu Azure [Web App bot](/azure/bot-service/) Resource a [bot Framework verze](https://github.com/Microsoft/botbuilder-dotnet) v4.

**V tomto kurzu se naučíte:**

> [!div class="checklist"]
> * Vytvořit Web App Bota. Tento proces za vás vytvoří novou aplikaci LUIS.
> * Stažení projektu robota vytvořeného službou Web bot Service
> * Spustit robota a emulátor místně na počítači
> * Zobrazit výsledky promluv v robotovi

## <a name="prerequisites"></a>Požadavky

* [Emulátor pro robot Framework](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

## <a name="create-a-web-app-bot-resource"></a>Vytvoření prostředku robota webové aplikace

1. Na portálu **Azure Portal** vyberte [Vytvořit nový prostředek](https://portal.azure.com).

1. Ve vyhledávací poli vyhledejte a vyberte **Web App Bot**. Vyberte **Vytvořit**.

1. V **Bot Service** (Služba robota) zadejte požadované informace:

    |Nastavení|Účel|Navrhované nastavení|
    |--|--|--|
    |Popisovač robota|Název prostředku|`luis-nodejs-bot-` + `<your-name>`, například `luis-nodejs-bot-johnsmith`|
    |Předplatné|Předplatné, pod kterým se má robot vytvořit|Vaše primární předplatné
    |Skupina prostředků|Logická skupina prostředků Azure|Vytvořte novou skupinu k uložení všech prostředků používaných s tímto robotem a skupinu pojmenujte `luis-nodejs-bot-resource-group`.|
    |Umístění|Oblast Azure – nemusí být stejná jako oblast LUIS pro vytváření nebo publikování.|`westus`|
    |Cenová úroveň|Slouží pro limity žádostí o služby a fakturaci.|`F0` je bezplatná úroveň.
    |Název aplikace|Název se používá jako subdoména, když je váš robot nasazený do cloudu (například humanresourcesbot.azurewebsites.net).|`luis-nodejs-bot-` + `<your-name>`, například `luis-nodejs-bot-johnsmith`|
    |Bot template (Šablona robota)|Nastavení Bot Frameworku – viz následující tabulka|
    |LUIS App location (Umístění aplikace LUIS)|Musí být stejné jako oblast prostředků LUIS|`westus`|
    |Plán/umístění služby App Service|Neměňte z poskytnuté výchozí hodnoty.|
    |Application Insights|Neměňte z poskytnuté výchozí hodnoty.|
    |ID a heslo aplikace od Microsoftu|Neměňte z poskytnuté výchozí hodnoty.|

1. V **šabloně robota** vyberte následující a pak klikněte na tlačítko **Vybrat** pod těmito nastaveními:

    |Nastavení|Účel|Výběr|
    |--|--|--|
    |SDK language (Jazyk sady SDK)|Programovací jazyk robota|**Node.js**|
    |Dole|Typ robota|**Basic Bot** (Základní robot)|

1. Vyberte **Vytvořit**. Tím se vytvoří a nasadí služba robota do Azure. Část tohoto procesu vytvoří aplikaci LUIS nazvanou `luis-nodejs-bot-XXXX`. Tento název vychází z názvu aplikace služby/Azure bot.

    > [!div class="mx-imgBorder"]
    > [![Vytvoření Web App Bota](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    Než budete pokračovat, počkejte, než se vytvoří služba robot.

1. Vyberte `Go to resource` v oznámení, abyste přešli na stránku robota vaší webové aplikace.

## <a name="the-bot-has-a-language-understanding-model"></a>Robot má Language Understanding model

Proces vytvoření služby robot také vytvoří novou aplikaci LUIS s záměry a příkladem projevy. Robot poskytuje mapování záměrů na novou aplikaci LUIS pro následující záměry:

|Záměry základního robota LUIS|Příklad promluvy|
|--|--|
|Kniha let|`Travel to Paris`|
|Zrušit|`bye`|
|Getpočasí|`what's the weather like?`|
|Žádné|Cokoli mimo doménu aplikace|

## <a name="test-the-bot-in-web-chat"></a>Testování robota ve webovém chatu

1. I když jste stále v Azure Portal pro nový robot, vyberte **test na webu chat**.
1. Do textového pole **text zprávy** zadejte text `Book a flight from Seattle to Berlin tomorrow` . Robot odpoví s ověřením, že chcete zarezervovat let.

    ![Snímek obrazovky Azure Portal zadejte text Hello.](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    Pomocí funkce test můžete rychle otestovat robot. Pro úplnější testování, včetně ladění, Stáhněte si kód robota a použijte Visual Studio Code.

## <a name="download-the-web-app-bot-source-code"></a>Stažení zdrojového kódu robota webové aplikace
Pokud chcete vyvíjet kód Web App Bota, stáhněte si kód a použijte ho ve svém místním počítači.

1. Na portálu Azure Portal vyberte **Build** (Sestavení) v části **Bot management** (Správa robota).

1. Vyberte **Download Bot source code** (Stáhnout zdrojový kód robota).

    [![Stažení zdrojového kódu robota webové aplikace pro základní bot](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Když se automaticky otevře okno s dotazem **zahrnout nastavení aplikace ve staženém souboru ZIP?** vyberte **Ano**. To poskytuje LUIS nastavení.

1. Když se zdrojový kód zazipuje, zobrazí se zpráva s odkazem na stažení kódu. Vyberte odkaz.

1. Uložte soubor zip do místního počítače a extrahujte soubory. Otevřete složku projektu pomocí Visual Studio Code.

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Přečtěte si kód, který odešle utterance do LUIS a získá odpověď.

1. Chcete-li odeslat uživatele utterance do koncového bodu předpovědi LUIS, otevřete **dialogová okna > flightBookingRecognizer.js** . Tady se uživatelská promluva zadaná do robota odešle do služby LUIS. Odpověď z LUIS je vrácena z metody **executeLuisQuery** .

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { LuisRecognizer } = require('botbuilder-ai');

    class FlightBookingRecognizer {
        constructor(config) {
            const luisIsConfigured = config && config.applicationId && config.endpointKey && config.endpoint;
            if (luisIsConfigured) {
                // Set the recognizer options depending on which endpoint version you want to use e.g v2 or v3.
                // More details can be found in https://docs.microsoft.com/en-gb/azure/cognitive-services/luis/luis-migration-api-v3
                const recognizerOptions = {
                    apiVersion: 'v3'
                };

                this.recognizer = new LuisRecognizer(config, recognizerOptions);
            }
        }

        get isConfigured() {
            return (this.recognizer !== undefined);
        }

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        getFromEntities(result) {
            let fromValue, fromAirportValue;
            if (result.entities.$instance.From) {
                fromValue = result.entities.$instance.From[0].text;
            }
            if (fromValue && result.entities.From[0].Airport) {
                fromAirportValue = result.entities.From[0].Airport[0][0];
            }

            return { from: fromValue, airport: fromAirportValue };
        }

        getToEntities(result) {
            let toValue, toAirportValue;
            if (result.entities.$instance.To) {
                toValue = result.entities.$instance.To[0].text;
            }
            if (toValue && result.entities.To[0].Airport) {
                toAirportValue = result.entities.To[0].Airport[0][0];
            }

            return { to: toValue, airport: toAirportValue };
        }

        /**
         * This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
         * TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
         */
        getTravelDate(result) {
            const datetimeEntity = result.entities.datetime;
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;

            const timex = datetimeEntity[0].timex;
            if (!timex || !timex[0]) return undefined;

            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }

    module.exports.FlightBookingRecognizer = FlightBookingRecognizer;
    ```

1. **Dialogy-> mainDialog** zachycuje utterance a pošle je do executeLuisQuery v metodě actStep.

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { TimexProperty } = require('@microsoft/recognizers-text-data-types-timex-expression');
    const { MessageFactory, InputHints } = require('botbuilder');
    const { LuisRecognizer } = require('botbuilder-ai');
    const { ComponentDialog, DialogSet, DialogTurnStatus, TextPrompt, WaterfallDialog } = require('botbuilder-dialogs');

    const MAIN_WATERFALL_DIALOG = 'mainWaterfallDialog';

    class MainDialog extends ComponentDialog {
        constructor(luisRecognizer, bookingDialog) {
            super('MainDialog');

            if (!luisRecognizer) throw new Error('[MainDialog]: Missing parameter \'luisRecognizer\' is required');
            this.luisRecognizer = luisRecognizer;

            if (!bookingDialog) throw new Error('[MainDialog]: Missing parameter \'bookingDialog\' is required');

            // Define the main dialog and its related components.
            // This is a sample "book a flight" dialog.
            this.addDialog(new TextPrompt('TextPrompt'))
                .addDialog(bookingDialog)
                .addDialog(new WaterfallDialog(MAIN_WATERFALL_DIALOG, [
                    this.introStep.bind(this),
                    this.actStep.bind(this),
                    this.finalStep.bind(this)
                ]));

            this.initialDialogId = MAIN_WATERFALL_DIALOG;
        }

        /**
         * The run method handles the incoming activity (in the form of a TurnContext) and passes it through the dialog system.
         * If no dialog is active, it will start the default dialog.
         * @param {*} turnContext
         * @param {*} accessor
         */
        async run(turnContext, accessor) {
            const dialogSet = new DialogSet(accessor);
            dialogSet.add(this);

            const dialogContext = await dialogSet.createContext(turnContext);
            const results = await dialogContext.continueDialog();
            if (results.status === DialogTurnStatus.empty) {
                await dialogContext.beginDialog(this.id);
            }
        }

        /**
         * First step in the waterfall dialog. Prompts the user for a command.
         * Currently, this expects a booking request, like "book me a flight from Paris to Berlin on march 22"
         * Note that the sample LUIS model will only recognize Paris, Berlin, New York and London as airport cities.
         */
        async introStep(stepContext) {
            if (!this.luisRecognizer.isConfigured) {
                const messageText = 'NOTE: LUIS is not configured. To enable all capabilities, add `LuisAppId`, `LuisAPIKey` and `LuisAPIHostName` to the .env file.';
                await stepContext.context.sendActivity(messageText, null, InputHints.IgnoringInput);
                return await stepContext.next();
            }

            const messageText = stepContext.options.restartMsg ? stepContext.options.restartMsg : 'What can I help you with today?\nSay something like "Book a flight from Paris to Berlin on March 22, 2020"';
            const promptMessage = MessageFactory.text(messageText, messageText, InputHints.ExpectingInput);
            return await stepContext.prompt('TextPrompt', { prompt: promptMessage });
        }

        /**
         * Second step in the waterfall.  This will use LUIS to attempt to extract the origin, destination and travel dates.
         * Then, it hands off to the bookingDialog child dialog to collect any remaining details.
         */
        async actStep(stepContext) {
            const bookingDetails = {};

            if (!this.luisRecognizer.isConfigured) {
                // LUIS is not configured, we just run the BookingDialog path.
                return await stepContext.beginDialog('bookingDialog', bookingDetails);
            }

            // Call LUIS and gather any potential booking details. (Note the TurnContext has the response to the prompt)
            const luisResult = await this.luisRecognizer.executeLuisQuery(stepContext.context);
            switch (LuisRecognizer.topIntent(luisResult)) {
            case 'BookFlight': {
                // Extract the values for the composite entities from the LUIS result.
                const fromEntities = this.luisRecognizer.getFromEntities(luisResult);
                const toEntities = this.luisRecognizer.getToEntities(luisResult);

                // Show a warning for Origin and Destination if we can't resolve them.
                await this.showWarningForUnsupportedCities(stepContext.context, fromEntities, toEntities);

                // Initialize BookingDetails with any entities we may have found in the response.
                bookingDetails.destination = toEntities.airport;
                bookingDetails.origin = fromEntities.airport;
                bookingDetails.travelDate = this.luisRecognizer.getTravelDate(luisResult);
                console.log('LUIS extracted these booking details:', JSON.stringify(bookingDetails));

                // Run the BookingDialog passing in whatever details we have from the LUIS call, it will fill out the remainder.
                return await stepContext.beginDialog('bookingDialog', bookingDetails);
            }

            case 'GetWeather': {
                // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                const getWeatherMessageText = 'TODO: get weather flow here';
                await stepContext.context.sendActivity(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                break;
            }

            default: {
                // Catch all for unhandled intents
                const didntUnderstandMessageText = `Sorry, I didn't get that. Please try asking in a different way (intent was ${ LuisRecognizer.topIntent(luisResult) })`;
                await stepContext.context.sendActivity(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
            }
            }

            return await stepContext.next();
        }

        /**
         * Shows a warning if the requested From or To cities are recognized as entities but they are not in the Airport entity list.
         * In some cases LUIS will recognize the From and To composite entities as a valid cities but the From and To Airport values
         * will be empty if those entity values can't be mapped to a canonical item in the Airport.
         */
        async showWarningForUnsupportedCities(context, fromEntities, toEntities) {
            const unsupportedCities = [];
            if (fromEntities.from && !fromEntities.airport) {
                unsupportedCities.push(fromEntities.from);
            }

            if (toEntities.to && !toEntities.airport) {
                unsupportedCities.push(toEntities.to);
            }

            if (unsupportedCities.length) {
                const messageText = `Sorry but the following airports are not supported: ${ unsupportedCities.join(', ') }`;
                await context.sendActivity(messageText, messageText, InputHints.IgnoringInput);
            }
        }

        /**
         * This is the final step in the main waterfall dialog.
         * It wraps up the sample "book a flight" interaction with a simple confirmation.
         */
        async finalStep(stepContext) {
            // If the child dialog ("bookingDialog") was cancelled or the user failed to confirm, the Result here will be null.
            if (stepContext.result) {
                const result = stepContext.result;
                // Now we have all the booking details.

                // This is where calls to the booking AOU service or database would go.

                // If the call to the booking service was successful tell the user.
                const timeProperty = new TimexProperty(result.travelDate);
                const travelDateMsg = timeProperty.toNaturalLanguage(new Date(Date.now()));
                const msg = `I have you booked to ${ result.destination } from ${ result.origin } on ${ travelDateMsg }.`;
                await stepContext.context.sendActivity(msg, msg, InputHints.IgnoringInput);
            }

            // Restart the main dialog with a different message the second time around
            return await stepContext.replaceDialog(this.initialDialogId, { restartMsg: 'What else can I do for you?' });
        }
    }

    module.exports.MainDialog = MainDialog;
    ```

<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="start-the-bot-code"></a>Spuštění kódu robota

1. Otevřete konzolu Windows nebo terminál pro Linux nebo macOS.

1. Přejděte do adresáře s kódem bot a zadejte následující příkaz pro instalaci závislosti dotenv:

    ```console
    npm install dotenv --save
    ```

1. Spusťte robot zadáním následujícího příkazu:

    ```console
    node index.js
    ```

Otevře se okno prohlížeče s webem Web App Bota na adrese `http://localhost:3978/`. Zobrazí se Domovská stránka s informacemi o robotovi.

![Zobrazí se Domovská stránka s informacemi o robotovi.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-framework-emulator-to-test-the-bot"></a>Použití emulátoru bot Framework k otestování robota

Zeptejte se robota na záměr na knihu letu.

1. Spusťte emulátor rozhraní bot Framework a vyberte **otevřít robot**.
1. V automaticky **otevřeném okně robota** zadejte adresu URL robota, například `http://localhost:3978/api/messages` . `/api/messages`Trasa je webová adresa pro robota.
1. Zadejte **ID aplikace Microsoftu** a **heslo aplikace Microsoftu**, které najdete v souboru **. env** v kořenovém adresáři kódu robota, který jste stáhli.

1. V emulátoru bot Framework zadejte `Book a flight from Seattle to Berlin tomorrow` a získejte stejnou reakci na základní bot, jak jste dostali v **testu ve webovém chatu**.

    [![Snímek obrazovky ukazuje emulátor rozhraní bot Framework.](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Vyberte **Ano**. Robot odpoví souhrnem jeho akcí.
1. Z protokolu emulátoru bot Framework vyberte řádek, který obsahuje `<- trace LuisV3 Trace` . Tím se zobrazí odpověď JSON z LUIS pro záměr a entity utterance.

    [![Snímek obrazovky s panelem na pravé straně, který obsahuje kód JSON, zobrazuje emulátor rozhraní bot Framework.](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Další kroky

Podívejte se na další [ukázky](https://github.com/microsoft/botframework-solutions) s konverzací roboty.

> [!div class="nextstepaction"]
> [Vytvoření aplikace Language Understanding s vlastní doménou předmětu](./tutorial-intents-only.md)