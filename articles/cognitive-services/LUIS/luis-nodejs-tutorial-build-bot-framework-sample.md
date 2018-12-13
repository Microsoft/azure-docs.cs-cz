---
title: Služba LUIS robotů s využitím Node.js
titleSuffix: Azure Cognitive Services
description: Vytvořte robota, integrovaná aplikace LUIS pomocí rozhraní Bot Framework.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 7229155b9fbfb93babd45c746d0f36fbab812013
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53093399"
---
# <a name="luis-bot-in-nodejs-with-the-bot-framework-3x-and-the-azure-web-app-bot"></a>Služba LUIS robotů v Node.js s použitím rozhraní Bot Framework 3.x a použijete Azure Web app bot

Pomocí Node.js vytvořte chatovacího robota integrovaného se službou Language Understanding (LUIS). Tuto chatovací robot používá předem připravených HomeAutomation doménu k rychlé implementaci řešení robota. Robot využívá rozhraní Bot Framework 3.x a použijete Azure Web app bot.

## <a name="prerequisite"></a>Požadavek

Než začnete vytvářet robota, postupujte podle kroků v [vytvořit aplikaci](./luis-get-started-create-app.md) k sestavení aplikace LUIS, který používá.

Robot jsou reaguje na záměry od HomeAutomation domény, které jsou v aplikaci LUIS. Pro každou z těchto záměry aplikace LUIS poskytuje záměru, který se mapuje na ni. Robot poskytuje dialogového okna, která zpracovává záměru, který zjistí LUIS.

| Záměr | Ukázková promluva | Funkce robota |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | Vypnul světla. | Vyvolá robota `TurnOnDialog` při `HomeAutomation.TurnOn` se detekuje. Toto dialogové okno je, kde by vyvolat služby IoT můžete zapnout v zařízení a informace pro uživatele, že zařízení je zapnutý. |
| HomeAutomation.TurnOff | Vypněte ložnici světla. | Vyvolá robota `TurnOffDialog` při `HomeAutomation.TurnOff` se detekuje. Toto dialogové okno, kde by vyvolat služby IoT, vypněte zařízení a informace pro uživatele, která zařízení jsou vypnuté. |


## <a name="create-a-language-understanding-bot-with-bot-service"></a>Vytváření robotů Language Understanding s využitím služby Bot

