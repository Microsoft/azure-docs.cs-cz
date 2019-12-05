---
title: Přepis asynchronních konverzací (Preview) – služba Speech
titleSuffix: Azure Cognitive Services
description: Naučte se používat přepis asynchronní konverzace pomocí služby Speech. K dispozici pouze pro Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: f34ce66d126a47e186cd1196fb5c92d670def445
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806163"
---
# <a name="asynchronous-conversation-transcription-preview"></a>Přepis asynchronní konverzace (Náhled)

V tomto článku se v přepisu asynchronní konverzace ukazuje použití rozhraní **RemoteConversationTranscriptionClient** API. Pokud jste nakonfigurovali přepis konverzace na asynchronní přepis a máte `conversationId`, můžete získat přepis přidružený k tomuto `conversationId` pomocí rozhraní **RemoteConversationTranscriptionClient** API.

## <a name="asynchronous-vs-real-time--asynchronous"></a>Asynchronní vs. v reálném čase a asynchronní

Pomocí asynchronního přepisu zařadíte do proudu zvuk konverzace, ale v reálném čase nebudete vracet žádný přepis. Místo toho se po odeslání zvuku pomocí `conversationId` `Conversation` dotazuje na stav asynchronního přepisu. Po přípravě asynchronního přepisu získáte `RemoteConversationTranscriptionResult`.

V reálném čase plus asynchronní získáte přepis v reálném čase, ale také získáte přepis pomocí `conversationId` (podobně jako asynchronní scénář).

K provedení asynchronního přepisu se vyžadují dva kroky. Prvním krokem je nahrání zvuku a výběr buď asynchronního, nebo plus v reálném čase. Druhým krokem je získání výsledků přepisu.

## <a name="upload-the-audio"></a>Nahrát zvuk

Před provedením asynchronního přepisu je třeba odeslat zvuk do služby přepisu konverzace pomocí sady Microsoft rozpoznávání řeči Client SDK (verze 1.8.0 nebo vyšší).

Tento příklad kódu ukazuje, jak vytvořit Transcriber konverzace pro režim pouze asynchronního režimu. Aby bylo možné streamovat zvuk do Transcriber, budete muset pro sadu Speech SDK přidat zvukový stream odvozený z [konverzací přepisovat v reálném čase](how-to-use-conversation-transcription-service.md). V části **omezení** tohoto tématu najdete podporované rozhraní API platforem a jazyků.

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
Conversation conversation = new Conversation(speechConfig, conversationId);

// Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
// Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16Khz16Bits8channelsOfRecordedPCMAudio.wav");
// Create an audio stream format assuming the file used above is 16Khz, 16 bits and 8 channel pcm wav file
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

Pokud chcete v reálném čase _plus_ asynchronní, komentovat a odkomentovat příslušné řádky kódu následujícím způsobem:

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>Získat výsledky přepisu

Tento krok získá asynchronní výsledky přepisu, ale předpokládá, že se vyžaduje zpracování v reálném čase, které můžete potřebovat jinde. Další informace najdete v tématu [konverzace přepisovat v reálném čase se sadou Speech SDK](how-to-use-conversation-transcription-service.md).

Zde uvedený kód vyžaduje **vzdálenou konverzaci verze 1.8.0**, která je podporovaná jenom pro Java (1.8.0 nebo vyšší) v systémech Windows, Linux a Android (jenom úroveň rozhraní API 26 nebo vyšší).

### <a name="obtaining-the-client-sdk"></a>Získání klientské sady SDK

**Vzdálenou konverzaci** můžete získat úpravou souboru pom. XML následujícím způsobem.

1. Na konci souboru před uzavírací `</project>`značky vytvořte `repositories` element s odkazem na úložiště Maven pro sadu Speech SDK:

   ```xml
   <repositories>
     <repository>
       <id>maven-cognitiveservices-speech</id>
       <name>Microsoft Cognitive Services Speech Maven Repository</name>
       <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
     </repository>
   </repositories>
   ```

2. Přidejte také prvek `dependencies` s remoteconversation-Client-SDK 1.8.0 jako závislost:

   ```xml
   <dependencies>
     <dependency>
       <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
       <artifactId>remote-conversation</artifactId>
       <version>1.8.0</version>
     </dependency>
   </dependencies>
   ```

3. Uložit změny

### <a name="sample-transcription-code"></a>Ukázka přepisu kódu

Po dokončení `conversationId`vytvořte vzdálenou konverzaci s přepisem klienta **RemoteConversationTranscriptionClient** v klientské aplikaci, aby se dotazoval na stav asynchronního přepisu. K získání objektu [PollerFlux](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/PollerFlux.java) použijte metodu **getTranscriptionOperation** v **RemoteConversationTranscriptionClient** . Objekt PollerFlux bude obsahovat informace o stavu vzdálené operace **RemoteConversationTranscriptionOperation** a konečném výsledku **RemoteConversationTranscriptionResult**. Po dokončení operace Získejte **RemoteConversationTranscriptionResult** voláním **getFinalResult** na [SyncPoller](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/SyncPoller.java). V tomto kódu jednoduše vypíšeme obsah výsledku do systémového výstupu.

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
> [Prozkoumejte naše ukázky na GitHubu](https://aka.ms/csspeech/samples)
