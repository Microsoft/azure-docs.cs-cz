---
title: 'Kurz: Language Understanding bot Node. js v4'
titleSuffix: Azure Cognitive Services
description: Pomocí Node. js sestavíte v tomto kurzu konverzaci robota integrovaná s jazykem Language porozumění (LUIS). Tento chatovací robot používá k rychlé implementaci řešení robota aplikaci Human Resources. K vytvoření robota se používá Bot Framework ve verzi 4 a Web App Bot Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 754d9d74a5d2c74a873145eaaddaaced29aa2ca8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75448003"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Kurz: použití robota webové aplikace s podporou Language Understanding v Node. js 

Pomocí Node. js můžete sestavit robota chatu integrovanou s jazykem Language porozumění (LUIS). Robot má vytvořenou službu Azure [Web App bot](https://docs.microsoft.com/azure/bot-service/) Resource a [bot Framework verze](https://github.com/Microsoft/botbuilder-dotnet) v4.

[!INCLUDE [Waiting for Bot refresh](./includes/wait-bot-upgrade.md)]

**V tomto kurzu se naučíte:**

> [!div class="checklist"]
> * Vytvořit Web App Bota. Tento proces za vás vytvoří novou aplikaci LUIS.
> * Stažení projektu robota vytvořeného službou Web bot Service
> * Spustit robota a emulátor místně na počítači
> * Zobrazit výsledky promluv v robotovi

## <a name="prerequisites"></a>Požadavky

* [Emulátor robota](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-a-web-app-bot-resource"></a>Vytvoření prostředku robota webové aplikace

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
    |Plán služby App Service / umístění|Neměňte z poskytnuté výchozí hodnoty.|
    |Application Insights|Neměňte z poskytnuté výchozí hodnoty.|
    |ID a heslo aplikace Microsoftu|Neměňte z poskytnuté výchozí hodnoty.|

1. V **šabloně robota**vyberte následující a pak klikněte na tlačítko **Vybrat** pod těmito nastaveními:

    |Nastavení|Účel|Výběr|
    |--|--|--|
    |SDK version (Verze sady SDK)|Bot framework version (Verze Bot Frameworku)|**SDK v4**|
    |SDK language (Jazyk sady SDK)|Programovací jazyk robota|**Node.js**|
    |Dole|Typ robota|**Basic Bot** (Základní robot)|
    
1. Vyberte **Vytvořit**. Tím se vytvoří a nasadí služba robota do Azure. Část tohoto procesu vytvoří aplikaci LUIS nazvanou `luis-nodejs-bot-XXXX`. Tento název vychází z názvu aplikace služby/Azure bot.

    [![vytvoření robota webové aplikace](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    Než budete pokračovat, počkejte, než se vytvoří služba robot.

## <a name="the-bot-has-a-language-understanding-model"></a>Robot má Language Understanding model

Proces vytvoření služby robot také vytvoří novou aplikaci LUIS s záměry a příkladem projevy. Robot poskytuje mapování záměrů na novou aplikaci LUIS pro následující záměry: 

|Záměry základního robota LUIS|Příklad promluvy|
|--|--|
|Kniha let|`Travel to Paris`|
|Zrušit|`bye`|
|Getpočasí|`what's the weather like?`|
|Žádné|Cokoli mimo doménu aplikace|

## <a name="test-the-bot-in-web-chat"></a>Testování ve Web Chat robota

1. I když jste stále v Azure Portal pro nový robot, vyberte **test na webu chat**. 
1. Do textového pole **Zadejte text zprávy** zadejte text `Book a flight from Seattle to Berlin tomorrow`. Robot odpoví s ověřením, že chcete zarezervovat let. 

    ![Snímek obrazovky Azure Portal zadejte text Hello.](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    K rychlému testování robota můžete použít funkci test. Pro úplnější testování, včetně ladění, Stáhněte si kód robota a použijte Visual Studio. 

## <a name="download-the-web-app-bot-source-code"></a>Stažení zdrojového kódu robota webové aplikace
Pokud chcete vyvíjet kód Web App Bota, stáhněte si kód a použijte ho ve svém místním počítači. 

1. Na portálu Azure Portal vyberte **Build** (Sestavení) v části **Bot management** (Správa robota). 

1. Vyberte **Download Bot source code** (Stáhnout zdrojový kód robota). 

    [![stáhnout zdrojový kód robota webové aplikace pro základní bot](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Když se automaticky otevře okno s dotazem **zahrnout nastavení aplikace ve staženém souboru ZIP?** vyberte **Ano**. To poskytuje LUIS nastavení. 

1. Když se zdrojový kód zazipuje, zobrazí se zpráva s odkazem na stažení kódu. Vyberte odkaz. 

1. Uložte soubor zip do místního počítače a extrahujte soubory. Otevřete projekt v aplikaci Visual Studio. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Přečtěte si kód, který odešle utterance do LUIS a získá odpověď.

1. Chcete-li odeslat uživatele utterance do koncového bodu předpovědi LUIS, otevřete **dialogová okna > flightBookingRecognizer. js** . Tady se uživatelská promluva zadaná do robota odešle do služby LUIS. Odpověď z LUIS je vrácena z metody **executeLuisQuery** .  

    ````javascript
    class FlightBookingRecognizer {

        ...

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        ...

    }
    ````

1. **Dialogy-> mainDialog** zachycuje utterance a pošle je do executeLuisQuery v metodě actStep.


    ````javascript
    class MainDialog extends ComponentDialog {

        constructor(luisRecognizer, bookingDialog) {
            ...
            this.luisRecognizer = luisRecognizer;
            ...
        }


        ...

        /**
         * Second step in the waterfall.  This will use LUIS to attempt to extract the origin, destination and travel dates.
         * Then, it hands off to the bookingDialog child dialog to collect any remaining details.
         */
        async actStep(stepContext) {

            ...

            const luisResult = await this.luisRecognizer.executeLuisQuery(stepContext.context);

            switch (LuisRecognizer.topIntent(luisResult)) {
                    case 'BookFlight':
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
            
                    case 'GetWeather':
                        // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                        const getWeatherMessageText = 'TODO: get weather flow here';
                        await stepContext.context.sendActivity(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                        break;
            
                    default:
                        // Catch all for unhandled intents
                        const didntUnderstandMessageText = `Sorry, I didn't get that. Please try asking in a different way (intent was ${ LuisRecognizer.topIntent(luisResult) })`;
                        await stepContext.context.sendActivity(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
                    }
            
                    return await stepContext.next();

        }

        ...

    }
    ````
<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Použití emulátoru bot k otestování robota

Zeptejte se robota na záměr na knihu letu.

1. Spusťte emulátor bot a vyberte **otevřít robot**.
1. V překryvném okně **otevřete robota** zadejte adresu URL robota, například `http://localhost:3978/api/messages`. `/api/messages` trasa je webová adresa pro robot.
1. Zadejte **ID aplikace Microsoftu** a **heslo aplikace Microsoftu**, které najdete v souboru **. env** v kořenovém adresáři kódu robota, který jste stáhli.

1. V emulátoru bot zadejte `Book a flight from Seattle to Berlin tomorrow` a získejte stejnou reakci na základní bot, jak jste dostali v **testu na webu chat**.

    [![základní odpověď robota v emulátoru](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Vyberte **Ano**. Robot odpoví souhrnem jeho akcí. 
1. V protokolu emulátoru bot vyberte řádek, který obsahuje `Luis Trace`. Tím se zobrazí odpověď JSON z LUIS pro záměr a entity utterance.

    [![základní odpověď robota v emulátoru](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Další kroky

Podívejte se na další [ukázky](https://github.com/microsoft/botframework-solutions) s konverzací roboty. 

> [!div class="nextstepaction"]
> [Vytvoření aplikace Language Understanding s vlastní doménou předmětu](luis-quickstart-intents-only.md)