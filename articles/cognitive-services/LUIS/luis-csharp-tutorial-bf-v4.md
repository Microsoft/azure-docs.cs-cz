---
title: Language Understanding Bot C# v4
titleSuffix: Language Understanding - Azure Cognitive Services
description: Pomocí jazyka C# vytvořte chatovacího robota integrovaného se službou Language Understanding (LUIS). Robot využívá rozhraní Bot Framework verze 4 a Azure Web app bot service.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: 8a03d87441f26d3116aff8af33fd94da0ef9a909
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67438430"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>Kurz: Použijte Web App Bot povolené službou Language Understanding vC#

Použití C# vytvářet chatovací robot integrovaná jazyka (LUIS). Robot je vytvořené pomocí Azure [Web app bot](https://docs.microsoft.com/azure/bot-service/) prostředků a [Bot Framework verze](https://github.com/Microsoft/botbuilder-dotnet) V4.

**V tomto kurzu se naučíte:**

> [!div class="checklist"]
> * Vytvořit Web App Bota. Tento proces za vás vytvoří novou aplikaci LUIS.
> * Stáhněte si projekt bot vytvořena webová služba bot
> * Spustit robota a emulátor místně na počítači
> * Zobrazit výsledky promluv v robotovi

## <a name="prerequisites"></a>Požadavky

* [Emulátor robota](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-a-web-app-bot-resource"></a>Vytvořit prostředek použijete web app bot

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
    |Plán App service/umístění|Neměňte z Zadaná výchozí hodnota.|
    |Application Insights|Neměňte z Zadaná výchozí hodnota.|
    |Microsoft App ID a heslo|Neměňte z Zadaná výchozí hodnota.|

1. V **Bot šablony**, vyberte následující a potom klepněte **vyberte** tlačítko v těchto nastavení:

    |Nastavení|Účel|Výběr|
    |--|--|--|
    |SDK version (Verze sady SDK)|Bot framework version (Verze Bot Frameworku)|**SDK v4**|
    |SDK language (Jazyk sady SDK)|Programovací jazyk robota|**C#**|
    |Bot|Typ robota|**Basic Bot** (Základní robot)|
    
1. Vyberte **Vytvořit**. Tím se vytvoří a nasadí služba robota do Azure. Část tohoto procesu vytvoří aplikaci LUIS nazvanou `luis-csharp-bot-XXXX`. Tento název je založen na názvu aplikace /Azure Bot Service.

    [![Vytvoření webové aplikace robota](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

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

    ![Snímek obrazovky webu Azure portal, zadejte text "hello".](./media/bfv4-csharp/ask-bot-question-in-portal-test-in-web-chat.png)

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

1. Otevřít **LuisHelper.cs** souboru. Tady se uživatelská promluva zadaná do robota odešle do služby LUIS. Odpověď služby luis se vrátí z metody jako **BookDetails** objektu. Když vytvoříte vlastní robota, by měl taky vytvořit vlastní objekt vrátit podrobnosti služby luis. 


    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;
    
    namespace Microsoft.BotBuilderSamples
    {
        public static class LuisHelper
        {
            public static async Task<BookingDetails> ExecuteLuisQuery(IConfiguration configuration, ILogger logger, ITurnContext turnContext, CancellationToken cancellationToken)
            {
                var bookingDetails = new BookingDetails();
    
                try
                {
                    // Create the LUIS settings from configuration.
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]
                    );
    
                    var recognizer = new LuisRecognizer(luisApplication);
    
                    // The actual call to LUIS
                    var recognizerResult = await recognizer.RecognizeAsync(turnContext, cancellationToken);
    
                    var (intent, score) = recognizerResult.GetTopScoringIntent();
                    if (intent == "Book_flight")
                    {
                        // We need to get the result from the LUIS JSON which at every level returns an array.
                        bookingDetails.Destination = recognizerResult.Entities["To"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
                        bookingDetails.Origin = recognizerResult.Entities["From"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
    
                        // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                        // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                        bookingDetails.TravelDate = recognizerResult.Entities["datetime"]?.FirstOrDefault()?["timex"]?.FirstOrDefault()?.ToString().Split('T')[0];
                    }
                }
                catch (Exception e)
                {
                    logger.LogWarning($"LUIS Exception: {e.Message} Check your LUIS configuration.");
                }
    
                return bookingDetails;
            }
        }
    }
    ```

1. Otevřít **BookingDetails.cs** Chcete-li zobrazit, jak objekt abstrahuje služby LUIS informace. 

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    namespace Microsoft.BotBuilderSamples
    {
        public class BookingDetails
        {
            public string Destination { get; set; }
    
            public string Origin { get; set; }
    
            public string TravelDate { get; set; }
        }
    }
    ```

1. Otevřít **dialogová okna -> BookingDialog.cs** pochopit, jak objekt BookingDetails slouží ke správě toku konverzace. Podrobnosti cesty se zobrazí výzva, v krocích, pak celý rezervace je potvrzen a nakonec opakován zpět tak, aby uživatel. 

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Recognizers.Text.DataTypes.TimexExpression;
    
    namespace Microsoft.BotBuilderSamples.Dialogs
    {
        public class BookingDialog : CancelAndHelpDialog
        {
            public BookingDialog()
                : base(nameof(BookingDialog))
            {
                AddDialog(new TextPrompt(nameof(TextPrompt)));
                AddDialog(new ConfirmPrompt(nameof(ConfirmPrompt)));
                AddDialog(new DateResolverDialog());
                AddDialog(new WaterfallDialog(nameof(WaterfallDialog), new WaterfallStep[]
                {
                    DestinationStepAsync,
                    OriginStepAsync,
                    TravelDateStepAsync,
                    ConfirmStepAsync,
                    FinalStepAsync,
                }));
    
                // The initial child Dialog to run.
                InitialDialogId = nameof(WaterfallDialog);
            }
    
            private async Task<DialogTurnResult> DestinationStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                if (bookingDetails.Destination == null)
                {
                    return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = MessageFactory.Text("Where would you like to travel to?") }, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.Destination, cancellationToken);
                }
            }
    
            private async Task<DialogTurnResult> OriginStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.Destination = (string)stepContext.Result;
    
                if (bookingDetails.Origin == null)
                {
                    return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = MessageFactory.Text("Where are you traveling from?") }, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.Origin, cancellationToken);
                }
            }
            private async Task<DialogTurnResult> TravelDateStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.Origin = (string)stepContext.Result;
    
                if (bookingDetails.TravelDate == null || IsAmbiguous(bookingDetails.TravelDate))
                {
                    return await stepContext.BeginDialogAsync(nameof(DateResolverDialog), bookingDetails.TravelDate, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.TravelDate, cancellationToken);
                }
            }
    
            private async Task<DialogTurnResult> ConfirmStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.TravelDate = (string)stepContext.Result;
    
                var msg = $"Please confirm, I have you traveling to: {bookingDetails.Destination} from: {bookingDetails.Origin} on: {bookingDetails.TravelDate}";
    
                return await stepContext.PromptAsync(nameof(ConfirmPrompt), new PromptOptions { Prompt = MessageFactory.Text(msg) }, cancellationToken);
            }
    
            private async Task<DialogTurnResult> FinalStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                if ((bool)stepContext.Result)
                {
                    var bookingDetails = (BookingDetails)stepContext.Options;
    
                    return await stepContext.EndDialogAsync(bookingDetails, cancellationToken);
                }
                else
                {
                    return await stepContext.EndDialogAsync(null, cancellationToken);
                }
            }
    
            private static bool IsAmbiguous(string timex)
            {
                var timexProperty = new TimexProperty(timex);
                return !timexProperty.Types.Contains(Constants.TimexTypes.Definite);
            }
        }
    }
    ```


## <a name="start-the-bot-code-in-visual-studio"></a>Spustit bot kódu v sadě Visual Studio

V sadě Visual Studio spusťte robota. Otevře se okno prohlížeče s webem Web App Bota na adrese `http://localhost:3978/`. Zobrazí se domovská stránka s informacemi o váš robot.

