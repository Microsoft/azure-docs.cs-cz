---
title: Bot - C# -v4
titleSuffix: Language Understanding - Azure Cognitive Services
description: Pomocí jazyka C# vytvořte chatovacího robota integrovaného se službou Language Understanding (LUIS). Tento chatovací robot používá k rychlé implementaci řešení robota aplikaci Human Resources. K vytvoření robota se používá Bot Framework ve verzi 4 a Web App Bot Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: cjgronlund
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: 98c65b7adfe35c1ca80846ff7619dad69c5ba266
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219222"
---
# <a name="tutorial-luis-bot-in-c-with-the-bot-framework-4x-and-the-azure-web-app-bot"></a>Kurz: Služba LUIS robotů v C# pomocí rozhraní Bot Framework 4.x a použijete Azure Web app bot
Pomocí jazyka C# můžete vytvořit chatovacího robota integrovaného se službou Language Understanding (LUIS). Tento robot používá k implementaci řešení robota aplikaci HomeAutomation. K vytvoření robota se používá [Web App Bot](https://docs.microsoft.com/azure/bot-service/) Azure a [Bot Framework ve verzi](https://github.com/Microsoft/botbuilder-js) v4.

**V tomto kurzu se naučíte:**

> [!div class="checklist"]
> * Vytvořit Web App Bota. Tento proces za vás vytvoří novou aplikaci LUIS.
> * Přidat předem připravenou doménu do nového modelu LUIS
> * Stáhnout si projekt vytvořený službou webového robota
> * Spustit robota a emulátor místně na počítači
> * Upravit kód robota pro nové záměry LUIS
> * Zobrazit výsledky promluv v robotovi

## <a name="prerequisites"></a>Požadavky

