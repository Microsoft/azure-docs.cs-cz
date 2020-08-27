---
title: 'Rychlý Start: Převod řeči na text, C# (.NET Framework Windows) – služba Speech'
titleSuffix: Azure Cognitive Services
description: Bude doplněno
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 16ca6a1402c1d79ef38b7a6d96ea9f213aeae970
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88925610"
---
## <a name="prerequisites"></a>Předpoklady

Než začnete, nezapomeňte:

> [!div class="checklist"]
> * [Vytvoření prostředku Azure Speech](../../../../get-started.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp)

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete **program.cs**a v něm nahraďte veškerý kód následujícím kódem.

   ```Csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.CognitiveServices.Speech;
   using Microsoft.CognitiveServices.Speech.Translation;

   namespace helloworld
   {
       class Program
       {
           public static async Task TranslateSpeechToText()
           {
               // Creates an instance of a speech translation config with specified subscription key and service region.
               // Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
               var config = SpeechTranslationConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

               // Sets source and target languages.
               // Replace with the languages of your choice, from list found here: https://aka.ms/speech/sttt-languages
               string fromLanguage = "en-US";
               string toLanguage = "de";
               config.SpeechRecognitionLanguage = fromLanguage;
               config.AddTargetLanguage(toLanguage);

               // Creates a translation recognizer using the default microphone audio input device.
               using (var recognizer = new TranslationRecognizer(config))
               {
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
               TranslateSpeechToText().Wait();
           }
       }
   }
   ```

1. Ve stejném souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. Nahraďte řetězec `YourServiceRegion` [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) , která je přidružená k vašemu předplatnému.

1. V řádku nabídek vyberte **soubor**  >  **Uložit vše**.

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

1. V řádku nabídek vyberte **sestavit**  >  **sestavení řešení** a sestavte aplikaci. Kód by se teď měl zkompilovat bez chyb.

1. Zvolte **ladění**  >  **Spustit ladění** (nebo stiskněte klávesu **F5**) a spusťte aplikaci **HelloWorld** .

1. Vyslovte anglickou frázi nebo větu. Aplikace přenáší váš hlas ke službě rozpoznávání řeči, která překládá a transcribes text (v tomto případě do němčiny). Služba rozpoznávání řeči pak pošle text zpátky do aplikace k zobrazení.

````
Say something...
RECOGNIZED 'en-US': What's the weather in Seattle?
TRANSLATED into 'de': Wie ist das Wetter in Seattle?
````

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]