![Zobrazí se domovská stránka s informacemi o váš robot.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Použití emulátoru robota k otestování robota

1. Začněte Bot emulátoru a vyberte **otevřít Bot**.
1. V **otevřete robota** vyskakovací dialogové okno, zadejte adresu URL vašeho robota, jako například `http://localhost:3978/api/messages`. `/api/messages` Trasy je webová adresa pro robota.
1. Zadejte **ID aplikace Microsoft** a **heslo Microsoft App**byl nalezen v **appsettings.json** soubor v kořenové složce kódu bot jste si stáhli.

    Volitelně můžete vytvořit novým robotem, konfigurace a zkopírujte `appId` a `appPassword` z **appsettings.json** soubor v projektu sady Visual Studio pro robota. Název konfiguračního souboru robota by měl být stejný jako název robota. 

    ```json
    {
        "name": "<bot name>",
        "description": "<bot description>",
        "services": [
            {
                "type": "endpoint",
                "appId": "<appId from appsettings.json>",
                "appPassword": "<appPassword from appsettings.json>",
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

    [![Odpověď základního robota v emulátoru](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>Zeptejte se bot pro knihy letu záměr

1. V emulátoru bot rezervovat tak, že zadáte následující utterance let: 

    ```bot
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    Emulátor bot požádá o potvrzení. 

1. Vyberte **Ano**. Robot jako odpověď vrátí souhrn jeho akce. 
1. Z protokolu bot emulátor, vyberte řádek, který zahrnuje `Luis Trace`. Tím se zobrazí odpověď JSON na záměr a entity utterance služby luis.

    [![Odpověď základního robota v emulátoru](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Další postup

Další informace naleznete v [ukázky](https://github.com/microsoft/botframework-solutions) s konverzační robotů. 

> [!div class="nextstepaction"]
> [Vytvoření aplikace Language Understanding s doménou vlastního subjektu](luis-quickstart-intents-only.md)
