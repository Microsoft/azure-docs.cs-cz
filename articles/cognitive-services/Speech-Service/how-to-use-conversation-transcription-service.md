---
title: Přepisovat konverzace s více účastníky pomocí sady Speech SDK – Speech Service
titleSuffix: Azure Cognitive Services
description: Naučte se používat přepis konverzace pomocí sady Speech SDK. K C++dispozici C#pro, a Java.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: jhakulin
ms.openlocfilehash: 5ad912b1cee5495e18a5eb2da4d981eadd74dd7d
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066436"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>Přepisovat konverzace s více účastníky pomocí sady Speech SDK

Rozhraní **ConversationTranscriber** API sady Speech SDK umožňuje přepisovat schůzky a konverzace s možností přidávat, odebírat a identifikovat účastníky streamování zvuku do služeb řeči pomocí `PullStream` nebo. `PushStream`

## <a name="limitations"></a>Omezení

* Konverzace Transcriber je podporována pro C++systémy C#, a Java v systémech Windows, Linux a Android.
* ROOBO DevKit je podporované hardwarové prostředí pro vytváření přepisů konverzací, protože poskytuje kruhové pole s více mikrofony, které je možné efektivně využít pro identifikaci mluvčího. [Další informace najdete v tématu sada Speech Devices SDK](speech-devices-sdk.md).
* Podpora sady Speech SDK pro přepisy konverzací je omezená na proudy pro vyžádání obsahu a streamy nabízených oznámení s 8 kanály 16bitového zvuku se 16 kHz PCM. V současné době se pro zvukové zachycení 8 kanálů podporují jenom tyto sady:
   * [ROOBO Smart audio s kulatým 7 – MIC DK](https://ddk.roobo.com/)
   * [Azure Kinect DK](https://azure.microsoft.com/en-in/services/kinect-dk/).
* Přepis konverzace je aktuálně k dispozici v jazycích "en-US" a "zh-CN" v následujících oblastech: centralus a eastasia.

## <a name="prerequisites"></a>Požadavky

* [Naučte se používat převod řeči na text pomocí sady Speech SDK.](quickstart-csharp-dotnet-windows.md)
* [Získejte zkušební verzi předplatného pro rozpoznávání řeči.](https://azure.microsoft.com/try/cognitive-services/)
* Je vyžadována sada Speech SDK verze 1.5.1 nebo novější.

## <a name="create-voice-signatures-for-participants"></a>Vytvoření hlasových podpisů pro účastníky

Prvním krokem je vytvoření hlasových podpisů pro účastníky konverzace. Pro efektivní identifikaci mluvčího se vyžadují vytváření podpisů hlasu.

### <a name="requirements-for-input-wave-file"></a>Požadavky na vstupní soubor Wave

* Vstupní zvukový soubor Wave pro vytváření podpisů hlasu musí být v 16bitovém vzorcích, 16 kHz vzorkování a ve formátu jediného kanálu (mono).
* Doporučená délka každého zvukového vzorku je mezi 30 sekundami a dvěma minutami.

Následující příklad ukazuje dva různé způsoby, jak vytvořit podpis hlasu [pomocí REST API](https://aka.ms/cts/signaturegenservice) z C#:

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

## <a name="transcribing-conversations"></a>Konverzace zdlouhavého přepisování

Pokud chcete přepisovat konverzace s více účastníky, vytvořte `ConversationTranscriber` objekt přidružený `AudioConfig` k objektu vytvořenému pro relaci konverzace a zvuk streamu pomocí `PullAudioInputStream` nebo `PushAudioInputStream`.

Pojďme předpokládat, že máte ConversationTranscriber třídu s názvem `MyConversationTranscriber`. Váš kód může vypadat takto:

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
> [Prozkoumejte naše ukázky na GitHubu](https://aka.ms/csspeech/samples)
