---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: dapine
ms.openlocfilehash: 36fb26fbc658e00c98f118d9b14347bd8392c8ae
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81275208"
---
## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

> [!div class="checklist"]
> * [Vytvoření prostředku řeči Azure](../../../../get-started.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="open-your-project-in-visual-studio"></a>Otevření projektu v sadě Visual Studio

Prvním krokem je ujistěte se, že máte projekt otevřený v sadě Visual Studio.

1. Spusťte Visual Studio 2019.
2. Načtěte projekt `Program.cs`a otevřete .
3. Stáhněte si <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike.wav <span class="docon docon-download x-hidden-focus"></span> </a> a přidejte jej do svého projektu.
    - Uložte soubor *Whatstheweatherlike.wav* `Program.cs` vedle souboru.
    - V **Průzkumníku řešení** klikněte pravým tlačítkem myši na projekt a vyberte **přidat > existující položku**.
    - Vyberte soubor *whatstheweatherlike.wav* a vyberte tlačítko **Přidat.**
    - Klikněte pravým tlačítkem myši na nově přidaný soubor a vyberte **příkaz Vlastnosti**.
    - Změňte **kopírovat do výstupního adresáře** na **kopírovat vždy**.

## <a name="start-with-some-boilerplate-code"></a>Začněte s nějakým standardním kódem

Přidáme nějaký kód, který funguje jako kostra pro náš projekt. Všimněte si, že jste vytvořili `RecognizeSpeechAsync()`asynchronní metodu s názvem .

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

Před inicializaci objektu `SpeechRecognizer` je třeba vytvořit konfiguraci, která používá klíč předplatného a oblast předplatného. Vložte tento `RecognizeSpeechAsync()` kód do metody.

> [!NOTE]
> Tato ukázka `FromSubscription()` používá metodu k sestavení `SpeechConfig`. Úplný seznam dostupných metod naleznete v tématu [SpeechConfig Class](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet).
> Sada Speech SDK bude ve výchozím nastavení rozpoznána použití jazyka en-us, viz [Určení zdrojového jazyka pro řeč na text,](../../../../how-to-specify-source-language.md) kde naleznete informace o výběru zdrojového jazyka.

```csharp
// Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="create-an-audio-configuration"></a>Vytvoření konfigurace zvuku

Nyní je třeba vytvořit `AudioConfig` objekt, který odkazuje na zvukový soubor. Tento objekt je vytvořen uvnitř using prohlášení k zajištění správné uvolnění nespravovaných prostředků. Vložte tento `RecognizeSpeechAsync()` kód do metody přímo pod konfigurací řeči.

```csharp
using (var audioInput = AudioConfig.FromWavFileInput("whatstheweatherlike.wav"))
{
}
```

## <a name="initialize-a-speechrecognizer"></a>Inicializovat rozpoznávání řeči

Nyní vytvoříme `SpeechRecognizer` objekt pomocí objektů `SpeechConfig` `AudioConfig` a vytvořených dříve. Tento objekt je také vytvořen uvnitř using prohlášení k zajištění správné uvolnění nespravovaných prostředků. Vložte tento `RecognizeSpeechAsync()` kód do metody, uvnitř using ```AudioConfig``` prohlášení, které obtéká objekt.

```csharp
using (var recognizer = new SpeechRecognizer(config, audioInput))
{
}
```

## <a name="recognize-a-phrase"></a>Rozpoznání fráze

Z `SpeechRecognizer` objektu zavoláte metodu. `RecognizeOnceAsync()` Tato metoda umožňuje řeči služby vědět, že odesíláte jednu frázi pro rozpoznávání a že jakmile je fráze identifikována k zastavení rozpoznávání řeči.

Uvnitř příkazu using přidejte tento kód:

```csharp
Console.WriteLine("Recognizing first result...");
var result = await recognizer.RecognizeOnceAsync();
```

## <a name="display-the-recognition-results-or-errors"></a>Zobrazení výsledků rozpoznávání (nebo chyb)

Když je výsledek rozpoznávání vrácen službou Řeč, budete s ním chtít něco udělat. Budeme to jednoduché a vytisknout výsledek na konzoli.

Uvnitř příkazu using `RecognizeOnceAsync()`níže přidejte tento kód:

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

## <a name="check-your-code"></a>Kontrola kódu

V tomto okamžiku by měl váš kód vypadat takto:

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

## <a name="build-and-run-your-app"></a>Vytvoření a spuštění aplikace

Teď jste připraveni vytvořit aplikaci a otestovat naše rozpoznávání řeči pomocí služby Řeč.

1. Kompilace kódu: Z panelu nabídek *sady Visual Studio*zvolte **Sestavení** > **sestavení řešení**.
2. Spuštění aplikace: Z řádku nabídek zvolte **Ladění** > **ladění startování** nebo stiskněte **klávesu F5**.
3. Rozpoznávání spuštění: Zvukový soubor je odeslán do služby Řeč, přepsán jako text a vykreslen v konzole.

   ```console
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
