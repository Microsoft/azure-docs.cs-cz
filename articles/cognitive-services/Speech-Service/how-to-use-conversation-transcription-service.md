---
title: Přepisy více účastníka konverzace se sadou SDK pro řeč – hlasové služby
titleSuffix: Azure Cognitive Services
description: Další informace o použití služby určené k transkripci konverzace se sadou SDK pro řeč. K dispozici pro C++, C#a Java.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: jhakulin
ms.openlocfilehash: 73ab4cfa92a1efc49dea16ba2941cf16b7a1cf3e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025789"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>Přepisy více účastníka konverzace se sadou SDK pro řeč

Sada SDK řeči **ConversationTranscriber** API umožňuje přepisy schůzky/konverzace s možností přidat, odebrat a identifikovat účastníky pomocí vysílání datového proudu zvuku pro hlasové služby s využitím `PullStream` nebo `PushStream`.

## <a name="limitations"></a>Omezení

* Aplikace transcriber konverzace je podporováno pro C++, C#a Java na Windows, Linuxu a Android.
* ROOBO DevKit je podporovaný hardware prostředí pro vytváření konverzací jako, který obsahuje cyklický více mikrofon pole, které můžete používat efektivně službou konverzace přepis pro identifikaci mluvčího. [Další informace najdete v tématu sadou SDK pro řeč zařízení](speech-devices-sdk.md). 
* Podpora SDK řeči je omezena na využívání zvuku o přijetí změn a push režim datové proudy s osmi kanály PCM zvuku.

## <a name="prerequisites"></a>Požadavky

* [Naučte se používat se sadou SDK pro řeč Speech to text.](quickstart-csharp-dotnet-windows.md)
* [Získejte zkušební verzi předplatného řeči.](https://azure.microsoft.com/try/cognitive-services/)

## <a name="create-voice-signatures-for-participants"></a>Vytvoření hlasu podpisy pro účastníky

Prvním krokem je vytvoření hlasové podpisy pro účastníky konverzace. Vytvoření podpisy hlasu je požadované pro identifikaci mluvčího efektivní.
V následující ukázce jsme budete [pomocí rozhraní REST API k získání hlas podpis.](https://aka.ms/cts/signaturegenservice)

Následující příklad ukazuje dva různé způsoby vytváření podpisů hlasu:
```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://{region}.signature.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature can be extracted from the jsonData
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature can be extracted from the jsonData
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
    private static string endpoint = "YourOwnEndpoint";

    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region.
        // Replace with your own endpoint and subscription key.
        var config = SpeechConfig.FromEndpoint(new Uri(endpoint), "YourSubScriptionKey");
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
                        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}, SpeakerID={e.Result.SpeakerId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                // Sets a conversation Id.
                transcriber.ConversationId = "AConversationFromTeams";

                // Add participants to the conversation.
                // Create data for voice signatures using REST API described in the earlier section in this document.
                // How to create voice signatureA, signatureB & signatureC variables, please check the SDK API samples.

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
