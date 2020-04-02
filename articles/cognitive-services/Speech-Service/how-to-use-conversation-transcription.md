---
title: Přepis konverzace v reálném čase (náhled) - služba řeči
titleSuffix: Azure Cognitive Services
description: Naučte se používat přepis konverzace v reálném čase s sadou Speech SDK. K dispozici pro C++, C# a Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: weixu
ms.openlocfilehash: e2c9c0aadc8e443f07f60f3ccddb4a1b6dd661b1
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520913"
---
# <a name="real-time-conversation-transcription-preview"></a>Přepis konverzace v reálném čase (náhled)

Rozhraní **CONVERSATIONTranscriber** rozhraní Api sady Speech SDK umožňuje přepsat schůzky a další konverzace s možností přidat, odebrat `PullStream` a `PushStream`identifikovat více účastníků streamováním zvuku do služby Řeči pomocí nebo . Toto téma vyžaduje, abyste věděli, jak používat řeč na text s sadou Speech SDK (verze 1.8.0 nebo novější). Další informace naleznete v tématu [Co jsou služby řeči](overview.md).

## <a name="limitations"></a>Omezení

- Rozhraní CONVERSATIONTranscriber API je podporováno pro C++, C# a Java ve Windows, Linuxu a Androidu.
- V současné době k dispozici v jazycích "en US" a "zh-CN" v následujících oblastech: _centralus_ a _eastasia_.
- Vyžaduje 7-mic kruhové vícemikrofonní pole s datovým proudem odkazu přehrávání. Mikrofonní pole by mělo splňovat [naše specifikace](https://aka.ms/sdsdk-microphone).
- Sada [SDK pro řečová zařízení](speech-devices-sdk.md) poskytuje vhodná zařízení a ukázkovou aplikaci demonstrující přepis konverzace.

## <a name="optional-sample-code-resources"></a>Volitelné ukázkové zdroje kódu

Sada Speech Device SDK poskytuje ukázkový kód v jazyce Java pro snímání zvuku v reálném čase pomocí 8 kanálů.

- [Ukázkový kód zařízení ROOBO](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/ConversationTranscription.java)
- [Ukázkový kód sady Azure Kinect Dev Kit](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)

## <a name="prerequisites"></a>Požadavky

Předplatné služby Řeči. Pokud předplatné zkušební verze řeči nemáte, můžete si ho [získat.](https://azure.microsoft.com/try/cognitive-services/)

## <a name="create-voice-signatures"></a>Vytváření hlasových podpisů

Prvním krokem je vytvoření hlasových podpisů pro účastníky konverzace pro efektivní identifikaci reproduktoru.

### <a name="audio-input-requirements"></a>Požadavky na vstup zvuku

- Vstupní soubor zvukových vln pro vytváření hlasových podpisů by měl být v 16bitových vzorcích, vzorkovací frekvenci 16 kHz a ve formátu jednoho kanálu (mono).
- Doporučená délka pro každý zvukový vzorek je mezi třiceti sekundami a dvěma minutami.

### <a name="sample-code"></a>Ukázka kódu

Následující příklad ukazuje dva různé způsoby vytvoření [hlasového podpisu pomocí rozhraní REST API](https://aka.ms/cts/signaturegenservice) v jazyce C#. Všimněte si, že budete muset nahradit skutečné informace o "YourSubscriptionKey", název souboru wave `{region}` pro "speakerVoice.wav", a váš region pro a "YourServiceRegion"_(centralus_ nebo _eastasia_).

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribe-conversations"></a>Přepis konverzace

Následující ukázkový kód ukazuje, jak přepsat konverzace v reálném čase pro tři reproduktory. Předpokládá, že jste již vytvořili hlasové podpisy pro každého řečníka, jak je uvedeno výše. Nahradit skutečné informace o "YourSubscriptionKey" a "YourServiceRegion" při vytváření SpeechConfig objektu.

Ukázkový kód zdůrazňuje patří:

- Vytvoření `Conversation` objektu `SpeechConfig` z objektu pomocí identifikátoru schůzky generovaného pomocí`Guid.NewGuid()`
- Vytvoření `ConversationTranscriber` objektu a připojení `JoinConversationAsync()` konverzace s chcete-li zahájit přepis
- Registrace zajímavých událostí
- Přidání nebo odebrání účastníků konverzace pomocí objektu Konverzace
- Streamování zvuku
- V řeči SDK verze 1.9.0 `int` a `string` dále oba typy hodnot jsou podporovány v poli verze hlasového podpisu.

Přepis a identifikátor mluvčího se vrátí v registrovaných událostech.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Transcription;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region
        // Replace with your own subscription key and region
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        config.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            var meetingId = Guid.NewGuid().ToString();
            using (var conversation = await Conversation.CreateConversationAsync(config, meetingId).ConfigureAwait(false))
            {
                // Create a conversation transcriber using audio stream input
                using (var conversationTranscriber = new ConversationTranscriber    (audioInput))
                {
                    await conversationTranscriber.JoinConversationAsync(conversation);

                    // Subscribe to events
                    conversationTranscriber.Transcribing += (s, e) =>
                    {
                            Console.WriteLine($"TRANSCRIBING: Text={e.Result.Text}");
                    };

                    conversationTranscriber.Transcribed += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"TRANSCRIBED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    conversationTranscriber.Canceled += (s, e) =>
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

                    conversationTranscriber.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\nSession started event.");
                    };

                    conversationTranscriber.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\nSession stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopTranscription.TrySetResult(0);
                    };

                    // Add participants to the conversation.
                    // Create voice signatures using REST API described in the earlier section in this document.
                    // Voice signature needs to be in the following format:
                    // { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }

                    var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                    var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                    var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                    await conversation.AddParticipantAsync(speakerA);
                    await conversation.AddParticipantAsync(speakerB);
                    await conversation.AddParticipantAsync(speakerC);

                    // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                    await conversationTranscriber.StartTranscribingAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopTranscription.Task });

                    // Stop transcribing the conversation.
                    await conversationTranscriber.StopTranscribingAsync().ConfigureAwait(false);
                 }
            }
       }
    }
}
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přepis asynchronní konverzace](how-to-async-conversation-transcription.md)