* [Emulátor robota](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-web-app-bot"></a>Vytvoření Web App Bota

1. Na portálu **Azure Portal** vyberte [Vytvořit nový prostředek](https://portal.azure.com).

2. Ve vyhledávací poli vyhledejte a vyberte **Web App Bot**. Vyberte **Vytvořit**.

3. V **Bot Service** (Služba robota) zadejte požadované informace:

    |Nastavení|Účel|Navrhované nastavení|
    |--|--|--|
    |Bot name (Název robota)|Název prostředku|`luis-csharp-bot-` + `<your-name>`, například `luis-csharp-bot-johnsmith`|
    |Předplatné|Předplatné, pod kterým se má robot vytvořit|Vaše primární předplatné
    |Skupina prostředků|Logická skupina prostředků Azure|Vytvořte novou skupinu k uložení všech prostředků používaných s tímto robotem a skupinu pojmenujte `luis-csharp-bot-resource-group`.|
    |Umístění|Oblast Azure – nemusí být stejná jako oblast LUIS pro vytváření nebo publikování.|`westus`|
    |Cenová úroveň|Slouží pro limity žádostí o služby a fakturaci.|`F0` je bezplatná úroveň.
    |App name (Název aplikace)|Název se používá jako subdoména, když je váš robot nasazený do cloudu (například humanresourcesbot.azurewebsites.net).|`luis-csharp-bot-` + `<your-name>`, například `luis-csharp-bot-johnsmith`|
    |Bot template (Šablona robota)|Nastavení Bot Frameworku – viz následující tabulka|
    |LUIS App location (Umístění aplikace LUIS)|Musí být stejné jako oblast prostředků LUIS|`westus`|

4. V **nastaveních šablony robota** vyberte následující možnosti a pak v těchto nastaveních zvolte tlačítko **Vybrat**:

    |Nastavení|Účel|Výběr|
    |--|--|--|
    |SDK version (Verze sady SDK)|Bot framework version (Verze Bot Frameworku)|**SDK v4**|
    |SDK language (Jazyk sady SDK)|Programovací jazyk robota|**C#**|
    |Echo/Basic Bot (Robot Echo/Základní robot)|Typ robota|**Basic Bot** (Základní robot)|
    
5. Vyberte **Vytvořit**. Tím se vytvoří a nasadí služba robota do Azure. Část tohoto procesu vytvoří aplikaci LUIS nazvanou `luis-csharp-bot-XXXX`. Tento název je založený na názvu robota a aplikace v předchozí části.

    [ ![Vytvoření Web App Bota](./media/bfv4-csharp/create-web-app-service.png) ](./media/bfv4-csharp/create-web-app-service.png#lightbox)

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
2. Na stránce **My Apps** (Moje aplikace) vyberte sloupec **Created date** (Datum vytvoření) k seřazení podle data, kdy byla aplikace vytvořena. Služba Azure Bot Service vytvořila novou aplikaci v předchozí části. Její název je `luis-csharp-bot-` + `<your-name>` + 4 náhodné znaky.
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

6. Otevřete soubor bot.cs a najděte `_services.LuisServices`. Tady se uživatelská promluva zadaná do robota odešle do služby LUIS.

    ```csharp
    /// <summary>
    /// Run every turn of the conversation. Handles orchestration of messages.
    /// </summary>
    /// <param name="turnContext">Bot Turn Context.</param>
    /// <param name="cancellationToken">Task CancellationToken.</param>
    /// <returns>A <see cref="Task"/> representing the asynchronous operation.</returns>
    public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    {
        var activity = turnContext.Activity;

        if (activity.Type == ActivityTypes.Message)
        {
            // Perform a call to LUIS to retrieve results for the current activity message.
            var luisResults = await _services.LuisServices[LuisConfiguration].RecognizeAsync(turnContext, cancellationToken).ConfigureAwait(false);

            // If any entities were updated, treat as interruption.
            // For example, "no my name is tony" will manifest as an update of the name to be "tony".
            var topScoringIntent = luisResults?.GetTopScoringIntent();

            var topIntent = topScoringIntent.Value.intent;
            switch (topIntent)
            {
                case GreetingIntent:
                    await turnContext.SendActivityAsync("Hello.");
                    break;
                case HelpIntent:
                    await turnContext.SendActivityAsync("Let me try to provide some help.");
                    await turnContext.SendActivityAsync("I understand greetings, being asked for help, or being asked to cancel what I am doing.");
                    break;
                case CancelIntent:
                    await turnContext.SendActivityAsync("I have nothing to cancel.");
                    break;
                case NoneIntent:
                default:
                    // Help or no intent identified, either way, let's provide some help.
                    // to the user
                    await turnContext.SendActivityAsync("I didn't understand what you just said to me.");
                    break;
            }
        }
        else if (activity.Type == ActivityTypes.ConversationUpdate)
        {
            if (activity.MembersAdded.Any())
            {
                // Iterate over all new members added to the conversation.
                foreach (var member in activity.MembersAdded)
                {
                    // Greet anyone that was not the target (recipient) of this message.
                    // To learn more about Adaptive Cards, see https://aka.ms/msbot-adaptivecards for more details.
                    if (member.Id != activity.Recipient.Id)
                    {
                        var welcomeCard = CreateAdaptiveCardAttachment();
                        var response = CreateResponse(activity, welcomeCard);
                        await turnContext.SendActivityAsync(response).ConfigureAwait(false);
                    }
                }
            }
        }

    }
    ```

    Robot odešle uživatelovu promluvu do služby LUIS a obdrží výsledky. Hlavní záměr určuje tok konverzace. 


## <a name="start-the-bot"></a>Spuštění robota
Před změnou kódu nebo nastavení zkontrolujte, jestli robot funguje. 

1. V sadě Visual Studio otevřete soubor řešení. 

2. Vytvořte soubor `appsettings.json` pro proměnné, které kód robota hledá:

    ```JSON
    {
    "botFileSecret": "",
    "botFilePath": ""

    }
    ```

    Nastavte hodnoty proměnných na hodnoty, které jste zkopírovali z Application Settings (Nastavení aplikace) služby Azure Bot Service v 1. kroku v části **[Stažení Web App Bota](#download-the-web-app-bot)**.

3. V sadě Visual Studio spusťte robota. Otevře se okno prohlížeče s webem Web App Bota na adrese `http://localhost:3978/`.

## <a name="start-the-emulator"></a>Spuštění emulátoru

1. Spusťte emulátor robota.

2. V emulátoru robota vyberte soubor *.bot v kořenovém adresáři projektu. Tento soubor `.bot` obsahuje koncový bod adresy URL robota pro zprávy:

    [ ![Emulátor robota v4](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png) ](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png#lightbox)

3. Zadejte tajný kód robota, který jste zkopírovali z Application Settings (Nastavení aplikace) služby Azure Bot Service v 1. kroku v části **[Stažení Web App Bota](#download-the-web-app-bot)**. Emulátoru to umožní přístup k zašifrovaným polím v souboru `.bot`.

    ![Tajný kód emulátoru robota v4](../../../includes/media/cognitive-services-luis/bfv4/bot-secret.png)

4. V emulátoru robota zadejte `Hello` a získejte správnou odpověď pro základního robota.

    [ ![Odpověď základního robota v emulátoru](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png) ](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png#lightbox)

## <a name="modify-bot-code"></a>Úprava kódu robota 

Do souboru `BasicBot.cs` přidejte kód pro zpracování nových záměrů. 

1. V horní části souboru najděte část **Supported LUIS Intents** (Podporované záměry LUIS) a přidejte konstanty pro záměry HomeAutomation:

    ```csharp
    // Supported LUIS Intents
    public const string GreetingIntent = "Greeting";
    public const string CancelIntent = "Cancel";
    public const string HelpIntent = "Help";
    public const string NoneIntent = "None";
    public const string TurnOnIntent = "HomeAutomation_TurnOn"; // new intent
    public const string TurnOffIntent = "HomeAutomation_TurnOff"; // new intent
    ```

    Všimněte si, že tečka, `.`, mezi doménou a záměrem z aplikace portálu LUIS se nahradí podtržítkem, `_`. 

2. Najděte metodu **OnTurnAsync**, která přijímá předpověď LUIS promluvy. Přidejte kód do příkazu switch k vrácení odpovědi služby LUIS pro dva záměry HomeAutomation. 

    ```csharp
    case TurnOnIntent:
        await turnContext.SendActivityAsync("TurnOn intent found, JSON response: " + luisResults?.Entities.ToString());
        break;
    case TurnOffIntent:
        await turnContext.SendActivityAsync("TurnOff intent found, JSON response: " + luisResults?.Entities.ToString());
        break;
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



## <a name="view-results-in-bot"></a>Zobrazení výsledků v robotovi

1. V emulátoru robota zadejte promluvu: `Turn on the livingroom lights to 50%`

2. Robot odpoví takto:

    ```JSON
    TurnOn intent found, JSON response: {"$instance":{“HomeAutomation_Device”:[{“startIndex”:23,“endIndex”:29,“score”:0.9776345,“text”:“lights”,“type”:“HomeAutomation.Device”}],“HomeAutomation_Room”:[{“startIndex”:12,“endIndex”:22,“score”:0.9079433,“text”:“livingroom”,“type”:“HomeAutomation.Room”}]},“HomeAutomation_Device”:[“lights”],“HomeAutomation_Room”:[“livingroom”]}
    ```    

## <a name="learn-more-about-bot-framework"></a>Další informace o Bot Frameworku
Azure Bot Service využívá sadu SDK Bot Frameworku. Přečtěte si další informace o sadě SDK a Bot Frameworku:

* Dokumentace pro [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0) v4
* [Ukázky Bot Builderu](https://github.com/Microsoft/botbuilder-samples)
* [Sada SK Bot Builderu](https://docs.microsoft.com/javascript/api/botbuilder-core/?view=botbuilder-ts-latest)
* [Nástroje Bot Builderu](https://github.com/Microsoft/botbuilder-tools):

## <a name="next-steps"></a>Další postup

Vytvořili jste službu Azure Bot Service, zkopírovali jste tajný klíč robota a cestu k souboru `.bot` a stáhli jste soubor ZIP kódu. Přidali jste předem připravenou doménu HomeAutomation do aplikace LUIS vytvořené v rámci nové služby Azure Bot Service a pak aplikaci vyškolili a znovu publikovali. Extrahovali jste projekt kódu, vytvořili soubor prostředí (`.env`) a nastavili tajný kód robota a cestu k souboru `.bot`. Do souboru bot.js jste přidali kód pro zpracování dvou nových záměrů. Pak jste robota otestovali v emulátoru robota, abyste viděli odpověď služby LUIS pro promluvu jednoho z nových záměrů. 

Další informace naleznete v [ukázky](https://github.com/Microsoft/AI) s konverzační robotů. 

> [!div class="nextstepaction"]
> [Vytvoření vlastní domény v LUIS](luis-quickstart-intents-only.md)
