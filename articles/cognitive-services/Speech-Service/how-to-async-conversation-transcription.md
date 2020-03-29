---
title: Přepis asynchronní konverzace (preview) – služba řeči
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak používat přepis asynchronní konverzace pomocí služby Řeč. K dispozici pouze pro Jazyk Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: c1f0110c83eb42aaedbd36736946ae3faff58699
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80366611"
---
# <a name="asynchronous-conversation-transcription-preview"></a>Přepis asynchronní konverzace (náhled)

V tomto článku je přepis asynchronní konverzace prokázáno pomocí **rozhraní API vzdálenékonverzacetranskripce klienta.** Pokud jste nakonfigurovali přepis konverzace k asynchronnímu `conversationId`přepisu a mají , můžete `conversationId` získat přepis přidružený k tomupomocí rozhraní API **klienta RemoteConversationTranscriptionClient.**

## <a name="asynchronous-vs-real-time--asynchronous"></a>Asynchronní vs. real-time + asynchronní

S asynchronním přepisem streamujete zvuk konverzace, ale nepotřebujete přepis vrácený v reálném čase. Místo toho po odeslání zvuku `conversationId` použijte `Conversation` of k dotazování na stav asynchronního přepisu. Když je asynchronní přepis připraven, získáte `RemoteConversationTranscriptionResult`.

S real-time plus asynchronní, získáte přepis v reálném čase, ale také získat přepis `conversationId` dotazováním s (podobně jako asynchronní scénář).

K provedení asynchronního transkripce jsou nutné dva kroky. Prvním krokem je nahrát zvuk, výběr buď asynchronní pouze nebo v reálném čase plus asynchronní. Druhým krokem je získat výsledky přepisu.

## <a name="upload-the-audio"></a>Nahrát zvuk

Před provedením asynchronního přepisu je třeba odeslat zvuk do služby přepisu konverzace pomocí sady Microsoft Cognitive Speech client SDK (verze 1.8.0 nebo vyšší).

Tento ukázkový kód ukazuje, jak vytvořit přepiskonverzace pro režim pouze pro asynchronní. Chcete-li streamovat zvuk do přepisovače, budete muset přidat kód streamování zvuku odvozený z [přepisování konverzací v reálném čase pomocí sady Speech SDK](how-to-use-conversation-transcription-service.md). Naleznete v části **Omezení** v tomto tématu zobrazíte podporovaná platformy a jazyky API.

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speechConfig.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// pick a conversation Id that is a GUID.
String conversationId = UUID.randomUUID().toString();

// Create a Conversation
Future<Conversation> conversationFuture = Conversation.createConversationAsync(speechConfig, conversationId);
Conversation conversation = conversationFuture.get();

// Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
// Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16kHz16Bits8channelsOfRecordedPCMAudio.wav");
// Create an audio stream format assuming the file used above is 16kHz, 16 bits and 8 channel pcm wav file
AudioStreamFormat audioStreamFormat = AudioStreamFormat.getWaveFormatPCM((long)16000, (short)16,(short)8);
// Create an input stream
AudioInputStream audioStream = AudioInputStream.createPullStream(wavfilePullStreamCallback, audioStreamFormat);

// Create a conversation transcriber
ConversationTranscriber transcriber = new ConversationTranscriber(AudioConfig.fromStreamInput(audioStream));

// join a conversation
transcriber.joinConversationAsync(conversation);

// Add the event listener for the realtime events
transcriber.transcribed.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber Recognized:" + e.toString());
});

transcriber.canceled.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber canceled:" + e.toString());
    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

transcriber.sessionStopped.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber stopped:" + e.toString());

    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

