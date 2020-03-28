---
title: 'Kurz: Jazykporozumění Bot Node.js v4'
description: Pomocí Node.js vytvořte chatovacího robota integrovaného s porozuměním jazykům (LUIS) v tomto kurzu. Tento chatovací robot používá k rychlé implementaci řešení robota aplikaci Human Resources. K vytvoření robota se používá Bot Framework ve verzi 4 a Web App Bot Azure.
ms.topic: tutorial
ms.date: 02/03/2020
ms.openlocfilehash: 3ce12176957412a5599ced8b043f553969194efb
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987832"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Kurz: Použití webappového robota s povoleným jazykem v souboru Node.js

Pomocí souboru Node.js vytvořte chatovacího robota integrovaného s porozuměním jazykům (LUIS). Bot je sestavený s prostředkem [robota Azure Web App](https://docs.microsoft.com/azure/bot-service/) a [bot frameworkem verze](https://github.com/Microsoft/botbuilder-dotnet) V4.

**V tomto kurzu se dozvíte, jak:**

> [!div class="checklist"]
> * Vytvořit Web App Bota. Tento proces za vás vytvoří novou aplikaci LUIS.
> * Stáhněte si projekt botů vytvořený službou Web bot
> * Spustit robota a emulátor místně na počítači
> * Zobrazit výsledky promluv v robotovi

## <a name="prerequisites"></a>Požadavky

* [Emulátor robota](https://aka.ms/abs/build/emulatordownload)
* [Kód visual studia](https://code.visualstudio.com/Download)


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
    |Plán/umístění služby Aplikace|Neměňte z poskytnuté výchozí hodnoty.|
    |Application Insights|Neměňte z poskytnuté výchozí hodnoty.|
    |ID aplikace Microsoft a heslo|Neměňte z poskytnuté výchozí hodnoty.|

1. V **šabloně Bot**vyberte následující položku a pak vyberte tlačítko **Vybrat** v těchto nastaveních:

    |Nastavení|Účel|Výběr|
    |--|--|--|
    |SDK version (Verze sady SDK)|Bot framework version (Verze Bot Frameworku)|**SDK v4**|
    |SDK language (Jazyk sady SDK)|Programovací jazyk robota|**Node.js**|
    |Bot|Typ robota|**Basic Bot** (Základní robot)|

1. Vyberte **Vytvořit**. Tím se vytvoří a nasadí služba robota do Azure. Část tohoto procesu vytvoří aplikaci LUIS nazvanou `luis-nodejs-bot-XXXX`. Tento název je založen na názvu aplikace /Azure Bot Service.

    > [!div class="mx-imgBorder"]
    > [![Vytvoření Web App Bota](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    Před pokračováním počkejte, až bude služba robota vytvořena.

## <a name="the-bot-has-a-language-understanding-model"></a>Bot má model language understanding

Proces vytváření služby robota také vytvoří novou aplikaci LUIS se záměry a příklady projevy. Robot poskytuje mapování záměrů na novou aplikaci LUIS pro následující záměry:

|Záměry základního robota LUIS|Příklad promluvy|
|--|--|
|Rezervovat let|`Travel to Paris`|
|Zrušit|`bye`|
|GetWeather|`what's the weather like?`|
|Žádný|Cokoli mimo doménu aplikace|

## <a name="test-the-bot-in-web-chat"></a>Testování robota ve webovém chatu

1. Zatímco ještě na portálu Azure pro nového robota, vyberte **Test ve webovém chatu**.
1. Do textového pole **Zadejte** text `Book a flight from Seattle to Berlin tomorrow`zprávy . Bot odpoví ověřením, že chcete rezervovat let.

    ![Snímek obrazovky portálu Azure zadejte text "ahoj".](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    Pomocí funkce testu můžete rychle otestovat svého robota. Chcete-li provést úplnější testování, včetně ladění, stáhněte kód robota a použijte Visual Studio.

## <a name="download-the-web-app-bot-source-code"></a>Stažení zdrojového kódu robota webové aplikace
Pokud chcete vyvíjet kód Web App Bota, stáhněte si kód a použijte ho ve svém místním počítači.

1. Na portálu Azure Portal vyberte **Build** (Sestavení) v části **Bot management** (Správa robota).

1. Vyberte **Download Bot source code** (Stáhnout zdrojový kód robota).

    [![Stáhněte si zdrojový kód robota z webových aplikací pro základní robota](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Když se vyskakovací dialog zeptá Zahrnout nastavení **Yes** **aplikace do staženého souboru zip?** To poskytuje nastavení LUIS.

1. Když se zdrojový kód zazipuje, zobrazí se zpráva s odkazem na stažení kódu. Vyberte odkaz.

1. Uložte soubor zip do místního počítače a extrahujte soubory. Otevřete projekt pomocí sady Visual Studio.

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Zkontrolujte kód pro odeslání utterance luis a získat odpověď

1. Chcete-li odeslat projev uživatele do koncového bodu předpověď LUIS, otevřete **dialogy -> flightBookingRecognizer.js** soubor. Tady se uživatelská promluva zadaná do robota odešle do služby LUIS. Odpověď z LUIS je vrácena z **metody executeLuisQuery.**

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

1. **Dialogy -> mainDialog** zachytí utterance a odešle jej do executeLuisQuery v actStep metoda.


    ````javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { LuisRecognizer } = require('botbuilder-ai');

    class FlightBookingRecognizer {
        constructor(config) {
            const luisIsConfigured = config && config.applicationId && config.endpointKey && config.endpoint;
            if (luisIsConfigured) {
                this.recognizer = new LuisRecognizer(config, {}, true);
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
    ````
<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Použijte emulátor bot otestovat bot

Zeptejte se bot otázku pro záměr book letu.

1. Začněte Semulator botů a vyberte **Otevřít bota**.
1. V automaticky otevíraném **dialogovém** okně Otevřít `http://localhost:3978/api/messages`robota zadejte adresu URL robota, například . Trasa `/api/messages` je webová adresa robota.
1. Zadejte **ID aplikace Microsoft** a heslo aplikace Microsoft **App**, které se nachází v souboru **ENV** v kořenovém adresáři staženého kódu robota.

1. V emulátoru bot, `Book a flight from Seattle to Berlin tomorrow` zadejte a získat stejnou odpověď pro základní bot, jak jste obdrželi v **Test in Web Chat**.

    [![Základní odpověď robota v emulátoru](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Vyberte **ano**. Bot reaguje se shrnutím svých akcí.
1. V protokolu emulátoru robota vyberte řádek, který obsahuje `Luis Trace`. Zobrazí odpověď JSON od služby LUIS pro záměr a entity utterance.

    [![Základní odpověď robota v emulátoru](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Další kroky

Podívejte se na další [ukázky](https://github.com/microsoft/botframework-solutions) s konverzačními roboty.

> [!div class="nextstepaction"]
> [Vytvoření aplikace language understanding s vlastní doménou předmětu](luis-quickstart-intents-only.md)