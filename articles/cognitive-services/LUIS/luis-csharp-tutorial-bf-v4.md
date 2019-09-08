---
title: 'Kurz: Language Understanding robot C# v4'
titleSuffix: Azure Cognitive Services
description: Pomocí jazyka C# vytvořte chatovacího robota integrovaného se službou Language Understanding (LUIS). Robot má vytvořenou verzi bot Framework verze 4 a službu Azure Web App bot.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: diberry
ms.openlocfilehash: 0911747da38ed736a79e692fd511e5bfbfaf7439
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772913"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>Kurz: Použití robota webové aplikace s povoleným Language Understanding vC#

Použijte C# k sestavení robota pro chat integrovaný s jazykem porozumění (Luis). Robot má vytvořenou službu Azure [Web App bot](https://docs.microsoft.com/azure/bot-service/) Resource a [bot Framework verze](https://github.com/Microsoft/botbuilder-dotnet) v4.

**V tomto kurzu se naučíte:**

> [!div class="checklist"]
> * Vytvořit Web App Bota. Tento proces za vás vytvoří novou aplikaci LUIS.
> * Stažení projektu robota vytvořeného službou Web bot Service
> * Spustit robota a emulátor místně na počítači
> * Zobrazit výsledky promluv v robotovi

## <a name="prerequisites"></a>Požadavky

