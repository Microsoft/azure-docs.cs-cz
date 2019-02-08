---
title: 'Rychlý start: Překlad řeči, C# (Windows rozhraní .NET Framework) – hlasové služby'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vytvoříte jednoduchou aplikaci rozhraní .NET Framework zaznamenat uživatelské řeči, přeloží ji do jiného jazyka a panelem text do příkazového řádku. Tato příručka je určená pro uživatele Windows.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: f39012b02174cd5a14032efccdc1977579b7ff70
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859993"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-net-framework"></a>Rychlý start: Překlad řeči se sadou SDK pro řeč pro rozhraní .NET Framework

V tomto rychlém startu vytvoříte jednoduchou aplikaci rozhraní .NET Framework, která zachycuje uživatele řeči z vašeho počítače mikrofonu, přeloží řeč a transcribes přeložený text do příkazového řádku v reálném čase. Tato aplikace je navržen pro spouštění na Windows 64-bit a využívá rozhraní [balíček NuGet sady SDK pro řeč](https://aka.ms/csspeech/nuget) a Microsoft Visual Studio 2017.

Úplný seznam jazyků, které jsou k dispozici pro překlad řeči, naleznete v tématu [jazykovou podporu](language-support.md).

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Klíč předplatného pro službu rozpoznávání řeči. [Získat zdarma](get-started.md).

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

[!INCLUDE [Create project ](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete soubor `Program.cs` a veškerý kód nahraďte následujícím kódem.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Translation;

    namespace helloworld
    {
        class Program
        {
            public static async Task TranslationContinuousRecognitionAsync()
            {
                // Creates an instance of a speech translation config with specified subscription key and service region.
                // Replace with your own subscription key and service region (e.g., "westus").
                var config = SpeechTranslationConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

                // Sets source and target languages.
                string fromLanguage = "en-US";
                config.SpeechRecognitionLanguage = fromLanguage;
                config.AddTargetLanguage("de");

                // Sets voice name of synthesis output.
                const string GermanVoice = "de-DE-Hedda";
                config.VoiceName = GermanVoice;
                // Creates a translation recognizer using microphone as audio input.
                using (var recognizer = new TranslationRecognizer(config))
                {
                    // Subscribes to events.
                    recognizer.Recognizing += (s, e) =>
                    {
                        Console.WriteLine($"RECOGNIZING in '{fromLanguage}': Text={e.Result.Text}");
                        foreach (var element in e.Result.Translations)
                        {
                            Console.WriteLine($"    TRANSLATING into '{element.Key}': {element.Value}");
                        }
                    };

                    recognizer.Recognized += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.TranslatedSpeech)
                        {
                            Console.WriteLine($"\nFinal result: Reason: {e.Result.Reason.ToString()}, recognized text in {fromLanguage}: {e.Result.Text}.");
                            foreach (var element in e.Result.Translations)
                            {
                                Console.WriteLine($"    TRANSLATING into '{element.Key}': {element.Value}");
                            }
                        }
                    };

                    recognizer.Synthesizing += (s, e) =>
                    {
                        var audio = e.Result.GetAudio();
                        Console.WriteLine(audio.Length != 0
                            ? $"AudioSize: {audio.Length}"
                            : $"AudioSize: {audio.Length} (end of synthesis data)");
                    };

                    recognizer.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"\nRecognition canceled. Reason: {e.Reason}; ErrorDetails: {e.ErrorDetails}");
                    };

                    recognizer.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\nSession started event.");
                    };

                    recognizer.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\nSession stopped event.");
                    };

                    // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
                    Console.WriteLine("Say something...");
                    await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

                    do
                    {
                        Console.WriteLine("Press Enter to stop");
                    } while (Console.ReadKey().Key != ConsoleKey.Enter);

                    // Stops continuous recognition.
                    await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
                }
            }

            static void Main(string[] args)
            {
                TranslationContinuousRecognitionAsync().Wait();
            }
        }
    }
    ```

1. Ve stejném souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. Také řetězec `YourServiceRegion` nahraďte [oblastí](regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. Uložte změny do projektu.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. Sestavte aplikaci. V řádku nabídek zvolte **Sestavení** > **Sestavit řešení**. Kód by se měl zkompilovat bez chyb.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Sestavit řešení](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Úspěšné sestavení")

1. Spusťte aplikaci. V řádku nabídek zvolte **Ladit** > **Spustit ladění** nebo stiskněte klávesu **F5**.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Spustit ladění](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Spuštění aplikace s laděním")

1. Zobrazí se okno konzoly a vyzve vás, abyste něco řekli. Vyslovte anglickou frázi nebo větu. Hlasu je do služby řeči, přeložit a převede na text, který se zobrazí ve stejném okně.

    ![Snímek obrazovky výstup na konzole po úspěšné překlad](media/sdk/qs-translate-csharp-dotnetcore-windows-output.png "výstupu konzoly po úspěšné překladu")

## <a name="next-steps"></a>Další postup

Další ukázky, jako je čtení řeči ze zvukových souboru a výstup přeložený text jako řečového, jsou k dispozici na Githubu.

> [!div class="nextstepaction"]
> [Prozkoumejte C# ukázky na Githubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Další informace najdete v tématech

- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)
