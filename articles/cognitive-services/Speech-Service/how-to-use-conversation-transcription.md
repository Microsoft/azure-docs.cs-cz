---
title: Přepisy konverzací v reálném čase (Preview) – služba Speech
titleSuffix: Azure Cognitive Services
description: Naučte se používat přepis konverzací v reálném čase s využitím sady Speech SDK. K dispozici pro jazyky C++, C# a Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: weixu
ms.custom: devx-track-csharp
ms.openlocfilehash: a425c75dfd57f0d5f9c98b73d8b351972b70703a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88918651"
---
# <a name="real-time-conversation-transcription-preview"></a>Přepis konverzace v reálném čase (Preview)

Rozhraní **ConversationTranscriber** API sady Speech SDK umožňuje přepisovat schůzky a další konverzace s možností přidávat, odebírat a identifikovat více účastníků streamování zvuku do služby Speech pomocí `PullStream` nebo `PushStream` . V tomto tématu se dozvíte, jak používat převod řeči na text pomocí sady Speech SDK (verze 1.8.0 nebo novější). Další informace najdete v tématu [co jsou hlasové služby](overview.md).

## <a name="limitations"></a>Omezení

- Rozhraní ConversationTranscriber API se podporuje pro jazyky C++, C# a Java v systémech Windows, Linux a Android.
- Aktuálně k dispozici v jazycích "en-US" a "zh-CN" v následujících oblastech: _centralus_ a _eastasia_.
- Vyžaduje pro datový proud s odkazem na přehrávání více než 7 mikrofon. Pole mikrofonu by mělo splňovat [naše specifikace](https://aka.ms/sdsdk-microphone).
- [Sada Speech Devices SDK](speech-devices-sdk.md) nabízí vhodná zařízení a ukázkovou aplikaci, která demonstruje přepis konverzace.

## <a name="optional-sample-code-resources"></a>Volitelné ukázkové prostředky kódu

Sada Speech SDK pro zařízení nabízí vzorový kód v jazyce Java pro záznam zvuku v reálném čase pomocí 8 kanálů.

- [Vzorový kód zařízení ROOBO](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/ConversationTranscription.java)
- [Vzorový kód pro Azure Kinect dev Kit](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)

## <a name="prerequisites"></a>Požadavky

Předplatné služby Speech. [Bezplatný účet Azure](https://azure.microsoft.com/free/cognitive-services/) si můžete vytvořit, pokud ho nemáte.

## <a name="create-voice-signatures"></a>Vytvoření podpisů hlasu

Prvním krokem je vytvoření hlasových podpisů účastníkům konverzace za účelem efektivní identifikace mluvčího.

### <a name="audio-input-requirements"></a>Požadavky na zvukové vstupy

- Vstupní zvukový soubor Wave pro vytváření podpisů hlasu by měl být v 16bitovém vzorcích, 16 kHz vzorkování a ve formátu jediného kanálu (mono).
- Doporučená délka každého zvukového vzorku je mezi třiceti sekund a dvě minuty.

### <a name="sample-code"></a>Ukázka kódu

Následující příklad ukazuje dva různé způsoby vytvoření hlasového podpisu [pomocí REST API](https://aka.ms/cts/signaturegenservice) v jazyce C#. Všimněte si, že budete muset nahradit skutečné informace o "YourSubscriptionKey", názvu souboru Wave pro "speakerVoice. wav" a vaší oblasti " `{region}` YourServiceRegion" (_centralus_ nebo _eastasia_).

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

## <a name="transcribe-conversations"></a>Konverzace přepisovat

Následující vzorový kód ukazuje, jak přepisovat konverzace v reálném čase pro tři reproduktory. Předpokládá se, že jste už vytvořili hlasový podpis pro každého mluvčího, jak je uvedeno výše. Při vytváření objektu SpeechConfig nahraďte skutečné informace o "YourSubscriptionKey" a "YourServiceRegion".

Příklady ukázek kódu zahrnují:

- Vytvoření `Conversation` objektu z `SpeechConfig` objektu pomocí identifikátoru schůzky generovaného pomocí `Guid.NewGuid()`
- Vytvoření `ConversationTranscriber` objektu a zapojení do konverzace s nástrojem `JoinConversationAsync()` ke spuštění přepisu
- Registrace událostí zájmu
- Přidání nebo odebrání účastníků konverzace pomocí objektu konverzace
- Streamování zvuku
- V sadě Speech SDK verze 1.9.0 a vyšší `int` `string` jsou podporovány i typy hodnot v poli verze hlasového podpisu.

Přepis a identifikátor mluvčího se vrátí do registrovaných událostí.

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
                using (var conversationTranscriber = new ConversationTranscriber(audioInput))
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