* [Emulátor robota](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-a-web-app-bot-resource"></a>Vytvoření prostředku robota webové aplikace

1. Na portálu **Azure Portal** vyberte [Vytvořit nový prostředek](https://portal.azure.com).

1. Ve vyhledávací poli vyhledejte a vyberte **Web App Bot**. Vyberte **Vytvořit**.

1. V **Bot Service** (Služba robota) zadejte požadované informace:

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
    |Plán/umístění služby App Service|Neměňte z poskytnuté výchozí hodnoty.|
    |Application Insights|Neměňte z poskytnuté výchozí hodnoty.|
    |ID a heslo aplikace od Microsoftu|Neměňte z poskytnuté výchozí hodnoty.|

1. V **šabloně robota**vyberte následující a pak klikněte na tlačítko **Vybrat** pod těmito nastaveními:

    |Nastavení|Účel|Výběr|
    |--|--|--|
    |SDK version (Verze sady SDK)|Bot framework version (Verze Bot Frameworku)|**SDK v4**|
    |SDK language (Jazyk sady SDK)|Programovací jazyk robota|**C#**|
    |Dole|Typ robota|**Basic Bot** (Základní robot)|
    
1. Vyberte **Vytvořit**. Tím se vytvoří a nasadí služba robota do Azure. Část tohoto procesu vytvoří aplikaci LUIS nazvanou `luis-csharp-bot-XXXX`. Tento název vychází z názvu aplikace služby/Azure bot.

    [![Vytvoření robota webové aplikace](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

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
1. Do textového pole **text zprávy** zadejte text `Book a flight from Seattle to Berlin tomorrow`. Robot odpoví s ověřením, že chcete zarezervovat let. 

    ![Snímek obrazovky Azure Portal zadejte text Hello.](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    K rychlému testování robota můžete použít funkci test. Pro úplnější testování, včetně ladění, Stáhněte si kód robota a použijte Visual Studio. 

## <a name="download-the-web-app-bot-source-code"></a>Stažení zdrojového kódu robota webové aplikace
Pokud chcete vyvíjet kód Web App Bota, stáhněte si kód a použijte ho ve svém místním počítači. 

1. Na portálu Azure Portal vyberte **Build** (Sestavení) v části **Bot management** (Správa robota). 

1. Vyberte **Download Bot source code** (Stáhnout zdrojový kód robota). 

    [![Stažení zdrojového kódu robota webové aplikace pro základní bot](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Když se automaticky otevře okno s dotazem **zahrnout nastavení aplikace ve staženém souboru ZIP?** vyberte **Ano**.

1. Když se zdrojový kód zazipuje, zobrazí se zpráva s odkazem na stažení kódu. Vyberte odkaz. 

1. Uložte soubor zip do místního počítače a extrahujte soubory. Otevřete projekt v aplikaci Visual Studio. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Přečtěte si kód, který odešle utterance do LUIS a získá odpověď.

1. Chcete-li odeslat uživatele utterance do koncového bodu předpovědi LUIS, otevřete soubor **FlightBookingRecognizer.cs** . Tady se uživatelská promluva zadaná do robota odešle do služby LUIS. Odpověď z LUIS je vrácena z metody **RecognizeAsync** .  

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;
    
    namespace Microsoft.BotBuilderSamples
    {
        public class FlightBookingRecognizer : IRecognizer
        {
            private readonly LuisRecognizer _recognizer;
    
            public FlightBookingRecognizer(IConfiguration configuration)
            {
                var luisIsConfigured = !string.IsNullOrEmpty(configuration["LuisAppId"]) && !string.IsNullOrEmpty(configuration["LuisAPIKey"]) && !string.IsNullOrEmpty(configuration["LuisAPIHostName"]);
                if (luisIsConfigured)
                {
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]);
    
                    _recognizer = new LuisRecognizer(luisApplication);
                }
            }
    
            // Returns true if luis is configured in the appsettings.json and initialized.
            public virtual bool IsConfigured => _recognizer != null;
    
            public virtual async Task<RecognizerResult> RecognizeAsync(ITurnContext turnContext, CancellationToken cancellationToken)
                => await _recognizer.RecognizeAsync(turnContext, cancellationToken);
    
            public virtual async Task<T> RecognizeAsync<T>(ITurnContext turnContext, CancellationToken cancellationToken)
                where T : IRecognizerConvert, new()
                => await _recognizer.RecognizeAsync<T>(turnContext, cancellationToken);
        }
    }
    ````

1. Otevřít **dialogy-> MainDialog.cs** zachycuje utterance a odešle ji do executeLuisQuery v metodě actStep. 

    ```csharp
    public class MainDialog : ComponentDialog
    {
        private readonly FlightBookingRecognizer _luisRecognizer;

        ...

        public MainDialog(FlightBookingRecognizer luisRecognizer, BookingDialog bookingDialog, ILogger<MainDialog> logger)
                    : base(nameof(MainDialog))
        {
            _luisRecognizer = luisRecognizer;
            ...
        }

        private async Task<DialogTurnResult> ActStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
        {
            if (!_luisRecognizer.IsConfigured)
            {
                // LUIS is not configured, we just run the BookingDialog path with an empty BookingDetailsInstance.
                return await stepContext.BeginDialogAsync(nameof(BookingDialog), new BookingDetails(), cancellationToken);
            }

            // Call LUIS and gather any potential booking details. (Note the TurnContext has the response to the prompt.)
            var luisResult = await _luisRecognizer.RecognizeAsync<FlightBooking>(stepContext.Context, cancellationToken);
            switch (luisResult.TopIntent().intent)
            {
                case FlightBooking.Intent.BookFlight:
                    await ShowWarningForUnsupportedCities(stepContext.Context, luisResult, cancellationToken);

                    // Initialize BookingDetails with any entities we may have found in the response.
                    var bookingDetails = new BookingDetails()
                    {
                        // Get destination and origin from the composite entities arrays.
                        Destination = luisResult.ToEntities.Airport,
                        Origin = luisResult.FromEntities.Airport,
                        TravelDate = luisResult.TravelDate,
                    };

                    // Run the BookingDialog giving it whatever details we have from the LUIS call, it will fill out the remainder.
                    return await stepContext.BeginDialogAsync(nameof(BookingDialog), bookingDetails, cancellationToken);

                case FlightBooking.Intent.GetWeather:
                    // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                    var getWeatherMessageText = "TODO: get weather flow here";
                    var getWeatherMessage = MessageFactory.Text(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                    await stepContext.Context.SendActivityAsync(getWeatherMessage, cancellationToken);
                    break;

                default:
                    // Catch all for unhandled intents
                    var didntUnderstandMessageText = $"Sorry, I didn't get that. Please try asking in a different way (intent was {luisResult.TopIntent().intent})";
                    var didntUnderstandMessage = MessageFactory.Text(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
                    await stepContext.Context.SendActivityAsync(didntUnderstandMessage, cancellationToken);
                    break;
            }

            return await stepContext.NextAsync(null, cancellationToken);
        }
        
        ...

    }
    ```

## <a name="start-the-bot-code-in-visual-studio"></a>Spuštění kódu robota v aplikaci Visual Studio

V aplikaci Visual Studio 2019 začněte robot. Otevře se okno prohlížeče s webem Web App Bota na adrese `http://localhost:3978/`. Zobrazí se Domovská stránka s informacemi o robotovi.

![Zobrazí se Domovská stránka s informacemi o robotovi.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Použití emulátoru bot k otestování robota

1. Spusťte emulátor bot a vyberte **otevřít robot**.
1. V automaticky **otevřeném okně robota** zadejte adresu URL robota, například `http://localhost:3978/api/messages`. `/api/messages` Trasa je webová adresa pro robota.
1. Zadejte **ID aplikace Microsoftu** a **heslo aplikace Microsoftu**, které najdete v souboru **appSettings. JSON** v kořenovém adresáři kódu robota, který jste stáhli.


1. V emulátoru bot zadejte `Book a flight from Seattle to Berlin tomorrow` a získejte stejnou reakci na základní bot, jak jste dostali v **testu na webu chat**.

    [![Základní odpověď robota v emulátoru](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Vyberte **Ano**. Robot odpoví souhrnem jeho akcí. 
1. V protokolu emulátoru bot vyberte řádek, který obsahuje `Luis Trace`. Tím se zobrazí odpověď JSON z LUIS pro záměr a entity utterance.

    [![Základní odpověď robota v emulátoru](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]


## <a name="next-steps"></a>Další postup

Podívejte se na další [ukázky](https://github.com/microsoft/botframework-solutions) s konverzací roboty. 

> [!div class="nextstepaction"]
> [Vytvoření aplikace Language Understanding s vlastní doménou předmětu](luis-quickstart-intents-only.md)
