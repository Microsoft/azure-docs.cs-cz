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
ms.openlocfilehash: 433ea2778d99de39991565b2529e2f8eab4e13df
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506975"
---
# <a name="asynchronous-conversation-transcription-preview"></a>Přepis asynchronní konverzace (Náhled)

V tomto článku se v přepisu asynchronní konverzace ukazuje použití rozhraní **RemoteConversationTranscriptionClient** API. Pokud jste nakonfigurovali přepis konverzace na asynchronní přepis a máte `conversationId`, můžete získat přepis přidružený k tomuto `conversationId` pomocí rozhraní **RemoteConversationTranscriptionClient** API.

## <a name="asynchronous-vs-real-time--asynchronous"></a>Asynchronní vs. v reálném čase a asynchronní

Pomocí asynchronního přepisu zařadíte do proudu zvuk konverzace, ale v reálném čase nebudete vracet žádný přepis. Místo toho se po odeslání zvuku pomocí `conversationId` `ConversationTranscriber` dotazuje na stav asynchronního přepisu. Po přípravě asynchronního přepisu získáte `RemoteConversationTranscriptionResult`.

V reálném čase plus asynchronní získáte přepis v reálném čase, ale také získáte přepis pomocí `conversationId` (podobně jako asynchronní scénář).

K provedení asynchronního přepisu se vyžadují dva kroky. Prvním krokem je nahrání zvuku a výběr buď asynchronního, nebo plus v reálném čase. Druhým krokem je získání výsledků přepisu.

## <a name="upload-the-audio"></a>Nahrát zvuk

Než bude možné provést asynchronní přepis, je třeba odeslat zvuk do přepisu konverzace pomocí sady Microsoft vnímání řeči Client SDK (verze 1.8.0 nebo vyšší).

Tento příklad kódu ukazuje, jak vytvořit Transcriber konverzace pro režim pouze asynchronního režimu. Aby bylo možné streamovat zvuk do Transcriber, budete muset pro sadu Speech SDK přidat zvukový stream odvozený z [konverzací přepisovat v reálném čase](how-to-use-conversation-transcription-service.md). V části **omezení** tohoto tématu najdete podporované rozhraní API platforem a jazyků.

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speech_config.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// Do rest of the things as explained in how to use Conversation Transcription

// pick a conversation Id that is a GUID.
Conversation conversation = new Conversation(speechConfig, conversationId);

// Create a conversation transcriber
ConversationTranscriber transcriber = new ConversationTranscriber(AudioConfig.fromDefaultMicrophoneInput());

// join a conversation
transcriber.joinConversationAsync(conversation).get();

// stream audio as shown in “Transcribe conversations in real time”
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

Zde uvedený kód vyžaduje **remoteconversation-Client-SDK verze 1.0.0**, který je podporovaný jenom pro Java (1,8 nebo novější) v systémech Windows, Linux a Android (úroveň rozhraní API 26 nebo vyšší).

### <a name="obtaining-the-client-sdk"></a>Získání klientské sady SDK

**Remoteconversation-Client-SDK** můžete získat úpravou souboru pom. XML následujícím způsobem.

- Na konci souboru před uzavírací `</project>`značky vytvořte `repositories` element s odkazem na úložiště Maven pro sadu Speech SDK:

  ```xml
  <repositories>
    <repository>
      <id>maven-cognitiveservices-speech</id>
      <name>Microsoft Cognitive Services Speech Maven Repository</name>
      <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
    </repository>
  </repositories>
  ```

- Přidejte také prvek `dependencies` s remoteconversation-Client-SDK 1.0.0 jako závislost:

  ```xml
  <dependencies>
    <dependency>
      <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
      <artifactId>remoteconversation-client-sdk</artifactId>
      <version>1.0.0</version>
    </dependency>
  </dependencies>
  ```

- Uložit změny

### <a name="sample-transcription-code"></a>Ukázka přepisu kódu

Po `conversationId`vytvořte objekt vzdálené operace **RemoteConversationTranscriptionOperation** na klientovi, který se dotazuje na stav asynchronního přepisu. **RemoteConversationTranscriptionOperation** je rozšířeno od [hlasování](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/Poller.java). Jakmile se dotáže dokončí, Získejte **RemoteConversationTranscriptionResult** přihlášením k odběru a dotazování objektu. V tomto kódu jednoduše vypíšeme obsah výsledku do systémového výstupu.

```java
// Create the speech config object
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");

// Create a remote Conversation Transcription client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(speechConfig);

// Create a remote Conversation Transcription operation
RemoteConversationTranscriptionOperation operation = new RemoteConversationTranscriptionOperation(conversationId, client);

// Operation identifier now be the value of `conversationId`
System.out.println("Operation Identifier:" + operation.getId());

// Get the observer (which is a Flux) and subscribe to it for the response
operation.getObserver()
                .subscribe(
                        pollResponse -> {
                            System.out.println("Poll response status : " + pollResponse.getStatus());
                            if(pollResponse.getValue().getConversationTranscriptionResults() != null) {
                                for (int i = 0; i < pollResponse.getValue().getConversationTranscriptionResults().size(); i++) {
                                    ConversationTranscriptionResult result = pollResponse.getValue().getConversationTranscriptionResults().get(i);
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
                );
// Block on the operation till it is finished
operation.block();

System.out.println("Operation finished");
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte naše ukázky na GitHubu](https://aka.ms/csspeech/samples)
