---
title: 'Rychlý Start: Převod řeči na řeč C# (.NET Core Windows) – služba Speech'
titleSuffix: Azure Cognitive Services
description: Bude doplněno
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: b5aa8cbbdcaca912a25481475e14adcb2159ad94
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77156250"
---
## <a name="prerequisites"></a>Předpoklady

Než začnete, nezapomeňte:

> [!div class="checklist"]
> * [Vytvoření prostředku Azure Speech](../../../../get-started.md)
> * [Nastavení vývojového prostředí](../../../../quickstarts/setup-platform.md?tabs=dotnetcore)
> * [Vytvořit prázdný ukázkový projekt](../../../../quickstarts/create-project.md?tabs=dotnetcore)

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete soubor `Program.cs` a veškerý kód nahraďte následujícím kódem.

   ```Csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.CognitiveServices.Speech;
   using Microsoft.CognitiveServices.Speech.Translation;

   namespace helloworld
   {
       class Program
       {
           public static async Task TranslateSpeechToSpeech()
           {
               // Creates an instance of a speech translation config with specified subscription key and service region.
               // Replace with your own subscription key and service region (e.g., "westus", use the one of SpeechSDKParameters
               // from here: https://aka.ms/speech/sdkregion).   
               var config = SpeechTranslationConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

               // Sets source and target languages.
               // Replace with the languages of your choice, from list found here: https://aka.ms/speech/sttt-languages
               string fromLanguage = "en-US";
               string toLanguage = "de";
               config.SpeechRecognitionLanguage = fromLanguage;
               config.AddTargetLanguage(toLanguage);

               // Sets the synthesis output voice name.
               // Replace with the languages of your choice, from list found here: https://aka.ms/speech/tts-languages
               config.VoiceName = "de-DE-Hedda";

               // Creates a translation recognizer using the default microphone audio input device.
               using (var recognizer = new TranslationRecognizer(config))
               {
                   // Prepare to handle the synthesized audio data.
                   recognizer.Synthesizing += (s, e) =>
                   {
                       var audio = e.Result.GetAudio();
                       Console.WriteLine(audio.Length != 0
                           ? $"AUDIO SYNTHESIZED: {audio.Length} byte(s)"
                           : $"AUDIO SYNTHESIZED: {audio.Length} byte(s) (COMPLETE)");
                   };

                   // Starts translation, and returns after a single utterance is recognized. The end of a
                   // single utterance is determined by listening for silence at the end or until a maximum of 15
                   // seconds of audio is processed. The task returns the recognized text as well as the translation.
                   // Note: Since RecognizeOnceAsync() returns only a single utterance, it is suitable only for single
                   // shot recognition like command or query.
                   // For long-running multi-utterance recognition, use StartContinuousRecognitionAsync() instead.
                   Console.WriteLine("Say something...");
                   var result = await recognizer.RecognizeOnceAsync();

                   // Checks result.
                   if (result.Reason == ResultReason.TranslatedSpeech)
                   {
                       Console.WriteLine($"RECOGNIZED '{fromLanguage}': {result.Text}");
                       Console.WriteLine($"TRANSLATED into '{toLanguage}': {result.Translations[toLanguage]}");
                   }
                   else if (result.Reason == ResultReason.RecognizedSpeech)
                   {
                       Console.WriteLine($"RECOGNIZED '{fromLanguage}': {result.Text} (text could not be translated)");
                   }
                   else if (result.Reason == ResultReason.NoMatch)
                   {
                       Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                   }
                   else if (result.Reason == ResultReason.Canceled)
                   {
                       var cancellation = CancellationDetails.FromResult(result);
                       Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

                       if (cancellation.Reason == CancellationReason.Error)
                       {
                           Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                           Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
                           Console.WriteLine($"CANCELED: Did you update the subscription info?");
                       }
                   }
               }
           }

           static void Main(string[] args)
           {
               TranslateSpeechToSpeech().Wait();
           }
       }
   }
   ```

1. Ve stejném souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. Řetězec `YourServiceRegion` nahraďte [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. V řádku nabídek vyberte možnost **soubor** > **Uložit vše**.

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

1. V řádku nabídek vyberte **sestavit** > **Sestavit řešení** a sestavte aplikaci. Kód by se teď měl zkompilovat bez chyb.

1. Zvolte **ladění** > **Spustit ladění** (nebo stiskněte klávesu **F5**) a spusťte aplikaci **HelloWorld** .

1. Vyslovte anglickou frázi nebo větu. Aplikace přenáší váš hlas ke službě rozpoznávání řeči, která překládá a transcribes text (v tomto případě do němčiny). Služba Speech pak pošle syntetizované zvuky a text zpátky do aplikace k zobrazení.

````
Say something...
AUDIO SYNTHESIZED: 76784 byte(s)
AUDIO SYNTHESIZED: 0 byte(s)(COMPLETE)
RECOGNIZED 'en-US': What's the weather in Seattle?
TRANSLATED into 'de': Wie ist das Wetter in Seattle?
````

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]
