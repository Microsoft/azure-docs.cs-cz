---
title: 'Úvodní příručka: Syntetizovat řeč do zvukového souboru, C# (.NET) - Služba řeči'
titleSuffix: Azure Cognitive Services
description: Bude doplněno
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 4ccc68b38d98c332435e252877d258c8591aab8a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "78924863"
---
## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

> [!div class="checklist"]
> * [Vytvoření řečového prostředku Azure](../../../../get-started.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>Otevření projektu v sadě Visual Studio

Prvním krokem je ujistěte se, že máte projekt otevřený v sadě Visual Studio.

1. Spusťte Visual Studio 2019.
2. Načtěte projekt `Program.cs`a otevřete .

## <a name="start-with-some-boilerplate-code"></a>Začněte s nějakým standardním kódem

Přidáme nějaký kód, který funguje jako kostra pro náš projekt. Všimněte si, že jste vytvořili `SynthesisToAudioFileAsync()`asynchronní metodu s názvem .

````C#

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task SynthesisToAudioFileAsync()
        {
        }

        static void Main()
        {
            SynthesisToAudioFileAsync().Wait();
        }
    }
}

````

## <a name="create-a-speech-configuration"></a>Vytvoření konfigurace řeči

Před inicializaci objektu `SpeechSynthesizer` je třeba vytvořit konfiguraci, která používá klíč předplatného a oblast předplatného. Vložte tento `SynthesisToAudioFileAsync()` kód do metody.

````C#
// Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
// The default language is "en-us".
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
````

## <a name="create-an-audio-configuration"></a>Vytvoření konfigurace zvuku

Nyní je třeba vytvořit ````AudioConfig```` objekt, který odkazuje na zvukový soubor. Tento objekt je vytvořen uvnitř using prohlášení k zajištění správné uvolnění nespravovaných prostředků. Vložte tento `SynthesisToAudioFileAsync()` kód do metody přímo pod konfigurací řeči.

````C#
var fileName = "helloworld.wav";
using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
{
}
````

## <a name="initialize-a-speechsynthesizer"></a>Inicializovat syntezátor řeči

Nyní vytvoříme `SpeechSynthesizer` objekt pomocí objektů `SpeechConfig` `AudioConfig` a vytvořených dříve. Tento objekt je také vytvořen uvnitř using prohlášení k zajištění správné uvolnění nespravovaných prostředků. Vložte tento `SynthesisToAudioFileAsync()` kód do metody, uvnitř using ````AudioConfig```` prohlášení, které obtéká objekt.

````C#
using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
{
}
````

## <a name="synthesize-text-using-speaktextasync"></a>Syntetizovat text pomocí aplikace SpeakTextAsync

Z `SpeechSynthesizer` objektu zavoláte metodu. `SpeakTextAsync()` Tato metoda odešle text do služby Speech, která jej převede na zvuk. Bude `SpeechSynthesizer` používat výchozí hlas, pokud `config.VoiceName` není explicitně zadán.

Uvnitř příkazu using přidejte tento kód:
````C#
var text = "Hello world!";
var result = await synthesizer.SpeakTextAsync(text);
````

## <a name="check-for-errors"></a>Kontrola chyb

Pokud je výsledek syntézy vrácen službou Řeč, měli byste zkontrolovat, zda byl váš text úspěšně syntetizován.

Uvnitř příkazu using `SpeakTextAsync()`níže přidejte tento kód:
````C#
if (result.Reason == ResultReason.SynthesizingAudioCompleted)
{
    Console.WriteLine($"Speech synthesized to [{fileName}] for text [{text}]");
}
else if (result.Reason == ResultReason.Canceled)
{
    var cancellation = SpeechSynthesisCancellationDetails.FromResult(result);
    Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

    if (cancellation.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails=[{cancellation.ErrorDetails}]");
        Console.WriteLine($"CANCELED: Did you update the subscription info?");
    }
}
````

## <a name="check-your-code"></a>Kontrola kódu

V tomto okamžiku by měl váš kód vypadat takto:

````C#
//
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE.md file in the project root for full license information.
//

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;

namespace helloworld
{
    class Program
    {
        public static async Task SynthesisToAudioFileAsync()
        {
            var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

            var fileName = "helloworld.wav";
            using (var fileOutput = AudioConfig.FromWavFileOutput(fileName))
            {
                using (var synthesizer = new SpeechSynthesizer(config, fileOutput))
                {
                    var text = "Hello world!";
                    var result = await synthesizer.SpeakTextAsync(text);

                    if (result.Reason == ResultReason.SynthesizingAudioCompleted)
                    {
                        Console.WriteLine($"Speech synthesized to [{fileName}] for text [{text}]");
                    }
                    else if (result.Reason == ResultReason.Canceled)
                    {
                        var cancellation = SpeechSynthesisCancellationDetails.FromResult(result);
                        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

                        if (cancellation.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails=[{cancellation.ErrorDetails}]");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        }
                    }
                }
            }
        }

        static void Main()
        {
            SynthesisToAudioFileAsync().Wait();
        }
    }
}
````

## <a name="build-and-run-your-app"></a>Vytvoření a spuštění aplikace

Teď jste připraveni vytvořit aplikaci a otestovat naši syntézu řeči pomocí služby Řeč.

1. **Kompilace kódu** – z panelu nabídek sady Visual Studio zvolte **Build** > **Build Build Solution**.
2. **Spuštění aplikace** – z řádku nabídek zvolte **Ladění** > **ladění startování** nebo stiskněte **klávesu F5**.
3. **Zahájit syntézu** – Text se převede na řeč a uloží se do zadaných zvukových dat.

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Viz také

- [Vytvoření vlastního hlasu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Záznam vlastních hlasových ukázek](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