// start the transcription.
Future<?> future = transcriber.startTranscribingAsync();
...
```

Pokud chcete v reálném čase _plus_ asynchronní, komentář a odkomentovat příslušné řádky kódu takto:

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>Získání výsledků přepisu

Tento krok získá výsledky asynchronního přepisu, ale předpokládá, že jakékoli zpracování v reálném čase, které jste mohli vyžadovat, se provádí jinde. Další informace naleznete [v tématu Přepiskonverzace v reálném čase s sadou Speech SDK](how-to-use-conversation-transcription-service.md).

Pro kód zde uvedený potřebujete **vzdálenou konverzaci verze 1.8.0**, podporovanou pouze pro Javu (1.8.0 nebo vyšší) ve Windows, Linuxu a Androidu (pouze úroveň rozhraní API 26 nebo vyšší).

### <a name="obtaining-the-client-sdk"></a>Získání sady SDK klienta

**Vzdálenou konverzaci** můžete získat takto úpravou souboru pom.xml.

1. Na konci souboru před uzavírací `</project>`značkou `repositories` vytvořte prvek s odkazem na úložiště Maven pro sadu Speech SDK:

   ```xml
   <repositories>
     <repository>
       <id>maven-cognitiveservices-speech</id>
       <name>Microsoft Cognitive Services Speech Maven Repository</name>
       <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
     </repository>
   </repositories>
   ```

2. Také přidat `dependencies` prvek, s remoteconversation-client sdk 1.8.0 jako závislost:

   ```xml
   <dependencies>
     <dependency>
       <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
       <artifactId>remote-conversation</artifactId>
       <version>1.8.0</version>
     </dependency>
   </dependencies>
   ```

3. Uložení změn

### <a name="sample-transcription-code"></a>Ukázkový kód přepisu

Po , `conversationId`vytvořte vzdálené konverzace přepis **klienta RemoteConversationTranscriptionClient** v klientské aplikaci dotaz na stav asynchronního přepisu. Použijte **metodu getTranscriptionOperation** v **remoteconversationtranscriptionClient** k získání objektu [PollerFlux.](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/PollerFlux.java) Objekt PollerFlux bude mít informace o stavu vzdáleného provozu **RemoteConversationTranscriptionOperation** a konečném výsledku **RemoteConversationTranscriptionResult**. Po dokončení operace, **získejte RemoteConversationTranscriptionResult** voláním **getFinalResult** na [SyncPoller](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/SyncPoller.java). V tomto kódu jednoduše vytiskneme obsah výsledků na výstup systému.

```java
// Create the speech config object
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");

// Create a remote Conversation Transcription client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(speechConfig);

// Get the PollerFlux for the remote operation
PollerFlux<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> remoteTranscriptionOperation = client.getTranscriptionOperation(conversationId);

// Subscribe to PollerFlux to get the remote operation status
remoteTranscriptionOperation.subscribe(
        pollResponse -> {
            System.out.println("Poll response status : " + pollResponse.getStatus());
            System.out.println("Poll response status : " + pollResponse.getValue().getServiceStatus());
        }
);

// Obtain the blocking operation using getSyncPoller
SyncPoller<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> blockingOperation =  remoteTranscriptionOperation.getSyncPoller();

// Wait for the operation to finish
blockingOperation.waitForCompletion();

// Get the final result response
RemoteConversationTranscriptionResult resultResponse = blockingOperation.getFinalResult();

// Print the result
if(resultResponse != null) {
    if(resultResponse.getConversationTranscriptionResults() != null) {
        for (int i = 0; i < resultResponse.getConversationTranscriptionResults().size(); i++) {
            ConversationTranscriptionResult result = resultResponse.getConversationTranscriptionResults().get(i);
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult.name()));
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult));
            System.out.println(result.getOffset());
            System.out.println(result.getDuration());
            System.out.println(result.getUserId());
            System.out.println(result.getReason());
            System.out.println(result.getResultId());
            System.out.println(result.getText());
            System.out.println(result.toString());
        }
    }
}

System.out.println("Operation finished");
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prohlédněte si naše ukázky na GitHubu](https://aka.ms/csspeech/samples)