1. V [webu Azure portal](https://portal.azure.com)vyberte **vytvořit nový prostředek** v okně s nabídkou a vyberte **zobrazit všechny**.

    ![Vytvořit nový prostředek](./media/luis-tutorial-node-bot/bot-service-creation.png)

2. Do vyhledávacího pole vyhledejte **Web App Bot**. 

    ![Vytvořit nový prostředek](./media/luis-tutorial-node-bot/bot-service-selection.png)

3. V **Bot Service** okno, zadejte požadované informace a vyberte **vytvořit**. Tím se vytvoří a nasadí bot service a LUIS aplikace do Azure. Pokud chcete použít [řeči dočištění](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming), zkontrolujte [požadavků oblasti](luis-resources-faq.md#what-luis-regions-support-bot-framework-speech-priming) před vytvořením svého robota. 
    * Nastavte **název aplikace** na název svého robota. Název se používá jako subdoménu svého robota nasazené do cloudu (například mynotesbot.azurewebsites.net). <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
    * Vyberte předplatné, [skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), plán služby App service, a [umístění](https://azure.microsoft.com/regions/).
    * Pro **Bot šablony**vyberte:
        * **Sada SDK v3**
        * **Node.js**
        * **Rozpoznávání jazyka**
    * Vyberte **umístění aplikace LUIS**. Toto je vytváření [oblasti] [ LUIS] vytvoření aplikace v.
    * Zaškrtněte políčko potvrzení právních upozornění. Podmínky právních upozornění jsou níže na zaškrtávací políčko.

    ![Okno bot Service](./media/luis-tutorial-node-bot/bot-service-setting-callout-template.png)


4. Potvrďte, že byla nasazena bot service.
    * Vyberte oznámení (ikona zvonku umístěné podél horního okraje na webu Azure portal). Oznámení se změní z **nasazení začalo** k **nasazení bylo úspěšné**.
    * Po oznámení změn **nasazení bylo úspěšné**vyberte **přejít k prostředku** na toto oznámení.

## <a name="try-the-default-bot"></a>Zkuste bot výchozí

Potvrďte, že byla nasazena robota kontrolou **oznámení**. Oznámení se změní z **probíhá nasazení...**  k **nasazení bylo úspěšné**. Vyberte **přejít k prostředku** tlačítko otevře se okno bodu robotů také prostředky.

<!-- this step isn't supposed to be necessary -->
## <a name="install-npm-resources"></a>Nainstalujte NPM prostředky
Nainstalujte balíčky NPM pomocí následujících kroků:

1. Vyberte **sestavení** z **Bot správu** část Web App Bot. 

2. A nové, sekundu, které otevře okno prohlížeče. Vyberte **otevřít online editor kódu**.

3. V horním navigačním panelu vyberte název webové aplikace robota `homeautomationluisbot`. 

4. V rozevíracím seznamu vyberte **otevřete konzoly Kudu**.

5. Otevře se nové okno prohlížeče. V konzole zadejte následující příkaz:

    ```console
    cd site\wwwroot && npm install
    ```

    Počkejte na dokončení procesu instalace. Vraťte se do první okno prohlížeče. 

## <a name="test-in-web-chat"></a>Testování webového chatu
Jakmile je registrovaný robota, vyberte **testování ve Web Chat** a otevřete tak podokno Web Chat. Zadejte "hello" v Web Chat.

  ![Testování ve Web Chat robota](./media/luis-tutorial-node-bot/bot-service-web-chat.png)

Robot reaguje vyslovením příkazu "dosáhli pozdrav. Je ale nutné dodat: hello ". Tím potvrdíte, že robota obdržel zprávu a předat aplikaci LUIS, který je vytvořen výchozí. Toto výchozí nastavení aplikace LUIS zjistil záměr pozdrav. V dalším kroku připojíte aplikaci LUIS, kterou jste dříve vytvořili místo výchozího aplikace LUIS robota.

## <a name="connect-your-luis-app-to-the-bot"></a>Připojte svou aplikaci LUIS robotu

Otevřít **nastavení aplikace** v první okno prohlížeče a úpravy **LuisAppId** pole obsahující ID aplikace pro vaši aplikaci LUIS.

  ![Aktualizovat ID aplikace LUIS v Azure](./media/luis-tutorial-node-bot/bot-service-app-id.png)

Pokud nemáte ID aplikace LUIS, přihlaste se k [LUIS](luis-reference-regions.md) webu pomocí stejného účtu, který používáte k přihlášení k Azure. Vyberte na **Moje aplikace**. 

1. Najdete LUIS aplikaci, kterou jste dříve vytvořili, a která obsahuje záměry a entity z HomeAutomation domény.

2. V **nastavení** stránky pro aplikaci LUIS, najít a zkopírovat ID aplikace.

3. Pokud ještě školení aplikaci, vyberte **trénování** tlačítko v pravém horním rohu tak moct trénovat vaší aplikace.

4. Pokud jste nepublikovali aplikaci, vyberte **publikovat** v horním navigačním panelu se otevře **publikovat** stránky. Vyberte slot Production (Produkční) a tlačítko **Publish** (Publikovat).

## <a name="modify-the-bot-code"></a>Úprava kódu robota

Přejít na druhé okno prohlížeče, pokud je stále otevřít nebo v prvním okně prohlížeče, vyberte **sestavení** a pak vyberte **otevřít online editor kódu**.

   ![Otevřít online editor kódu](./media/luis-tutorial-node-bot/bot-service-build.png)

V editoru kódu, otevřete `app.js`. Obsahuje následující kód:

```nodejs
/*-----------------------------------------------------------------------------
A simple Language Understanding (LUIS) bot for the Microsoft Bot Framework. 
-----------------------------------------------------------------------------*/

var restify = require('restify');
var builder = require('botbuilder');
var botbuilder_azure = require("botbuilder-azure");

// Setup Restify Server
var server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
   console.log('%s listening to %s', server.name, server.url); 
});
  
// Create chat connector for communicating with the Bot Framework Service
var connector = new builder.ChatConnector({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword,
    openIdMetadata: process.env.BotOpenIdMetadata 
});

// Listen for messages from users 
server.post('/api/messages', connector.listen());

/*----------------------------------------------------------------------------------------
* Bot Storage: This is a great spot to register the private state storage for your bot. 
* We provide adapters for Azure Table, CosmosDb, SQL Azure, or you can implement your own!
* For samples and documentation, see: https://github.com/Microsoft/BotBuilder-Azure
* ---------------------------------------------------------------------------------------- */

var tableName = 'botdata';
var azureTableClient = new botbuilder_azure.AzureTableClient(tableName, process.env['AzureWebJobsStorage']);
var tableStorage = new botbuilder_azure.AzureBotStorage({ gzipData: false }, azureTableClient);

// Create your bot with a function to receive messages from the user
// This default message handler is invoked if the user's utterance doesn't
// match any intents handled by other dialogs.
var bot = new builder.UniversalBot(connector, function (session, args) {
    session.send('You reached the default message handler. You said \'%s\'.', session.message.text);
});

bot.set('storage', tableStorage);

// Make sure you add code to validate these fields
var luisAppId = process.env.LuisAppId;
var luisAPIKey = process.env.LuisAPIKey;
var luisAPIHostName = process.env.LuisAPIHostName || 'westus.api.cognitive.microsoft.com';

const LuisModelUrl = 'https://' + luisAPIHostName + '/luis/v2.0/apps/' + luisAppId + '?subscription-key=' + luisAPIKey;

// Create a recognizer that gets intents from LUIS, and add it to the bot
var recognizer = new builder.LuisRecognizer(LuisModelUrl);
bot.recognizer(recognizer);

// Add a dialog for each intent that the LUIS app recognizes.
// See https://docs.microsoft.com/bot-framework/nodejs/bot-builder-nodejs-recognize-intent-luis 
bot.dialog('GreetingDialog',
    (session) => {
        session.send('You reached the Greeting intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Greeting'
})

bot.dialog('HelpDialog',
    (session) => {
        session.send('You reached the Help intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Help'
})

bot.dialog('CancelDialog',
    (session) => {
        session.send('You reached the Cancel intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Cancel'
})
```

Existující záměry v app.js jsou ignorovány. Můžete je nechat. 

## <a name="add-a-dialog-that-matches-homeautomationturnon"></a>Přidat dialog, který odpovídá HomeAutomation.TurnOn

Zkopírujte následující kód a přidejte ho do `app.js`.

```nodejs
bot.dialog('TurnOn',
    (session) => {
        session.send('You reached the TurnOn intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOn'
})
```

[Odpovídá] [ matches] možnost [triggerAction] [ triggerAction] připojené k dialogové okno určuje název záměr. Nástroj pro rozpoznávání se spustí pokaždé, když robota utterance přijme od uživatele. Pokud se shoduje s nejvyšší hodnocení záměr, který detekuje `triggerAction` vázán na dialogové okno, robota vyvolá dialogového okna.

## <a name="add-a-dialog-that-matches-homeautomationturnoff"></a>Přidat dialog, který odpovídá HomeAutomation.TurnOff

Zkopírujte následující kód a přidejte ho do `app.js`.

```nodejs
bot.dialog('TurnOff',
    (session) => {
        session.send('You reached the TurnOff intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOff'
})
```
## <a name="test-the-bot"></a>Testování robota

Na webu Azure Portal, vyberte na **testování ve Web Chat** otestovat robota. Zkuste zprávy typu like "vypnul světla" a "vypnout Moje Ohřívač" k vyvolání příkazů, které jste přidali do něj.
   ![Testování ve Web Chat HomeAutomation bot](./media/luis-tutorial-node-bot/bot-service-chat-results.png)

> [!TIP]
> Pokud zjistíte, že váš robot nerozpoznal vždy správné záměr nebo entity, zvýšit výkon aplikace LUIS tím, že je další příklad projevy jeho trénování. Mohou uchovávat aplikace LUIS bez jakékoli změny kódu vašeho robota. Zobrazit [přidání projevů příklad](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances) a [trénování a testování vaší aplikace LUIS](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-interactive-test).

## <a name="learn-more-about-bot-framework"></a>Další informace o Bot Frameworku
Další informace o [Bot Framework](https://dev.botframework.com/) a [3.x](https://github.com/Microsoft/BotBuilder) a [4.x](https://github.com/Microsoft/botbuilder-js) sady SDK.

## <a name="next-steps"></a>Další postup

<!-- From trying the bot, you can see that the recognizer can trigger interruption of the currently active dialog. Allowing and handling interruptions is a flexible design that accounts for what users really do. Learn more about the various actions you can associate with a recognized intent.--> Můžete zkusit přidat další záměrů, jako pomoc zrušit a pozdravu, na aplikaci LUIS. Potom přidejte dialogová okna pro novou záměry a otestovat je pomocí robota. 

<!-- 
> [!NOTE] 
> The default LUIS app that the template created contains example utterances for Cancel, Greeting, and Help intents. In the list of apps, find the app that begins with the name specified in **App name** in the **Bot Service** blade when you created the Bot Service. -->

> [!div class="nextstepaction"]
> [Přidání záměrů](./luis-how-to-add-intents.md)
> [dočištění řeči](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)


[intentDialog]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog.html

[intentDialog_matches]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog.html#matches 

[NotesSample]: https://github.com/Microsoft/BotFramework-Samples/tree/master/docs-samples/Node/basics-naturalLanguage

[triggerAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#triggeraction

[confirmPrompt]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#confirmprompt

[waterfall]: bot-builder-nodejs-dialog-manage-conversation-flow.md#manage-conversation-flow-with-a-waterfall

[session_userData]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session.html#userdata

[EntityRecognizer_findEntity]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.entityrecognizer.html#findentity

[matches]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#matches

[LUISAzureDocs]: https://docs.microsoft.com/azure/cognitive-services/LUIS/Home

[Dialog]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html

[IntentRecognizerSetOptions]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.iintentrecognizersetoptions.html

[LuisRecognizer]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.luisrecognizer

[LUISConcepts]: https://docs.botframework.com/node/builder/guides/understanding-natural-language/

[DisambiguationSample]: https://github.com/Microsoft/BotBuilder/tree/master/Node/examples/feature-onDisambiguateRoute

[IDisambiguateRouteHandler]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.idisambiguateroutehandler.html

[RegExpRecognizer]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.regexprecognizer.html

[AlarmBot]: https://github.com/Microsoft/BotBuilder/blob/master/Node/examples/basics-naturalLanguage/app.js

[LUISBotSample]: https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/intelligence-LUIS

[UniversalBot]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.universalbot.html


<!-- Old Links -->
[Github-BotFramework-Emulator-Download]: https://aka.ms/bot-framework-emulator
[Github-LUIS-Samples]: https://github.com/Microsoft/LUIS-Samples
[Github-LUIS-Samples-node-hotel-bot]: https://github.com/Microsoft/LUIS-Samples/tree/master/bot-integration-samples/hotel-finder/nodejs
[NodeJs]: https://nodejs.org/
[BFPortal]: https://dev.botframework.com/
[RegisterInstructions]: https://docs.microsoft.com/bot-framework/portal-register-bot
[BotFramework]: https://docs.microsoft.com/bot-framework/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions

