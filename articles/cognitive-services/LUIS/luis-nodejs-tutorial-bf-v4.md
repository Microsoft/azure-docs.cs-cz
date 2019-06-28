---
title: Language Understanding Bot Node.js v4
titleSuffix: Azure Cognitive Services
description: Pomocí Node.js vytvořte chatovacího robota integrovaného se službou Language Understanding (LUIS). Tento chatovací robot používá k rychlé implementaci řešení robota aplikaci Human Resources. K vytvoření robota se používá Bot Framework ve verzi 4 a Web App Bot Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: a06bd5a1a061de82230e93b867ea88e333b3cc93
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442554"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Kurz: Použijte Web App Bot povolené službou Language Understanding v Node.js 

Použití Node.js k vytvoření chatovací robot součástí jazyka (LUIS). Robot je vytvořené pomocí Azure [Web app bot](https://docs.microsoft.com/azure/bot-service/) prostředků a [Bot Framework verze](https://github.com/Microsoft/botbuilder-dotnet) V4.

**V tomto kurzu se naučíte:**

> [!div class="checklist"]
> * Vytvořit Web App Bota. Tento proces za vás vytvoří novou aplikaci LUIS.
> * Stáhněte si projekt bot vytvořena webová služba bot
> * Spustit robota a emulátor místně na počítači
> * Zobrazit výsledky promluv v robotovi

## <a name="prerequisites"></a>Požadavky

* [Emulátor robota](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-a-web-app-bot-resource"></a>Vytvořit prostředek použijete web app bot

1. Na portálu **Azure Portal** vyberte [Vytvořit nový prostředek](https://portal.azure.com).

1. Ve vyhledávací poli vyhledejte a vyberte **Web App Bot**. Vyberte **Vytvořit**.

1. V **Bot Service** (Služba robota) zadejte požadované informace:

    |Nastavení|Účel|Navrhované nastavení|
    |--|--|--|
    |Bot name (Název robota)|Název prostředku|`luis-nodejs-bot-` + `<your-name>`, například `luis-nodejs-bot-johnsmith`|
    |Předplatné|Předplatné, pod kterým se má robot vytvořit|Vaše primární předplatné
    |Skupina prostředků|Logická skupina prostředků Azure|Vytvořte novou skupinu k uložení všech prostředků používaných s tímto robotem a skupinu pojmenujte `luis-nodejs-bot-resource-group`.|
    |Umístění|Oblast Azure – nemusí být stejná jako oblast LUIS pro vytváření nebo publikování.|`westus`|
    |Cenová úroveň|Slouží pro limity žádostí o služby a fakturaci.|`F0` je bezplatná úroveň.
    |App name (Název aplikace)|Název se používá jako subdoména, když je váš robot nasazený do cloudu (například humanresourcesbot.azurewebsites.net).|`luis-nodejs-bot-` + `<your-name>`, například `luis-nodejs-bot-johnsmith`|
    |Bot template (Šablona robota)|Nastavení Bot Frameworku – viz následující tabulka|
    |LUIS App location (Umístění aplikace LUIS)|Musí být stejné jako oblast prostředků LUIS|`westus`|
    |Plán App service/umístění|Neměňte z Zadaná výchozí hodnota.|
    |Application Insights|Neměňte z Zadaná výchozí hodnota.|
    |Microsoft App ID a heslo|Neměňte z Zadaná výchozí hodnota.|

1. V **Bot šablony**, vyberte následující a potom klepněte **vyberte** tlačítko v těchto nastavení:

    |Nastavení|Účel|Výběr|
    |--|--|--|
    |SDK version (Verze sady SDK)|Bot framework version (Verze Bot Frameworku)|**SDK v4**|
    |SDK language (Jazyk sady SDK)|Programovací jazyk robota|**Node.js**|
    |Bot|Typ robota|**Basic Bot** (Základní robot)|
    
1. Vyberte **Vytvořit**. Tím se vytvoří a nasadí služba robota do Azure. Část tohoto procesu vytvoří aplikaci LUIS nazvanou `luis-nodejs-bot-XXXX`. Tento název je založen na názvu aplikace /Azure Bot Service.

    [![Vytvoření webové aplikace robota](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    Počkejte, dokud bot service je vytvořen před pokračováním.

## <a name="the-bot-has-a-language-understanding-model"></a>Robot má model Language Understanding

Proces vytvoření bot service vytvoří také novou aplikaci LUIS pomocí tříd Intent a projevy příklad. Robot poskytuje mapování záměrů na novou aplikaci LUIS pro následující záměry: 

|Záměry základního robota LUIS|Příklad promluvy|
|--|--|
|Kniha let|`Travel to Paris`|
|Zrušit|`bye`|
|Žádný|Cokoli mimo doménu aplikace|

## <a name="test-the-bot-in-web-chat"></a>Testování ve Web Chat robota

1. Zatímco jste pořád na webu Azure Portal pro novým robotem, vyberte **testování ve Web Chat**. 
1. V **napište zprávu** textového pole zadejte text `hello`. Informace o rozhraní bot framework, jakož i Příklady dotazů pro konkrétní model LUIS, jako je například rezervace letenky na Paříž jako odpověď vrátí robota. 

    ![Snímek obrazovky webu Azure portal, zadejte text "hello".](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    Funkce testu můžete použít pro rychlé testování svého robota. Další informace dokončení testování, včetně ladění, stažení kódu robotů a pomocí sady Visual Studio. 

## <a name="download-the-web-app-bot-source-code"></a>Stáhněte si zdrojový kód web app bot
Pokud chcete vyvíjet kód Web App Bota, stáhněte si kód a použijte ho ve svém místním počítači. 

1. Na portálu Azure Portal vyberte **Build** (Sestavení) v části **Bot management** (Správa robota). 

1. Vyberte **Download Bot source code** (Stáhnout zdrojový kód robota). 

    [![Stáhněte si web app bot zdrojový kód pro základního robota](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Při zobrazení dialogu požádá **obsahují nastavení aplikace v souboru zip staženého?** vyberte **Ano**.

1. Když se zdrojový kód zazipuje, zobrazí se zpráva s odkazem na stažení kódu. Vyberte odkaz. 

1. Uložte soubor zip do místního počítače a extrahujte soubory. Otevřete projekt pomocí sady Visual Studio. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Revize kódu utterance posílala LUIS a získat odpovědi

1. Otevřít **dialogová okna -> luisHelper.js** souboru. Tady se uživatelská promluva zadaná do robota odešle do služby LUIS. Odpověď služby luis se vrátí z metody jako **bookDetails** objekt JSON. Když vytvoříte vlastní robota, by měl taky vytvořit vlastní objekt vrátit podrobnosti služby luis. 

    ```nodejs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { LuisRecognizer } = require('botbuilder-ai');
    
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

1. Otevřít **dialogová okna -> bookingDialog.js** pochopit, jak objekt BookingDetails slouží ke správě toku konverzace. Podrobnosti cesty se zobrazí výzva, v krocích, pak celý rezervace je potvrzen a nakonec opakován zpět tak, aby uživatel. 

    ```nodejs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { TimexProperty } = require('@microsoft/recognizers-text-data-types-timex-expression');
    const { ConfirmPrompt, TextPrompt, WaterfallDialog } = require('botbuilder-dialogs');
    const { CancelAndHelpDialog } = require('./cancelAndHelpDialog');
    const { DateResolverDialog } = require('./dateResolverDialog');
    
    const CONFIRM_PROMPT = 'confirmPrompt';
    const DATE_RESOLVER_DIALOG = 'dateResolverDialog';
    const TEXT_PROMPT = 'textPrompt';
    const WATERFALL_DIALOG = 'waterfallDialog';
    
    class BookingDialog extends CancelAndHelpDialog {
        constructor(id) {
            super(id || 'bookingDialog');
    
            this.addDialog(new TextPrompt(TEXT_PROMPT))
                .addDialog(new ConfirmPrompt(CONFIRM_PROMPT))
                .addDialog(new DateResolverDialog(DATE_RESOLVER_DIALOG))
                .addDialog(new WaterfallDialog(WATERFALL_DIALOG, [
                    this.destinationStep.bind(this),
                    this.originStep.bind(this),
                    this.travelDateStep.bind(this),
                    this.confirmStep.bind(this),
                    this.finalStep.bind(this)
                ]));
    
            this.initialDialogId = WATERFALL_DIALOG;
        }
    
        /**
         * If a destination city has not been provided, prompt for one.
         */
        async destinationStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            if (!bookingDetails.destination) {
                return await stepContext.prompt(TEXT_PROMPT, { prompt: 'To what city would you like to travel?' });
            } else {
                return await stepContext.next(bookingDetails.destination);
            }
        }
    
        /**
         * If an origin city has not been provided, prompt for one.
         */
        async originStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the response to the previous step's prompt
            bookingDetails.destination = stepContext.result;
            if (!bookingDetails.origin) {
                return await stepContext.prompt(TEXT_PROMPT, { prompt: 'From what city will you be travelling?' });
            } else {
                return await stepContext.next(bookingDetails.origin);
            }
        }
    
        /**
         * If a travel date has not been provided, prompt for one.
         * This will use the DATE_RESOLVER_DIALOG.
         */
        async travelDateStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the results of the previous step
            bookingDetails.origin = stepContext.result;
            if (!bookingDetails.travelDate || this.isAmbiguous(bookingDetails.travelDate)) {
                return await stepContext.beginDialog(DATE_RESOLVER_DIALOG, { date: bookingDetails.travelDate });
            } else {
                return await stepContext.next(bookingDetails.travelDate);
            }
        }
    
        /**
         * Confirm the information the user has provided.
         */
        async confirmStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the results of the previous step
            bookingDetails.travelDate = stepContext.result;
            const msg = `Please confirm, I have you traveling to: ${ bookingDetails.destination } from: ${ bookingDetails.origin } on: ${ bookingDetails.travelDate }.`;
    
            // Offer a YES/NO prompt.
            return await stepContext.prompt(CONFIRM_PROMPT, { prompt: msg });
        }
    
        /**
         * Complete the interaction and end the dialog.
         */
        async finalStep(stepContext) {
            if (stepContext.result === true) {
                const bookingDetails = stepContext.options;
    
                return await stepContext.endDialog(bookingDetails);
            } else {
                return await stepContext.endDialog();
            }
        }
    
        isAmbiguous(timex) {
            const timexPropery = new TimexProperty(timex);
            return !timexPropery.types.has('definite');
        }
    }
    
    module.exports.BookingDialog = BookingDialog;
    ```


## <a name="install-dependencies-and-start-the-bot-code-in-visual-studio"></a>Instalace závislostí a spuštění kódu robotů v sadě Visual Studio

1. Ve VSCode, z integrovaný terminál nainstalujte závislosti pomocí příkazu `npm install`.
1. Také integrovaného terminálu spusťte pomocí příkazu robota `npm start`. 


## <a name="use-the-bot-emulator-to-test-the-bot"></a>Použití emulátoru robota k otestování robota

1. Začněte Bot emulátoru a vyberte **otevřít Bot**.
1. V **otevřete robota** vyskakovací dialogové okno, zadejte adresu URL vašeho robota, jako například `http://localhost:3978/api/messages`. `/api/messages` Trasy je webová adresa pro robota.
1. Zadejte **ID aplikace Microsoft** a **heslo Microsoft App**byl nalezen v **.env** soubor v kořenové složce kódu bot jste si stáhli.

    Volitelně můžete vytvořit novým robotem, konfigurace a zkopírujte `MicrosoftAppId` a `MicrosoftAppPassword` z **.env** soubor v projektu sady Visual Studio pro robota. Název konfiguračního souboru robota by měl být stejný jako název robota. 

    ```json
    {
        "name": "<bot name>",
        "description": "<bot description>",
        "services": [
            {
                "type": "endpoint",
                "appId": "<appId from .env>",
                "appPassword": "<appPassword from .env>",
                "endpoint": "http://localhost:3978/api/messages",
                "id": "<don't change this value>",
                "name": "http://localhost:3978/api/messages"
            }
        ],
        "padlock": "",
        "version": "2.0",
        "overrides": null,
        "path": "<local path to .bot file>"
    }
    ```

1. V emulátoru robota, zadejte `Hello` dostanete stejnou odpověď pro základního robota, jako jste obdrželi **testování ve Web Chat**.

    [![Odpověď základního robota v emulátoru](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>Zeptejte se bot pro knihy letu záměr

1. V emulátoru bot rezervovat tak, že zadáte následující utterance let: 

    ```bot
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    Emulátor bot požádá o potvrzení. 

1. Vyberte **Ano**. Robot jako odpověď vrátí souhrn jeho akce. 
1. Z protokolu bot emulátor, vyberte řádek, který zahrnuje `Luis Trace`. Tím se zobrazí odpověď JSON na záměr a entity utterance služby luis.

    [![Odpověď základního robota v emulátoru](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Další postup

Další informace naleznete v [ukázky](https://github.com/microsoft/botframework-solutions) s konverzační robotů. 

> [!div class="nextstepaction"]
> [Vytvoření aplikace Language Understanding s doménou vlastního subjektu](luis-quickstart-intents-only.md)