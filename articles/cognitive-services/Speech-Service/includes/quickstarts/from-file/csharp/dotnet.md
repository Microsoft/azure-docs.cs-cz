---
title: 'Rychlý Start: rozpoznávání řeči ze zvukového souboru C# , (.NET) – služba Speech'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: dapine
ms.openlocfilehash: edb6846974dc58af04ba800c0aa32e3ecc5d4b9a
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2020
ms.locfileid: "76308996"
---
## <a name="prerequisites"></a>Požadavky

Než začnete, nezapomeňte:

> [!div class="checklist"]
> * [Vytvoření prostředku Azure Speech](../../../../get-started.md)
> * [Nastavení vývojového prostředí](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [Vytvořit prázdný ukázkový projekt](../../../../quickstarts/create-project.md?tabs=vs)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="open-your-project-in-visual-studio"></a>Otevřete projekt v sadě Visual Studio

Prvním krokem je ujistit se, že máte projekt otevřený v aplikaci Visual Studio.

1. Spusťte Visual Studio 2019.
2. Načtěte projekt a otevřete `Program.cs`.
3. Stáhněte si soubor <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike. <span class="docon docon-download x-hidden-focus"></span> WAV</a> a přidejte ho do projektu.
    - Uložte soubor *whatstheweatherlike. wav* vedle souboru `Program.cs`.
    - V **Průzkumník řešení** klikněte pravým tlačítkem myši na projekt a vyberte **Přidat > existující položku**.
    - Vyberte soubor *whatstheweatherlike. wav* a pak vyberte tlačítko **Přidat** .
    - Klikněte pravým tlačítkem na nově přidaný soubor a vyberte **vlastnosti**.
    - Změňte **složku kopírovat na výstupní adresář** na **Kopírovat vždy**.

## <a name="start-with-some-boilerplate-code"></a>Začínáme s některým často používaným kódem

Pojďme přidat kód, který funguje jako kostra pro náš projekt. Nezapomeňte, že jste vytvořili asynchronní metodu nazvanou `RecognizeSpeechAsync()`.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

namespace HelloWorld
{
    class Program
    {
        static async Task Main()
        {
            await RecognizeSpeechAsync();
        }

        static async Task RecognizeSpeechAsync()
        {
        }
    }
}
```

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Než budete moct inicializovat objekt `SpeechRecognizer`, musíte vytvořit konfiguraci, která používá váš klíč předplatného a oblast předplatného. Vložte tento kód do metody `RecognizeSpeechAsync()`.

> [!NOTE]
> Tato ukázka používá metodu `FromSubscription()` k sestavení `SpeechConfig`. Úplný seznam dostupných metod naleznete v tématu [Třída SpeechConfig](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).
> Sada Speech SDK bude standardně rozpoznána pomocí en-US pro daný jazyk. informace o výběru zdrojového jazyka najdete v tématu [určení zdrojového jazyka pro převod řeči na text](../../../../how-to-specify-source-language.md) .

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="create-an-audio-configuration"></a>Vytvoření konfigurace zvuku

Nyní je třeba vytvořit objekt `AudioConfig`, který odkazuje na váš zvukový soubor. Tento objekt je vytvořen v rámci příkazu Using, aby bylo zajištěno správné vydání nespravovaných prostředků. Vložte tento kód do metody `RecognizeSpeechAsync()` hned pod konfigurací řeči.

```csharp
using (var audioInput = AudioConfig.FromWavFileInput("whatstheweatherlike.wav"))
{
}
```

## <a name="initialize-a-speechrecognizer"></a>Inicializovat SpeechRecognizer

Nyní vytvoříme objekt `SpeechRecognizer` pomocí dříve vytvořených objektů `SpeechConfig` a `AudioConfig`. Tento objekt je vytvořen také v rámci příkazu Using, aby bylo zajištěno správné vydání nespravovaných prostředků. Vložte tento kód do metody `RecognizeSpeechAsync()` uvnitř příkazu Using, který zabalí váš objekt ```AudioConfig```.

```csharp
using (var recognizer = new SpeechRecognizer(config, audioInput))
{
}
```

## <a name="recognize-a-phrase"></a>Rozpoznání fráze

Z objektu `SpeechRecognizer` zavoláte metodu `RecognizeOnceAsync()`. Tato metoda umožňuje službě rozpoznávání řeči zjistit, že posíláte jednoduchou frázi pro rozpoznávání, a že po identifikaci fráze zastavit rozpoznávání řeči.

V příkazu Using přidejte tento kód:

```csharp
Console.WriteLine("Recognizing first result...");
var result = await recognizer.RecognizeOnceAsync();
```

## <a name="display-the-recognition-results-or-errors"></a>Zobrazit výsledky rozpoznávání (nebo chyby)

Když Služba rozpoznávání řeči vrátí výsledek rozpoznávání, budete s ním chtít něco dělat. My to Zjednodušme a vytiskneme výsledek do konzoly.

V příkazu Using níže `RecognizeOnceAsync()`přidejte tento kód:

```csharp
switch (result.Reason)
{
    case ResultReason.RecognizedSpeech:
        Console.WriteLine($"We recognized: {result.Text}");
        break;
    case ResultReason.NoMatch:
        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        var cancellation = CancellationDetails.FromResult(result);
        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

        if (cancellation.Reason == CancellationReason.Error)
        {
            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
            Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
            Console.WriteLine($"CANCELED: Did you update the subscription info?");
        }
        break;
}
```

## <a name="check-your-code"></a>Kontrolovat kód

V tomto okamžiku váš kód by měl vypadat takto:

```csharp
//
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE.md file in the project root for full license information.
//

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

namespace HelloWorld
{
    class Program
    {
        static async Task Main()
        {
            await RecognizeSpeechAsync();
        }

        static async Task RecognizeSpeechAsync()
        {
            var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

            using (var audioInput = AudioConfig.FromWavFileInput("whatstheweatherlike.wav"))
            using (var recognizer = new SpeechRecognizer(config, audioInput))
            {
                Console.WriteLine("Recognizing first result...");
                var result = await recognizer.RecognizeOnceAsync();

                switch (result.Reason)
                {
                    case ResultReason.RecognizedSpeech:
                        Console.WriteLine($"We recognized: {result.Text}");
                        break;
                    case ResultReason.NoMatch:
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        break;
                    case ResultReason.Canceled:
                        var cancellation = CancellationDetails.FromResult(result);
                        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");
                
                        if (cancellation.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        }
                        break;
                }
            }
        }
    }
}
```

## <a name="build-and-run-your-app"></a>Sestavení a spuštění aplikace

Nyní jste připraveni sestavit aplikaci a otestovat rozpoznávání řeči pomocí služby Speech.

1. Zkompilujte kód: z panelu nabídek aplikace *Visual Studio*vyberte **sestavení** **řešení**Build > .
2. Spusťte aplikaci: z panelu nabídek zvolte **ladění** > **Spustit ladění** nebo stiskněte klávesu **F5**.
3. Spustit rozpoznávání: váš zvukový soubor se pošle službě Speech, přepisu jako text a vykresluje se v konzole.

   ```console
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]
