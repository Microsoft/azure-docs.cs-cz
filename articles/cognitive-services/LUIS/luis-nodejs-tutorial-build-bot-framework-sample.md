---
title: Integrovat LEOŠ robotu pomocí robota Tvůrce SDK pro Node.js v Azure | Microsoft Docs
description: Sestavení robotu integrovat LEOŠ aplikace pomocí rozhraní robota.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/06/2018
ms.author: v-geberr
ms.openlocfilehash: 5d9b78977457f818b964adb16ebb5e9e5872aa2c
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264969"
---
# <a name="integrate-luis-with-a-bot-using-the-bot-builder-sdk-for-nodejs"></a>Integrovat LEOŠ robotu pomocí robota Tvůrce SDK pro Node.js

Tento kurz vás provede procesem vytváření robota s [robota Framework] [ BotFramework] je integrována LEOŠ aplikace.

## <a name="prerequisite"></a>Požadavek

Než vytvoříte robota, postupujte podle kroků v [vytvořit aplikaci](./luis-get-started-create-app.md) k sestavení LEOŠ aplikaci, která se používá.

Robota odpoví na záměry z HomeAutomation domény, které jsou v aplikaci LEOŠ. Pro každou z těchto tříd Intent poskytuje aplikace LEOŠ záměrem, která se mapuje na ni. Robota poskytuje dialog, který zpracovává záměr, který zjistí LEOŠ.

| Záměr | Příklad utterance | Funkce robota |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | Zapněte světla. | Vyvolá robota `TurnOnDialog` při `HomeAutomation.TurnOn` je zjištěna. Toto dialogové okno je, kde by vyvolání služby IoT můžete zapnout v zařízení a informace pro uživatele, který zařízení je zapnutý. |
| HomeAutomation.TurnOff | Indikátory ložnici vypněte. | Vyvolá robota `TurnOffDialog` při `HomeAutomation.TurnOff` je zjištěna. Toto dialogové okno, kde by vyvolání služby IoT vypněte zařízení a informace pro uživatele, který zařízení je vypnutá. |


## <a name="create-a-language-understanding-bot-with-bot-service"></a>Vytvořit znalosti jazyka robota službou robota

