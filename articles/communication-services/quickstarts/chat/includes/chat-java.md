---
title: zahrnout soubor
description: zahrnout soubor
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 1675ad802f3a117c26ee10218f3b3bedad9b0a00
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779619"
---
## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true) verze 8 nebo vyšší.
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

### <a name="add-the-package-references-for-the-chat-client-library"></a>Přidat odkazy na balíček pro klientskou knihovnu chatu

V souboru POM se na balíček odkazuje `azure-communication-chat` pomocí rozhraní API pro chat:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-chat</artifactId>
    <version>1.0.0-beta.2</version> 
</dependency>
```

Pro ověřování musí klient odkazovat na `azure-communication-common` balíček:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-common</artifactId>
    <version>1.0.0-beta.2</version> 
</dependency>

```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce služby Azure Communications Scripting Client Library pro jazyk Java.

| Název                                  | Popis                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Tato třída je potřebná pro funkci chatu. Vytvoří se jeho instance s informacemi o předplatném a použije se k vytváření, získávání a odstraňování vláken. |
| ChatAsyncClient | Tato třída je potřebná pro funkci asynchronního chatu. Vytvoří se jeho instance s informacemi o předplatném a použije se k vytváření, získávání a odstraňování vláken. |
| ChatThreadClient | Tato třída je potřebná pro funkci konverzačního vlákna. Získáte instanci prostřednictvím ChatClient a použijete ji k posílání, přijímání, aktualizaci a odstraňování zpráv, přidávání, odebírání a získávání uživatelů, posílání oznámení a čtení. |
| ChatThreadAsyncClient | Tato třída je potřebná pro funkci asynchronního zřetězení chatu. Získáte instanci prostřednictvím ChatAsyncClient a použijete ji k posílání, přijímání, aktualizaci a odstraňování zpráv, přidávání, odebírání a získávání uživatelů, posílání oznámení a čtení. |

## <a name="create-a-chat-client"></a>Vytvoření chatového klienta
Chcete-li vytvořit chatovacího klienta, použijte koncový bod komunikační služby a přístupový token, který byl vygenerován jako součást požadavků. Tokeny přístupu uživatele umožňují vytvářet klientské aplikace, které se přímo ověřují na komunikačních službách Azure. Po vygenerování těchto tokenů na serveru je předejte zpátky do klientského zařízení. K předání tokenu klientovi konverzace je nutné použít třídu CommunicationUserCredential ze společné klientské knihovny. 

Při přidávání příkazů importu nezapomeňte přidat pouze importy z modelu COM. Azure. Communications. chat a com. Azure. Communications. chat. Models Namespaces, a ne z oboru názvů com. Azure. Communication. chat. Implements. V souboru App. Java, který byl vygenerován prostřednictvím Maven, můžete použít následující kód, který začíná na:

```Java
import com.azure.communication.chat.*;
import com.azure.communication.chat.models.*;
import com.azure.communication.common.*;
import com.azure.core.http.HttpClient;

import java.io.*;

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

        // Create a CommunicationUserCredential with the given access token, which is only valid until the token is valid
        CommunicationUserCredential userCredential = new CommunicationUserCredential(userAccessToken);

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

- Použijte `topic` k poskytnutí tématu tomuto chatu; Téma lze aktualizovat poté, co je vlákno konverzace vytvořeno pomocí `UpdateThread` funkce.
- Slouží `members` k vypsání členů vlákna, které mají být přidány do vlákna. `ChatThreadMember` převezme uživatele, kterého jste vytvořili v rychlém startu [tokenu přístupu uživatele](../../access-tokens.md) .

Odpověď `chatThreadClient` se používá k provádění operací na vytvořeném konverzačním vlákně: Přidání členů do konverzačního vlákna, odeslání zprávy, odstranění zprávy atd. Obsahuje vlastnost, `chatThreadId` která je jedinečné ID vlákna chatu. Vlastnost je přístupná prostřednictvím veřejné metody. getChatThreadId ().

```Java
List<ChatThreadMember> members = new ArrayList<ChatThreadMember>();

ChatThreadMember firstThreadMember = new ChatThreadMember()
    .setUser(firstUser)
    .setDisplayName("Member Display Name 1");
    
ChatThreadMember secondThreadMember = new ChatThreadMember()
    .setUser(secondUser)
    .setDisplayName("Member Display Name 2");

members.add(firstThreadMember);
members.add(secondThreadMember);

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions()
    .setTopic("Topic")
    .setMembers(members);
ChatThreadClient chatThreadClient = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = chatThreadClient.getChatThreadId();
```

## <a name="send-a-message-to-a-chat-thread"></a>Odeslání zprávy do konverzačního vlákna

Použijte `sendMessage` metodu k odeslání zprávy do vlákna, které jste právě vytvořili, identifikovaných pomocí chatThreadId.
`sendChatMessageOptions` slouží k popisu žádosti o zprávu chatu.

- Slouží `content` k zadání obsahu zprávy chatu.
- Slouží `priority` k zadání úrovně priority zprávy chatu, jako je například Normal nebo high; Tato vlastnost se dá použít k tomu, aby uživateli příjemce ve vaší aplikaci měl indikátor uživatelského rozhraní, aby se mohl dostat do zprávy nebo provádět vlastní obchodní logiku.
- Slouží `senderDisplayName` k zadání zobrazovaného jména odesílatele.

Odpověď `sendChatMessageResult` obsahuje `id` jedinečný identifikátor zprávy.

```Java
SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
    .setContent("Message content")
    .setPriority(ChatMessagePriority.NORMAL)
    .setSenderDisplayName("Sender Display Name");

