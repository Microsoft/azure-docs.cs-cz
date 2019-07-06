---
title: Přepisy více účastníka konverzace se sadou SDK pro řeč – hlasové služby
titleSuffix: Azure Cognitive Services
description: Další informace o použití přepisu konverzace se sadou SDK pro řeč. K dispozici pro C++, C#a Java.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: jhakulin
ms.openlocfilehash: 215209a5b8e3ed46b25fbfa492c305785a9a0070
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606467"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>Přepisy více účastníka konverzace se sadou SDK pro řeč

Sada SDK řeči **ConversationTranscriber** API umožňuje přepisy schůzky/konverzace s možností přidat, odebrat a identifikovat účastníky pomocí vysílání datového proudu zvuku pro hlasové služby s využitím `PullStream` nebo `PushStream`.

## <a name="limitations"></a>Omezení

* Aplikace transcriber konverzace je podporováno pro C++, C#a Java na Windows, Linuxu a Android.
* ROOBO DevKit je podporovaný hardware prostředí pro vytváření přepisů konverzace, které efektivně poskytuje cyklické více mikrofon pole, které lze využít pro identifikaci mluvčího. [Další informace najdete v tématu sadou SDK pro řeč zařízení](speech-devices-sdk.md).
* Podpora SDK řeči pro přepis konverzace je omezena na použití zvuku o přijetí změn a push režim datové proudy s osmi kanály 16bitové 16 kHz PCM zvuku.
* Přepis konverzace je aktuálně k dispozici v jazycích "en US" a "zh-CN" v těchto oblastech: centralus a východní Asie.

## <a name="prerequisites"></a>Požadavky

* [Naučte se používat se sadou SDK pro řeč Speech to text.](quickstart-csharp-dotnet-windows.md)
* [Získejte zkušební verzi předplatného řeči.](https://azure.microsoft.com/try/cognitive-services/)
* Rozpoznávání řeči SDK verze 1.5.1 nebo novější je povinný.

## <a name="create-voice-signatures-for-participants"></a>Vytvoření hlasu podpisy pro účastníky

Prvním krokem je vytvoření hlasové podpisy pro účastníky konverzace. Vytvoření podpisy hlasu je požadované pro identifikaci mluvčího efektivní.

### <a name="requirements-for-input-wave-file"></a>Požadavky pro soubor vstupní wave

* Soubor vstupní zvuková pro vytváření podpisů hlasu musí být v 16bitové ukázky, 16 kHz vzorkovací frekvence a jeden kanál (Mono) formát.
* Doporučená délka každé zvukové ukázce je 30 sekund až dvě minuty.

Následující příklad ukazuje dva různé způsoby vytváření hlasové podpisu [pomocí rozhraní REST API](https://aka.ms/cts/signaturegenservice) z C#:

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribing-conversations"></a>Přepisování konverzace

Přepisy konverzace s více účastníky, vytvořte `ConversationTranscriber` objekt, který je přidružený `AudioConfig` objekt vytvořený pro relaci konverzace a zvuku pomocí datového proudu `PullAudioInputStream` nebo `PushAudioInputStream`.

Předpokládejme, že máte ConversationTranscriber třídu s názvem `MyConversationTranscriber`. Váš kód může vypadat takto:

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Conversation;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region.
        // Replace with your own subscription key and region.
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file.
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            // Creates a conversation transcriber using audio stream input.
            using (var transcriber = new ConversationTranscriber(config, audioInput))
            {
                // Subscribes to events.
                transcriber.Recognizing += (s, e) =>
                {
                    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                };

                transcriber.Recognized += (s, e) =>
                {
                    if (e.Result.Reason == ResultReason.RecognizedSpeech)
                    {
                        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                transcriber.Canceled += (s, e) =>
                {
                    Console.WriteLine($"CANCELED: Reason={e.Reason}");

                    if (e.Reason == CancellationReason.Error)
                    {
                        Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                        Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                        Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        stopTranscription.TrySetResult(0);
                    }
                };

                transcriber.SessionStarted += (s, e) =>
                {
                    Console.WriteLine("\nSession started event.");
                };

                transcriber.SessionStopped += (s, e) =>
                {
                    Console.WriteLine("\nSession stopped event.");
                    Console.WriteLine("\nStop recognition.");
                    stopTranscription.TrySetResult(0);
                };

                // Sets a conversation Id.
                transcriber.ConversationId = "AConversationFromTeams";

                // Add participants to the conversation.
                // Create voice signatures using REST API described in the earlier section in this document.
                // Voice signature needs to be in the following format:
                // { "Version": <Numeric value>, "Tag": "string", "Data": "string" }

                var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                transcriber.AddParticipant(speakerA);
                transcriber.AddParticipant(speakerB);
                transcriber.AddParticipant(speakerC);

                // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                await transcriber.StartTranscribingAsync().ConfigureAwait(false);

                // Waits for completion.
                // Use Task.WaitAny to keep the task rooted.
                Task.WaitAny(new[] { stopTranscription.Task });

                // Stop transcribing the conversation.
                await transcriber.StopTranscribingAsync().ConfigureAwait(false);

                // Ends the conversation.
                await transcriber.EndConversationAsync().ConfigureAwait(false);
            }
        }
    }
}
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte naše ukázky na Githubu](https://aka.ms/csspeech/samples)
