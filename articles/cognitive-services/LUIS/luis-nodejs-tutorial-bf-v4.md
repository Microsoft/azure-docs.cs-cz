---
title: Robot LUIS pomocí Node.js – Kurz – Web App Bot – Bot Framework SDK 4.0
titleSuffix: Azure Cognitive Services
description: Pomocí Node.js vytvořte chatovacího robota integrovaného se službou Language Understanding (LUIS). Tento chatovací robot používá k rychlé implementaci řešení robota aplikaci Human Resources. K vytvoření robota se používá Bot Framework ve verzi 4 a Web App Bot Azure.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: ad21754b3f55a0d14bb43a2898d5bd4b8b8150ae
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49385902"
---
# <a name="tutorial-luis-bot-in-nodejs"></a>Kurz: Robot LUIS v Node.js
Pomocí Node.js můžete vytvořit chatovacího robota integrovaného se službou Language Understanding (LUIS). Tento robot používá k implementaci řešení robota aplikaci HomeAutomation. K vytvoření robota se používá [Web App Bot](https://docs.microsoft.com/azure/bot-service/) Azure a [Bot Framework ve verzi](https://github.com/Microsoft/botbuilder-js) v4.

**V tomto kurzu se naučíte:**

> [!div class="checklist"]
> * Vytvořit Web App Bota. Tento proces za vás vytvoří novou aplikaci LUIS.
> * Přidat předem připravenou doménu do nového modelu LUIS
> * Stáhnout si projekt vytvořený službou webového robota
> * Spustit robota a emulátor místně na počítači
> * Upravit kód robota pro nové záměry LUIS
> * Zobrazit výsledky promluv v robotovi

## <a name="prerequisites"></a>Požadavky

<!--* Samples from 
https://github.com/Microsoft/BotBuilder-Samples/tree/v4/javascript_nodejs/12.nlp-with-luis-->
* [Emulátor robota](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-web-app-bot"></a>Vytvoření Web App Bota

1. Na portálu **Azure Portal** vyberte [Vytvořit nový prostředek](https://portal.azure.com).

2. Ve vyhledávací poli vyhledejte a vyberte **Web App Bot**. Vyberte **Vytvořit**.

3. V **Bot Service** (Služba robota) zadejte požadované informace:

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

4. V **nastaveních šablony robota** vyberte následující možnosti a pak v těchto nastaveních zvolte tlačítko **Vybrat**:

    |Nastavení|Účel|Výběr|
    |--|--|--|
    |SDK version (Verze sady SDK)|Bot framework version (Verze Bot Frameworku)|**SDK v4**|
    |SDK language (Jazyk sady SDK)|Programovací jazyk robota|**Node.js**|
    |Echo/Basic Bot (Robot Echo/Základní robot)|Typ robota|**Basic Bot** (Základní robot)|
    
5. Vyberte **Vytvořit**. Tím se vytvoří a nasadí služba robota do Azure. Část tohoto procesu vytvoří aplikaci LUIS nazvanou `luis-nodejs-bot-XXXX`. Tento název je založený na názvu robota a aplikace v předchozí části.

    [ ![Vytvoření Web App Bota](./media/bfv4-nodejs/create-web-app-service.png) ](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

6. Tuto kartu prohlížeče nechte otevřenou. Pro všechny kroky s portálem LUIS otevřete novou kartu prohlížeče. Po nasazení nové služby robota pokračujte k další části.

## <a name="add-prebuilt-domain-to-model"></a>Přidání předem připravené domény do modelu
Část nasazení služby robota vytvoří novou aplikaci LUIS se záměry a příklady promluv. Robot poskytuje mapování záměrů na novou aplikaci LUIS pro následující záměry: 

|Záměry základního robota LUIS|Příklad promluvy|
|--|--|
|Zrušit|`stop`|
|Pozdrav|`hello`|
|Nápověda|`help`|
|Žádný|Cokoli mimo doménu aplikace|

Přidejte do modelu předem připravenou aplikaci HomeAutomation, která má zpracovávat promluvy jako: `Turn off the living room lights`

1. Přejděte na portál [LUIS](https://www.luis.ai) a přihlaste se.
2. Na stránce **My Apps** (Moje aplikace) vyberte sloupec **Created date** (Datum vytvoření) k seřazení podle data, kdy byla aplikace vytvořena. Služba Azure Bot Service vytvořila novou aplikaci v předchozí části. Její název je `luis-nodejs-bot-` + `<your-name>` + 4 náhodné znaky.
3. Otevřete aplikaci a vyberte část **Build** (Sestavení) v horním navigačním panelu.
4. V levém navigačním panelu vyberte **Prebuilt Domains** (Předem připravené domény).
5. Vyberte doménu **HomeAutomation** vybráním možnosti **Add domain** (Přidat doménu) na kartě.
6. V nabídce vpravo nahoře vyberte **Train** (Trénovat).
7. V nabídce vpravo nahoře vyberte **Publish** (Publikovat). 

    Aplikace vytvořená službou Azure Bot Service teď obsahuje nové záměry:

    |Nové záměry základního robota|Příklad promluvy|
    |--|--|
    |HomeAutomation.TurnOn|`turn the fan to high`
    |HomeAutomation.TurnOff|`turn off ac please`|

## <a name="download-the-web-app-bot"></a>Stažení Web App Bota 
Pokud chcete vyvíjet kód Web App Bota, stáhněte si kód a použijte ho ve svém místním počítači. 

1. Na portálu Azure Portal, stále v prostředku Web App Bot, vyberte **Application Settings** (Nastavení aplikace) a zkopírujte hodnoty **botFilePath** a **botFileSecret**. Později je budete muset přidat do souboru prostředí. 

2. Na portálu Azure Portal vyberte **Build** (Sestavení) v části **Bot management** (Správa robota). 

3. Vyberte **Download Bot source code** (Stáhnout zdrojový kód robota). 

    [ ![Stáhnout zdrojový kód Web App Bota pro základního robota](../../../includes/media/cognitive-services-luis/bfv4/download-code.png) ](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

4. Když se zdrojový kód zazipuje, zobrazí se zpráva s odkazem na stažení kódu. Vyberte odkaz. 

5. Uložte soubor zip do místního počítače a extrahujte soubory. Otevřete projekt. 

6. Otevřete soubor bot.js a najděte `const results = await this.luisRecognizer.recognize(context);`. Tady se uživatelská promluva zadaná do robota odešle do služby LUIS.

    ```javascript
    /**
     * Driver code that does one of the following:
     * 1. Display a welcome card upon startup
     * 2. Use LUIS to recognize intents
     * 3. Start a greeting dialog
     * 4. Optionally handle Cancel or Help interruptions
     *
     * @param {Context} context turn context from the adapter
     */
    async onTurn(context) {
        // Create a dialog context
        const dc = await this.dialogs.createContext(context);

        if(context.activity.type === ActivityTypes.Message) {
            // Perform a call to LUIS to retrieve results for the current activity message.
            const results = await this.luisRecognizer.recognize(context);
            
            const topIntent = LuisRecognizer.topIntent(results);

            // handle conversation interrupts first
            const interrupted = await this.isTurnInterrupted(dc, results);
            if(interrupted) {
                return;
            }

            // Continue the current dialog
            const dialogResult = await dc.continue();

            switch(dialogResult.status) {
                case DialogTurnStatus.empty:
                    switch (topIntent) {
                        case GREETING_INTENT:
                            await dc.begin(GREETING_DIALOG);
                            break;

                        case NONE_INTENT:
                        default:
                            // help or no intent identified, either way, let's provide some help
                            // to the user
                            await dc.context.sendActivity(`I didn't understand what you just said to me. topIntent ${topIntent}`);
                            break;
                    }

                case DialogTurnStatus.waiting:
                    // The active dialog is waiting for a response from the user, so do nothing
                break;

                case DialogTurnStatus.complete:
                    await dc.end();
                    break;

                default:
                    await dc.cancelAll();
                    break;

            }

        } else if (context.activity.type === 'conversationUpdate' && context.activity.membersAdded[0].name === 'Bot') {
            // When activity type is "conversationUpdate" and the member joining the conversation is the bot
            // we will send our Welcome Adaptive Card.  This will only be sent once, when the Bot joins conversation
            // To learn more about Adaptive Cards, see https://aka.ms/msbot-adaptivecards for more details.
            const welcomeCard = CardFactory.adaptiveCard(WelcomeCard);
            await context.sendActivity({ attachments: [welcomeCard] });
        }
    }
    ```

    Robot odešle uživatelovu promluvu do služby LUIS a obdrží výsledky. Hlavní záměr určuje tok konverzace. 


## <a name="start-the-bot"></a>Spuštění robota
Před změnou kódu nebo nastavení zkontrolujte, jestli robot funguje. 

1. Ve Visual Studio Codu otevřete okno terminálu. 

2. Nainstalujte závislosti npm pro tohoto robota. 

    ```bash
    npm install
    ```
3. Vytvořte soubor pro proměnné prostředí, které kód robota hledá. Pojmenujte soubor `.env`. Přidejte tyto proměnné prostředí:

    <!--there is no code language that represents an .env file correctly-->
    ```
    botFilePath=
    botFileSecret=
    ```

    Nastavte hodnoty proměnných prostředí na hodnoty, které jste zkopírovali z Application Settings (Nastavení aplikace) služby Azure Bot Service v 1. kroku v části **[Stažení Web App Bota](#download-the-web-app-bot)**.

4. Spusťte robota v režimu sledování. Všechny změny provedené v kódu po tomto spuštění způsobí automatické restartování aplikace.

    ```bash
    npm run watch
    ```

5. Při spuštění robota se v okně terminálu zobrazí místní port, na kterém robot běží:

    ```
    > basic-bot@0.1.0 start C:\Users\pattiowens\repos\BFv4\luis-nodejs-bot-src
    > node ./index.js NODE_ENV=development

    restify listening to http://[::]:3978
    
    Get the Emulator: https://aka.ms/botframework-emulator
    
    To talk to your bot, open the luis-nodejs-bot-pattiowens.bot file in the Emulator
    ```

## <a name="start-the-emulator"></a>Spusťte emulátor.

1. Spusťte emulátor robota. 

2. V emulátoru robota vyberte soubor *.bot v kořenovém adresáři projektu. Tento soubor `.bot` obsahuje koncový bod adresy URL robota pro zprávy:

    [ ![Emulátor robota v4](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png) ](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png#lightbox)

3. Zadejte tajný kód robota, který jste zkopírovali z Application Settings (Nastavení aplikace) služby Azure Bot Service v 1. kroku v části **[Stažení Web App Bota](#download-the-web-app-bot)**. Emulátoru to umožní přístup k zašifrovaným polím v souboru .bot.

    ![Tajný kód emulátoru robota v4](../../../includes/media/cognitive-services-luis/bfv4/bot-secret.png)


4. V emulátoru robota zadejte `Hello` a získejte správnou odpověď pro základního robota.

    [ ![Odpověď základního robota v emulátoru](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png) ](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png#lightbox)

## <a name="modify-bot-code"></a>Úprava kódu robota 

Do souboru `bot.js` přidejte kód pro zpracování nových záměrů. 

1. V horní části souboru najděte část **Supported LUIS Intents** (Podporované záměry LUIS) a přidejte konstanty pro záměry HomeAutomation:

    ```javascript
    // Supported LUIS Intents
    const GREETING_INTENT = 'Greeting';
    const CANCEL_INTENT = 'Cancel';
    const HELP_INTENT = 'Help';
    const NONE_INTENT = 'None';
    const TURNON_INTENT = 'HomeAutomation_TurnOn'; // new intent
    const TURNOFF_INTENT = 'HomeAutomation_TurnOff'; // new intent
    ```

    Všimněte si, že tečka, `.`, mezi doménou a záměrem z aplikace portálu LUIS se nahradí podtržítkem, `_`. 

2. Najděte parametr **isTurnInterrupted**, který přijímá předpověď LUIS promluvy a přidejte řádek, který vytiskne výsledek do konzoly.

    ```node
    /**
     * Look at the LUIS results and determine if we need to handle
     * an interruptions due to a Help or Cancel intent
     *
     * @param {DialogContext} dc - dialog context
     * @param {LuisResults} luisResults - LUIS recognizer results
     */
    async isTurnInterrupted(dc, luisResults) {
        console.log(JSON.stringify(luisResults));
    ...
    ```

    Robot nemá přesně stejnou odpověď jako žádost rozhraní REST API služby LUIS, proto je důležité zjistit rozdíl prohlédnutím odpovědi JSON. Vlastnosti textu a záměrů jsou stejné, ale hodnoty vlastností entity jsou změněné. 

    ```JSON
    {
        "$instance": {
            "HomeAutomation_Device": [
                {
                    "startIndex": 23,
                    "endIndex": 29,
                    "score": 0.9776345,
                    "text": "lights",
                    "type": "HomeAutomation.Device"
                }
            ],
            "HomeAutomation_Room": [
                {
                    "startIndex": 12,
                    "endIndex": 22,
                    "score": 0.9079433,
                    "text": "livingroom",
                    "type": "HomeAutomation.Room"
                }
            ]
        },
        "HomeAutomation_Device": [
            "lights"
        ],
        "HomeAutomation_Room": [
            "livingroom"
        ]
    }
    ```

3. Přidejte záměry do příkazu switch metody onTurn pro případ `DialogTurnStatus.empty`:

    ```javascript
    switch (topIntent) {
        case GREETING_INTENT:
            await dc.begin(GREETING_DIALOG);
            break;

        // New HomeAutomation.TurnOn intent
        case TURNON_INTENT: 

            await dc.context.sendActivity(`TurnOn intent found, entities included: ${JSON.stringify(results.entities)}`);
            break;

        // New HomeAutomation.TurnOff intent
        case TURNOFF_INTENT: 

            await dc.context.sendActivity(`TurnOff intent found, entities included: ${JSON.stringify(results.entities)}`);
            break;

        case NONE_INTENT:
        default:
            // help or no intent identified, either way, let's provide some help
            // to the user
            await dc.context.sendActivity(`I didn't understand what you just said to me. topIntent ${topIntent}`);
            break;
    }
    ```

## <a name="view-results-in-bot"></a>Zobrazení výsledků v robotovi

1. V emulátoru robota zadejte promluvu: `Turn on the livingroom lights to 50%`

2. Robot odpoví takto:

    ```JSON
    TurnOn intent found, entities included: {"$instance":{“HomeAutomation_Device”:[{“startIndex”:23,“endIndex”:29,“score”:0.9776345,“text”:“lights”,“type”:“HomeAutomation.Device”}],“HomeAutomation_Room”:[{“startIndex”:12,“endIndex”:22,“score”:0.9079433,“text”:“livingroom”,“type”:“HomeAutomation.Room”}]},“HomeAutomation_Device”:[“lights”],“HomeAutomation_Room”:[“livingroom”]}
    ```

## <a name="learn-more-about-bot-framework"></a>Další informace o Bot Frameworku
Azure Bot Service využívá sadu SDK Bot Frameworku. Přečtěte si další informace o sadě SDK a Bot Frameworku:

* Dokumentace pro [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0) v4
* [Ukázky Bot Builderu](https://github.com/Microsoft/botbuilder-samples)
* [Sada SK Bot Builderu](https://docs.microsoft.com/javascript/api/botbuilder-core/?view=botbuilder-ts-latest)
* [Nástroje Bot Builderu](https://github.com/Microsoft/botbuilder-tools):

## <a name="next-steps"></a>Další kroky

Vytvořili jste Azure Bot Service, zkopírovali jste tajný klíč robota a cestu k souboru .bot a stáhli jste soubor zip kódu. Přidali jste předem připravenou doménu HomeAutomation do aplikace LUIS vytvořené v rámci nové služby Azure Bot Service a pak aplikaci vyškolili a znovu publikovali. Extrahovali jste projekt kódu, vytvořili soubor prostředí (`.env`) a nastavili tajný kód robota a cestu k souboru .bot. Do souboru bot.js jste přidali kód pro zpracování dvou nových záměrů. Pak jste robota otestovali v emulátoru robota, abyste viděli odpověď služby LUIS pro promluvu jednoho z nových záměrů. 


> [!div class="nextstepaction"]
> [Vytvoření vlastní domény v LUIS](luis-quickstart-intents-only.md)