SendChatMessageResult sendChatMessageResult = chatThreadClient.sendMessage(sendChatMessageOptions);
String chatMessageId = sendChatMessageResult.getId();
```


## <a name="get-a-chat-thread-client"></a>Získat klienta vlákna chatu

`getChatThreadClient`Metoda vrací klienta vlákna pro vlákno, které již existuje. Dá se použít k provádění operací na vytvořeném vlákně: přidat členy, poslat zprávu atd. `chatThreadId` je jedinečné ID existujícího konverzačního vlákna.

```Java
String chatThreadId = "Id";
ChatThread chatThread = chatClient.getChatThread(chatThreadId);
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Příjem zpráv chatu z konverzačního vlákna

Můžete načíst zprávy chatu pomocí cyklického dotazování `listMessages` metody v klientovi vlákna konverzace v zadaných intervalech.

```Java
chatThreadClient.listMessages().iterableByPage().forEach(resp -> {
    System.out.printf("Response headers are %s. Url %s  and status code %d %n", resp.getHeaders(),
        resp.getRequest().getUrl(), resp.getStatusCode());
    resp.getItems().forEach(message -> {
        System.out.printf("Message id is %s.", message.getId());
    });
});
```

`listMessages` Vrátí nejnovější verzi zprávy, včetně všech úprav nebo odstranění, ke kterým došlo u zprávy pomocí. editMessage () a. deleteMessage (). Pro odstraněné zprávy `chatMessage.getDeletedOn()` vrátí hodnotu DateTime, která indikuje, kdy se tato zpráva odstranila. U upravených zpráv `chatMessage.getEditedOn()` vrátí hodnotu DateTime, která indikuje, kdy byla zpráva upravena. Původní čas vytvoření zprávy lze `chatMessage.getCreatedOn()` využít pomocí a lze jej použít k řazení zpráv.

`listMessages` vrátí různé typy zpráv, které mohou být identifikovány pomocí `chatMessage.getType()` . Tyto typy:

- `Text`: Běžná zpráva chatu odeslaná členem vlákna.

- `ThreadActivity/TopicUpdate`: Systémová zpráva, která indikuje, že téma bylo aktualizováno.

- `ThreadActivity/AddMember`: Systémová zpráva, která indikuje, že jeden nebo více členů bylo přidáno do konverzačního vlákna.

- `ThreadActivity/DeleteMember`: Systémová zpráva, která indikuje, že člen byl odebrán z konverzačního vlákna.

Další podrobnosti najdete v tématu [typy zpráv](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Přidat uživatele jako člena do vlákna chatu

Po vytvoření vlákna chatu můžete z něj přidat uživatele nebo je z něj odebrat. Přidáním uživatelů udělíte jim přístup k posílání zpráv do konverzačního vlákna a k přidání nebo odebrání jiných členů. Musíte začít získáním nového přístupového tokenu a identity pro tohoto uživatele. Před voláním metody addMembers se ujistěte, že jste pro tohoto uživatele získali nový přístupový token a identitu. Uživatel bude potřebovat přístupový token, aby mohl inicializovat svého chatového klienta.

Použijte `addMembers` metodu pro přidání členů vlákna do vlákna identifikovaného pomocí IDvlákna.

- Slouží `members` k vypsání členů, kteří mají být přidáni do konverzačního vlákna.
- `user`je povinné, je CommunicationUser, které jste vytvořili pomocí CommunicationIdentityClient v rychlém startu pro [uživatelský přístup tokenu](../../access-tokens.md) .
- `display_name`volitelné, je zobrazované jméno člena vlákna.
- `share_history_time`volitelné, je čas, od kterého je historie chatu sdílena s členem. Chcete-li sdílet historii od vytvoření vlákna chatu, nastavte tuto vlastnost na jakékoli datum, které je rovno nebo menší než čas vytvoření vlákna. Chcete-li sdílet žádnou historii předchozí po přidání člena, nastavte jej na aktuální datum. Chcete-li sdílet částečnou historii, nastavte ji na požadované datum.

```Java
List<ChatThreadMember> members = new ArrayList<ChatThreadMember>();

ChatThreadMember firstThreadMember = new ChatThreadMember()
    .setUser(user1)
    .setDisplayName("Display Name 1");

ChatThreadMember secondThreadMember = new ChatThreadMember()
    .setUser(user2)
    .setDisplayName("Display Name 2");

members.add(firstThreadMember);
members.add(secondThreadMember);

AddChatThreadMembersOptions addChatThreadMembersOptions = new AddChatThreadMembersOptions()
    .setMembers(members);
chatThreadClient.addMembers(addChatThreadMembersOptions);
```

## <a name="remove-user-from-a-chat-thread"></a>Odebrání uživatele z konverzačního vlákna

Podobně jako při přidávání uživatele do vlákna můžete odebrat uživatele z konverzačního vlákna. K tomu je potřeba sledovat identity uživatelů přidaných členů.

Použijte `removeMember` , kde `user` je CommunicationUser, který jste vytvořili.

```Java
chatThreadClient.removeMember(user);
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