1. V [portál Azure](https://portal.azure.com), vyberte **vytvořit nový prostředek** v okně nabídku a vyberte **zobrazit všechny**.

    ![Vytvořit nový prostředek](./media/luis-tutorial-node-bot/bot-service-creation.png)

2. Do vyhledávacího pole Hledat **webové aplikace robota**. 

    ![Vytvořit nový prostředek](./media/luis-tutorial-node-bot/bot-service-selection.png)

3. V **robota služby** okno, zadejte požadované informace a vyberte **vytvořit**. Tím se vytvoří a nasadí robota služby a aplikace LEOŠ do Azure. Pokud chcete použít [řeči dočištění](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming), zkontrolujte [požadavků oblasti](luis-resources-faq.md#what-luis-regions-support-bot-framework-speech-priming) před vytvořením vaší robota. 
    * Nastavit **název aplikace** na název vaší robota. Název se používá jako subdoménou, když vaše robota je nasazená do cloudu (například mynotesbot.azurewebsites.net). <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
    * Vyberte předplatné, [skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), plán služby App service, a [umístění](https://azure.microsoft.com/regions/).
    * Vyberte **znalosti jazyka (Node.js)** šablonu pro **robota šablony** pole.
    * Vyberte **umístění aplikace LEOŠ**. Toto je pro tvorbu [oblast] [ LUIS] vytvoří se v aplikaci.
    * Zaškrtněte políčko potvrzení právní upozornění. Právní upozornění. podmínky jsou uvedeny níže políčko.

    ![Okno robota služby](./media/luis-tutorial-node-bot/bot-service-setting-callout-template.png)


4. Potvrďte, že byla nasazena službu robota.
    * Vyberte oznámení (na ikonu zvonku umístěné podél horního okraje portálu Azure). Oznámení se změní ze **nasazení začalo** k **nasazení bylo úspěšné**.
    * Po oznámení změn na **nasazení bylo úspěšné**, vyberte **přejděte k prostředku** na tomto oznámení.

## <a name="try-the-default-bot"></a>Zkuste robota výchozí

Potvrďte, že byla nasazena robota kontrolou **oznámení**. Oznámení se změní ze **nasazení probíhá...**  k **nasazení bylo úspěšné**. Vyberte **přejděte k prostředku** tlačítko otevřete okno prostředky robota.

<!-- this step isn't supposed to be necessary -->
## <a name="install-npm-resources"></a>Nainstalujte NPM prostředky
Nainstalujte NPM balíčky pomocí následujících kroků:

1. Vyberte **sestavení** z **robota správu** robota aplikace webové části. 

2. A nové, druhé otevře okno prohlížeče. Vyberte **editor otevřete online kódu**.

3. V horním navigačním panelu, vyberte název webové aplikace robota `homeautomationluisbot`. 

4. V rozevíracím seznamu **otevřít konzolu Kudu**.

5. Otevře se nové okno prohlížeče. V konzole zadejte následující příkaz:

    ```
    cd site\wwwroot && npm install
    ```

    Počkejte na dokončení procesu instalace. Vraťte se k první okno prohlížeče. 

## <a name="test-in-web-chat"></a>Testování v webového chatu
Po registraci robota vyberte **Test v webového chatu** otevřete podokno webového chatu. Zadejte "hello" v webového chatu.

  ![Testování robota v webového chatu](./media/luis-tutorial-node-bot/bot-service-web-chat.png)

Odpoví robota vyslovením "bylo dosaženo pozdrav. Jste uvedli: hello ". Tím potvrdí, že má robota přijal zprávu a předaný výchozí LEOŠ aplikace, který vytvořil. Toto výchozí nastavení aplikace LEOŠ zjištěna pozdravu záměr. V dalším kroku připojíte robota LEOŠ aplikaci, kterou jste dříve vytvořili místo výchozího LEOŠ aplikace.

## <a name="connect-your-luis-app-to-the-bot"></a>Připojení aplikace LEOŠ k robota

Otevřete **nastavení aplikace** v první okno prohlížeče a upravit **LuisAppId** pole tak, aby obsahovala ID aplikace LEOŠ aplikace.

  ![Aktualizovat LEOŠ ID aplikace v Azure](./media/luis-tutorial-node-bot/bot-service-app-id.png)

Pokud nemáte ID aplikace LEOŠ, přihlaste se k [LEOŠ](luis-reference-regions.md) webu pomocí stejného účtu, který používáte k přihlášení do Azure. Vyberte na **Moje aplikace**. 

1. Najděte LEOŠ aplikaci, kterou jste dříve vytvořili, obsahující tříd Intent a entity z domény HomeAutomation.

2. V **nastavení** stránku pro aplikaci LEOŠ, najít a zkopírovat ID aplikace.

3. Pokud nebyly Trénink aplikace, vyberte **cvičení** tlačítko v horní části pro učení vaší aplikace.

4. Pokud jste ještě publikovali aplikaci, vyberte **publikovat** v horním navigačním panelu otevřete **publikovat** stránky. Vyberte produkční slot a **publikovat** tlačítko.

## <a name="modify-the-bot-code"></a>Upravit kód robota

Přejít do okna druhý prohlížeče, pokud je stále otevřít nebo v prvním okně prohlížeče, vyberte **sestavení** a pak vyberte **editor otevřete online kódu**.

   ![Otevřít online editor kódu](./media/luis-tutorial-node-bot/bot-service-build.png)

V editoru kódu otevřete `app.js`. Obsahuje následující kód:

```javascript
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

Existující záměry v app.js se ignorují. Můžete je nechat. 

## <a name="add-a-dialog-that-matches-homeautomationturnon"></a>Přidat dialog, který odpovídá HomeAutomation.TurnOn

Zkopírujte následující kód a přidejte ho do `app.js`.

```javascript
bot.dialog('TurnOn',
    (session) => {
        session.send('You reached the TurnOn intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOn'
})
```

[Odpovídá] [ matches] možnost [triggerAction] [ triggerAction] připojené k dialogu Určuje název záměr. Nástroj pro rozpoznávání se spustí pokaždé, když robota obdrží utterance od uživatele. Pokud odpovídá nejvyšším vyhodnocování záměr, který detekuje `triggerAction` vázaný do dialogového okna, robota vyvolá tento dialog.

## <a name="add-a-dialog-that-matches-homeautomationturnoff"></a>Přidat dialog, který odpovídá HomeAutomation.TurnOff

Zkopírujte následující kód a přidejte ho do `app.js`.

```javascript
bot.dialog('TurnOff',
    (session) => {
        session.send('You reached the TurnOff intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOff'
})
```
## <a name="test-the-bot"></a>Testovací robota

Na portálu Azure vyberte na **testů ve webové Chat** k testování robota. Zkuste zprávy typu like "Zapnout indikátory" a "vypnout Moje topení" k vyvolání záměry, které jste přidali do ní.
   ![Testování HomeAutomation robota v webového chatu](./media/luis-tutorial-node-bot/bot-service-chat-results.png)

> [!TIP]
> Pokud zjistíte, že vaše robota nerozpoznal vždy správné záměr nebo entity, zlepšit výkon aplikace LEOŠ tím, že je další příklad utterances ke cvičení ho. Můžete přeučování LEOŠ aplikace bez nutnosti jakékoli úpravy kódu vaší robota. V tématu [přidat příklad utterances](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances) a [trénování a testování aplikace s LEOŠ](https://docs.microsoft.com/azure/cognitive-services/LUIS/interactive-test).

## <a name="learn-more-about-bot-framework"></a>Další informace o robota Framework
Další informace o [robota Framework](https://dev.botframework.com/) a [3.x](https://github.com/Microsoft/BotBuilder) a [4.x](https://github.com/Microsoft/botbuilder-js) sady SDK.

## <a name="next-steps"></a>Další postup

<!-- From trying the bot, you can see that the recognizer can trigger interruption of the currently active dialog. Allowing and handling interruptions is a flexible design that accounts for what users really do. Learn more about the various actions you can associate with a recognized intent.--> Zkuste přidat jiné záměry, jako je Nápověda, zrušit a pozdravu, do LEOŠ aplikace. Pak přidejte dialogová okna pro novou tříd Intent a testování pomocí robota. 

<!-- 
> [!NOTE] 
> The default LUIS app that the template created contains example utterances for Cancel, Greeting, and Help intents. In the list of apps, find the app that begins with the name specified in **App name** in the **Bot Service** blade when you created the Bot Service. -->

> [!div class="nextstepaction"]
> [Přidat tříd Intent](./luis-how-to-add-intents.md)
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

