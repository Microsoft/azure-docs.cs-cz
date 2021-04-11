---
title: zahrnout soubor
description: zahrnout soubor
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: afb8a6f7d18a36503253da3666a1325eef228651
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105958222"
---
## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/) verze 8 nebo vyšší.
- [Apache Maven](https://maven.apache.org/download.cgi)
- Nasazený prostředek komunikačních služeb a připojovací řetězec. [Vytvořte prostředek služby Communications](../../create-communication-resource.md).
- [Přístupový token uživatele](../../access-tokens.md). Ujistěte se, že jste nastavili obor na "chat" a poznamenali jste řetězec tokenu a také řetězec userId.


## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-java-application"></a>Vytvoření nové aplikace Java

Otevřete okno terminálu nebo příkaz a přejděte do adresáře, kam chcete vytvořit aplikaci Java. Spuštěním následujícího příkazu vygenerujte projekt Java ze šablony Maven-Archetype-Starter.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Všimněte si, že cíl "Generate" vytvořil adresář se stejným názvem, jako má artifactId. V tomto adresáři `src/main/java directory` obsahuje zdrojový kód projektu, `src/test/java` adresář obsahuje zdroj testu a soubor pom.xml je projektový objektový model projektu nebo [pom](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html).

Aktualizujte soubor POM vaší aplikace tak, aby používal jazyk Java 8 nebo novější:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>
```

### <a name="add-the-package-references-for-the-chat-sdk"></a>Přidat odkazy na balíček pro sadu Chat SDK

V souboru POM se na balíček odkazuje `azure-communication-chat` pomocí rozhraní API pro chat:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-chat</artifactId>
    <version>1.0.0</version>
</dependency>
```

Pro ověřování musí klient odkazovat na `azure-communication-common` balíček:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-common</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce služby Azure Communications Chat SDK pro jazyk Java.

| Název                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Tato třída je potřebná pro funkci chatu. Vytvoří se jeho instance s informacemi o předplatném a použije se k vytváření, získávání a odstraňování vláken. |
| ChatAsyncClient | Tato třída je potřebná pro funkci asynchronního chatu. Vytvoří se jeho instance s informacemi o předplatném a použije se k vytváření, získávání a odstraňování vláken. |
| ChatThreadClient | Tato třída je potřebná pro funkci konverzačního vlákna. Získáte instanci prostřednictvím ChatClient a použijete ji k posílání, přijímání, aktualizaci a odstraňování zpráv, přidávání, odebírání a získávání uživatelů, posílání oznámení a čtení. |
| ChatThreadAsyncClient | Tato třída je potřebná pro funkci asynchronního zřetězení chatu. Získáte instanci prostřednictvím ChatAsyncClient a použijete ji k posílání, přijímání, aktualizaci a odstraňování zpráv, přidávání, odebírání a získávání uživatelů, posílání oznámení a čtení. |

## <a name="create-a-chat-client"></a>Vytvoření chatového klienta
Chcete-li vytvořit chatovacího klienta, použijte koncový bod komunikační služby a přístupový token, který byl vygenerován jako součást požadavků. Tokeny přístupu uživatele umožňují vytvářet klientské aplikace, které se přímo ověřují na komunikačních službách Azure. Po vygenerování těchto tokenů na serveru je předejte zpátky do klientského zařízení. Je nutné použít třídu CommunicationTokenCredential ze společné sady SDK k předání tokenu klientovi chatu.

Další informace o [architektuře chatu](../../../concepts/chat/concepts.md)

Při přidávání příkazů importu nezapomeňte přidat pouze importy z modelu COM. Azure. Communications. chat a com. Azure. Communications. chat. Models Namespaces, a ne z oboru názvů com. Azure. Communication. chat. Implements. V souboru App. Java, který byl vygenerován prostřednictvím Maven, můžete použít následující kód, který začíná na:

```Java
package com.communication.quickstart;

import com.azure.communication.chat.*;
import com.azure.communication.chat.models.*;
import com.azure.communication.common.*;
import com.azure.core.http.HttpClient;
import com.azure.core.http.netty.NettyAsyncHttpClientBuilder;
import com.azure.core.http.rest.PagedIterable;

import java.io.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Chat Quickstart");

        // Your unique Azure Communication service endpoint
        String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

        // Create an HttpClient builder of your choice and customize it
        // Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
        NettyAsyncHttpClientBuilder yourHttpClientBuilder = new NettyAsyncHttpClientBuilder();
        HttpClient httpClient = yourHttpClientBuilder.build();

        // User access token fetched from your trusted service
        String userAccessToken = "<USER_ACCESS_TOKEN>";

        // Create a CommunicationTokenCredential with the given access token, which is only valid until the token is valid
        CommunicationTokenCredential userCredential = new CommunicationTokenCredential(userAccessToken);

        // Initialize the chat client
        final ChatClientBuilder builder = new ChatClientBuilder();
        builder.endpoint(endpoint)
            .credential(userCredential)
            .httpClient(httpClient);
        ChatClient chatClient = builder.buildClient();
    }
}
```

## <a name="start-a-chat-thread"></a>Spustit chatovací vlákno

Použijte `createChatThread` metodu k vytvoření vlákna chatu.
`createChatThreadOptions` slouží k popisu požadavku vlákna.

- Použijte `topic` parametr konstruktoru k poskytnutí tématu tomuto chatu; Téma lze aktualizovat poté, co je vlákno konverzace vytvořeno pomocí `UpdateThread` funkce.
- Slouží `participants` k vypsání účastníků vlákna, které mají být přidány do vlákna. `ChatParticipant` převezme uživatele, kterého jste vytvořili v rychlém startu [tokenu přístupu uživatele](../../access-tokens.md) .

`CreateChatThreadResult` je odpověď vrácená z vytváření konverzačního vlákna.
Obsahuje `getChatThread()` metodu, která vrátí `ChatThread` objekt, který lze použít k získání klienta vlákna, ze kterého můžete získat `ChatThreadClient` operaci pro provádění operací ve vytvořeném vlákně: Přidat účastníky, poslat zprávu atd. `ChatThread` Objekt obsahuje také metodu, `getId()` která NAČTE jedinečné ID vlákna.

```Java
ChatParticipant firstThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(firstUser)
    .setDisplayName("Participant Display Name 1");

ChatParticipant secondThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(secondUser)
    .setDisplayName("Participant Display Name 2");

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions("Topic")
    .addParticipant(firstThreadParticipant)
    .addParticipant(secondThreadParticipant);

CreateChatThreadResult result = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = result.getChatThread().getId();
```

## <a name="list-chat-threads"></a>Vypsat vlákna chatu

Použijte `listChatThreads` metodu pro načtení seznamu existujících vláken chatu.

```java
PagedIterable<ChatThreadItem> chatThreads = chatClient.listChatThreads();

chatThreads.forEach(chatThread -> {
    System.out.printf("ChatThread id is %s.\n", chatThread.getId());
});
```

## <a name="get-a-chat-thread-client"></a>Získat klienta vlákna chatu

`getChatThreadClient`Metoda vrací klienta vlákna pro vlákno, které již existuje. Dá se použít k provádění operací na vytvořeném vlákně: Přidat účastníky, poslat zprávu atd. `chatThreadId` je jedinečné ID existujícího konverzačního vlákna.

```Java
ChatThreadClient chatThreadClient = chatClient.getChatThreadClient(chatThreadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>Odeslání zprávy do konverzačního vlákna

Použijte `sendMessage` metodu k odeslání zprávy do vlákna, které jste právě vytvořili, identifikovaných pomocí chatThreadId.
`sendChatMessageOptions` slouží k popisu žádosti o zprávu chatu.

- Slouží `content` k zadání obsahu zprávy chatu.
- Slouží `type` k určení typu obsahu zprávy chat, text nebo HTML.
- Slouží `senderDisplayName` k zadání zobrazovaného jména odesílatele.

Odpověď `sendChatMessageResult` obsahuje `id` jedinečný identifikátor zprávy.

```Java
SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
    .setContent("Message content")
    .setType(ChatMessageType.TEXT)
    .setSenderDisplayName("Sender Display Name");

SendChatMessageResult sendChatMessageResult = chatThreadClient.sendMessage(sendChatMessageOptions);
String chatMessageId = sendChatMessageResult.getId();
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Příjem zpráv chatu z konverzačního vlákna

Můžete načíst zprávy chatu pomocí cyklického dotazování `listMessages` metody v klientovi vlákna konverzace v zadaných intervalech.

```Java
chatThreadClient.listMessages().forEach(message -> {
    System.out.printf("Message id is %s.\n", message.getId());
});
```

`listMessages` Vrátí nejnovější verzi zprávy, včetně všech úprav nebo odstranění, ke kterým došlo u zprávy pomocí. editMessage () a. deleteMessage (). Pro odstraněné zprávy `chatMessage.getDeletedOn()` vrátí hodnotu DateTime, která indikuje, kdy se tato zpráva odstranila. U upravených zpráv `chatMessage.getEditedOn()` vrátí hodnotu DateTime, která indikuje, kdy byla zpráva upravena. Původní čas vytvoření zprávy lze `chatMessage.getCreatedOn()` využít pomocí a lze jej použít k řazení zpráv.

Další informace o typech zpráv naleznete zde: [typy zpráv](../../../concepts/chat/concepts.md#message-types).

## <a name="send-read-receipt"></a>Odeslat oznámení pro čtení

Použijte `sendReadReceipt` metodu pro odeslání události přijetí pro čtení do konverzačního vlákna jménem uživatele.
`chatMessageId` je jedinečné ID zprávy chatu, která byla přečtena.

```Java
String chatMessageId = message.getId();
chatThreadClient.sendReadReceipt(chatMessageId);
```

## <a name="list-chat-participants"></a>Seznam účastníků chatu

Slouží `listParticipants` k načtení stránkované kolekce obsahující účastníky vlákna chatu identifikovaného pomocí chatThreadId.

```Java
PagedIterable<ChatParticipant> chatParticipantsResponse = chatThreadClient.listParticipants();
chatParticipantsResponse.forEach(chatParticipant -> {
    System.out.printf("Participant id is %s.\n", ((CommunicationUserIdentifier) chatParticipant.getCommunicationIdentifier()).getId());
});
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>Přidat uživatele jako účastníka do konverzačního vlákna

Po vytvoření vlákna chatu můžete z něj přidat uživatele nebo je z něj odebrat. Přidáním uživatelů udělíte jim přístup k posílání zpráv do konverzačního vlákna a k přidání nebo odebrání dalších účastníků. Musíte začít získáním nového přístupového tokenu a identity pro tohoto uživatele. Před voláním metody addParticipants se ujistěte, že jste pro tohoto uživatele získali nový přístupový token a identitu. Uživatel bude potřebovat přístupový token, aby mohl inicializovat svého chatového klienta.

Použijte `addParticipants` metodu pro přidání účastníků do vlákna.

- `communicationIdentifier`je povinné, je CommunicationIdentifier, které jste vytvořili pomocí CommunicationIdentityClient v rychlém startu pro [uživatelský přístup tokenu](../../access-tokens.md) .
- `displayName`volitelné, je zobrazované jméno účastníka vlákna.
- `shareHistoryTime`volitelné, je čas, od kterého je historie chatu sdílena s účastníkem. Chcete-li sdílet historii od vytvoření vlákna chatu, nastavte tuto vlastnost na jakékoli datum, které je rovno nebo menší než čas vytvoření vlákna. Pokud chcete sdílet žádnou historii předchozí až po přidání účastníka, nastavte ho na aktuální datum. Chcete-li sdílet částečnou historii, nastavte ji na požadované datum.

```Java
List<ChatParticipant> participants = new ArrayList<ChatParticipant>();

ChatParticipant thirdThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(user3)
    .setDisplayName("Display Name 3");

ChatParticipant fourthThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(user4)
    .setDisplayName("Display Name 4");

participants.add(thirdThreadParticipant);
participants.add(fourthThreadParticipant);

chatThreadClient.addParticipants(participants);
```

## <a name="run-the-code"></a>Spuštění kódu

Přejděte do adresáře obsahujícího soubor *pom.xml* a zkompilujte projekt pomocí následujícího `mvn` příkazu.

```console
mvn compile
```

Pak Sestavte balíček.

```console
mvn package
```

Spusťte následující `mvn` příkaz, který aplikaci spustí.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
