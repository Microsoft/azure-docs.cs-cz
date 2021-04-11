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
ms.openlocfilehash: b2c5237f3f7e949edbfb5486a3a17cc6e0a008a4
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106178565"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-chat.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete, nezapomeňte:

- Vytvořte si účet Azure s aktivním předplatným. Podrobnosti najdete v článku o [Vytvoření účtu zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Nainstalovat [Android Studio](https://developer.android.com/studio), budeme pomocí Android Studio vytvořit aplikaci pro Android, která pro rychlý Start spustí instalaci závislostí.
- Vytvořte prostředek služby Azure Communication Services. Podrobnosti najdete v tématu [vytvoření prostředku komunikace Azure](../../create-communication-resource.md). Pro tento rychlý Start budete muset **zaznamenat koncový bod prostředku** .
- Vytvořte **dva** uživatele komunikačních služeb a vydejte jim [token přístupu uživatele](../../access-tokens.md)k přístupovému tokenu uživatele. Nezapomeňte nastavit rozsah pro **chat** a **Poznamenejte si řetězec tokenu a řetězec userId**. V tomto rychlém startu vytvoříme vlákno s počátečním účastníkem a potom do tohoto vlákna přidá druhý účastník.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-android-application"></a>Vytvoření nové aplikace pro Android

1. Otevřete Android Studio a vyberte `Create a new project` . 
2. V dalším okně vyberte `Empty Activity` jako šablonu projektu.
3. Při volbě možnosti zadejte `ChatQuickstart` název projektu.
4. Klikněte na tlačítko Další a vyberte adresář, ve kterém chcete vytvořit projekt.

### <a name="install-the-libraries"></a>Instalace knihoven

K instalaci nezbytných závislostí komunikačních služeb použijeme Gradle. Z příkazového řádku přejděte do kořenového adresáře `ChatQuickstart` projektu. Otevřete soubor Build. Gradle aplikace a přidejte následující závislosti do `ChatQuickstart` cíle:

```
implementation 'com.azure.android:azure-communication-common:1.0.0-beta.8'
implementation 'com.azure.android:azure-communication-chat:1.0.0-beta.8'
```

#### <a name="exclude-meta-files-in-packaging-options-in-root-buildgradle"></a>Vyloučit meta soubory v možnostech balení v kořenovém sestavení. Gradle
```
android {
   ...
    packagingOptions {
        exclude 'META-INF/DEPENDENCIES'
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/license'
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/notice'
        exclude 'META-INF/ASL2.0'
        exclude("META-INF/*.md")
        exclude("META-INF/*.txt")
        exclude("META-INF/*.kotlin_module")
    }
}
```

#### <a name="alternative-to-install-libraries-through-maven"></a>Jiné Instalace knihoven pomocí Maven
Pokud chcete knihovnu importovat do projektu pomocí sestavovacího systému [Maven](https://maven.apache.org/) , přidejte ji do `dependencies` oddílu souboru vaší aplikace `pom.xml` , zadejte ID artefaktu a verzi, kterou chcete použít:

```xml
<dependency>
  <groupId>com.azure.android</groupId>
  <artifactId>azure-communication-chat</artifactId>
  <version>1.0.0-beta.8</version>
</dependency>
```


### <a name="setup-the-placeholders"></a>Nastavení zástupných symbolů

Otevřete soubor a upravte ho `MainActivity.java` . V tomto rychlém startu přidáme náš kód do a `MainActivity` zobrazíme výstup v konzole. Tento rychlý Start neřeší sestavování uživatelského rozhraní. V horní části souboru importujte `Communication common` `Communication chat` knihovny a:

```
import com.azure.android.communication.chat.*;
import com.azure.android.communication.common.*;
```

Zkopírujte následující kód do souboru `MainActivity` :

```java
    private String secondUserId = "<second_user_id>";
    private String threadId = "<thread_id>";
    private String chatMessageId = "<chat_message_id>";
    private final String sdkVersion = "1.0.0-beta.8";
    private static final String APPLICATION_ID = "Chat Quickstart App";
    private static final String SDK_NAME = "azure-communication-com.azure.android.communication.chat";
    private static final String TAG = "--------------Chat Quickstart App-------------";

    private void log(String msg) {
        Log.i(TAG, msg);
        Toast.makeText(this, msg, Toast.LENGTH_LONG).show();
    }
    
   @Override
    protected void onStart() {
        super.onStart();
        try {
            // <CREATE A CHAT CLIENT>

            // <CREATE A CHAT THREAD>

            // <CREATE A CHAT THREAD CLIENT>

            // <SEND A MESSAGE>

            // <ADD A USER>

            // <LIST USERS>

            // <REMOVE A USER>
            
            // <<SEND A TYPING NOTIFICATION>>
            
            // <<SEND A READ RECEIPT>>
               
            // <<LIST READ RECEIPTS>>
        } catch (Exception e){
            System.out.println("Quickstart failed: " + e.getMessage());
        }
    }
```

V následujících krocích nahradíme zástupné symboly ukázkovým kódem pomocí knihovny chatu služby Azure Communications.


### <a name="create-a-chat-client"></a>Vytvoření chatového klienta

Nahraďte komentář `<CREATE A CHAT CLIENT>` následujícím kódem (vložte příkazy import do horní části souboru):

```java
import com.azure.android.communication.chat.ChatAsyncClient;
import com.azure.android.communication.chat.ChatClientBuilder;
import com.azure.android.core.credential.AccessToken;
import com.azure.android.core.http.HttpHeader;
import com.azure.android.core.http.okhttp.OkHttpAsyncClientProvider;
import com.azure.android.core.http.policy.BearerTokenAuthenticationPolicy;
import com.azure.android.core.http.policy.UserAgentPolicy;

final String endpoint = "https://<resource>.communication.azure.com";
final String userAccessToken = "<user_access_token>";

ChatAsyncClient chatAsyncClient = new ChatClientBuilder()
    .endpoint(endpoint)
    .credentialPolicy(new BearerTokenAuthenticationPolicy((request, callback) ->
        callback.onSuccess(new AccessToken(userAccessToken, OffsetDateTime.now().plusDays(1)))))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .httpClient(new OkHttpAsyncClientProvider().createInstance())
    .buildAsyncClient();

```

1. Použijte `ChatClientBuilder` ke konfiguraci a vytvoření instance `ChatAsyncClient` .
2. Nahraďte `<resource>` prostředkem komunikačních služeb.
3. Nahraďte `<user_access_token>` platným přístupovým tokenem komunikační služby.

## <a name="object-model"></a>Objektový model
Následující třídy a rozhraní zpracovávají některé hlavní funkce služby Azure Communications Chat SDK pro JavaScript.

| Název                                   | Description                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient/ChatAsyncClient | Tato třída je potřebná pro funkci chatu. Vytvoří se jeho instance s informacemi o předplatném a použije se k vytváření, získávání a odstraňování vláken. |
| ChatThreadClient/ChatThreadAsyncClient | Tato třída je potřebná pro funkci konverzačního vlákna. Získáte instanci prostřednictvím ChatClient a použijete ji k posílání, přijímání, aktualizaci a odstraňování zpráv, přidávání, odebírání a získávání uživatelů, odesílání oznámení o přečtení a čtení a čtení a k odběru událostí chatu. |

## <a name="start-a-chat-thread"></a>Spustit chatovací vlákno

Pomocí naší `ChatAsyncClient` služby vytvoříme nové vlákno s počátečním uživatelem.

Nahraďte komentář `<CREATE A CHAT THREAD>` následujícím kódem:

```java
// A list of ChatParticipant to start the thread with.
List<ChatParticipant> participants = new ArrayList<>();
// The communication user ID you created before, required.
String id = "<user_id>";
// The display name for the thread participant.
String displayName = "initial participant";
participants.add(new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(id))
    .setDisplayName(displayName));

// The topic for the thread.
final String topic = "General";
// Optional, set a repeat request ID.
final String repeatabilityRequestID = "";
// Options to pass to the create method.
CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions()
    .setTopic(topic)
    .setParticipants(participants)
    .setIdempotencyToken(repeatabilityRequestID);

CreateChatThreadResult createChatThreadResult =
    chatAsyncClient.createChatThread(createChatThreadOptions).get();
ChatThreadProperties chatThreadProperties = createChatThreadResult.getChatThreadProperties();
threadId = chatThreadProperties.getId();

```

Nahraďte `<user_id>` platným ID uživatele komunikační služby. `threadId`V pozdějších krocích budeme používat odpověď vrácenou do obslužné rutiny dokončení, takže nahraďte `<thread_id>` třídu v rámci `threadId` této žádosti a znovu spusťte aplikaci.

## <a name="get-a-chat-thread-client"></a>Získat klienta vlákna chatu

Teď, když jsme vytvořili chatovací vlákno, získáte `ChatThreadAsyncClient` k provádění operací v rámci tohoto vlákna. Nahraďte komentář `<CREATE A CHAT THREAD CLIENT>` následujícím kódem:

```
ChatThreadAsyncClient chatThreadAsyncClient = new ChatThreadClientBuilder()
    .endpoint(endpoint)
    .credentialPolicy(new BearerTokenAuthenticationPolicy((request, callback) ->
        callback.onSuccess(new AccessToken(userAccessToken, OffsetDateTime.now().plusDays(1)))))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .httpClient(new OkHttpAsyncClientProvider().createInstance())
    .chatThreadId(threadId)
    .buildAsyncClient();

```

## <a name="send-a-message-to-a-chat-thread"></a>Odeslání zprávy do konverzačního vlákna

Ujistěte se, že jsme nahradili `<thread_id>` platným ID vlákna, pošleme vám zprávu do tohoto vlákna hned teď.

Nahraďte komentář `<SEND A MESSAGE>` následujícím kódem:

```java
// The chat message content, required.
final String content = "Test message 1";
// The display name of the sender, if null (i.e. not specified), an empty name will be set.
final String senderDisplayName = "An important person";
SendChatMessageOptions chatMessageOptions = new SendChatMessageOptions()
    .setType(ChatMessageType.TEXT)
    .setContent(content)
    .setSenderDisplayName(senderDisplayName);

// A string is the response returned from sending a message, it is an id, which is the unique ID of the message.
chatMessageId = chatThreadAsyncClient.sendMessage(chatMessageOptions).get().getId();

```

Po získání můžeme `chatMessageId` použít `<chat_message_id>` `chatMessageId` pro pozdější použití metod v rychlém startu a opětovném spuštění aplikace.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Přidat uživatele jako účastníka do konverzačního vlákna

Nahraďte komentář `<ADD A USER>` následujícím kódem:

```java
// The display name for the thread participant.
displayName = "a new participant";
ChatParticipant participant = new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(secondUserId))
    .setDisplayName(secondUserDisplayName);
        
chatThreadAsyncClient.addParticipant(participant);

```

`<second_user_id>`Ve třídě nahraďte ID uživatele komunikační služby, které má uživatel přidat. 

## <a name="list-users-in-a-thread"></a>Výpis uživatelů ve vlákně

Nahraďte komentář `<LIST USERS>` následujícím kódem:

```java
// The maximum number of participants to be returned per page, optional.
final int maxPageSize = 10;

// Skips participants up to a specified position in response.
final int skip = 0;

// Options to pass to the list method.
ListParticipantsOptions listParticipantsOptions = new ListParticipantsOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedResponse<ChatParticipant> firstPageWithResponse =
    chatThreadAsyncClient.getParticipantsFirstPageWithResponse(listParticipantsOptions, Context.NONE).get();

for (ChatParticipant participant : firstPageWithResponse.getValue()) {
    // You code to handle participant
}

listParticipantsNextPage(firstPageWithResponse.getContinuationToken(), 2);

```

Do třídy vložte následující pomocnou metodu:

```java
void listParticipantsNextPage(String continuationToken, int pageNumber) {
if (continuationToken != null) {
    PagedResponse<ChatParticipant> nextPageWithResponse =
        chatThreadAsyncClient.getParticipantsNextPageWithResponse(continuationToken, Context.NONE).get();
        for (ChatParticipant participant : nextPageWithResponse.getValue()) {
            // You code to handle participant
        }
            
        listParticipantsNextPage(nextPageWithResponse.getContinuationToken(), ++pageNumber);
    }
}

```


## <a name="remove-user-from-a-chat-thread"></a>Odebrání uživatele z konverzačního vlákna

Nezapomeňte nahradit `<second_user_id>` platným ID uživatele. teď z tohoto vlákna odebereme druhého uživatele.

Nahraďte komentář `<REMOVE A USER>` následujícím kódem:

```java
// Using the unique ID of the participant.
chatThreadAsyncClient.removeParticipant(new CommunicationUserIdentifier(secondUserId)).get();

```

## <a name="send-a-typing-notification"></a>Odeslat oznámení o zápisu

Nahraďte komentář `<SEND A TYPING NOTIFICATION>` následujícím kódem:

```java
chatThreadAsyncClient.sendTypingNotification().get();
```

## <a name="send-a-read-receipt"></a>Odeslat účtenku pro čtení

Nezapomeňte nahradit `<chat_message_id>` platným ID zprávy chatu, abychom vám teď poslali oznámení o přečtení pro tuto zprávu.

Nahraďte komentář `<SEND A READ RECEIPT>` následujícím kódem:

```java
chatThreadAsyncClient.sendReadReceipt(chatMessageId).get();
```

## <a name="list-read-receipts"></a>Seznam potvrzení čtení

Nahraďte komentář `<READ RECEIPTS>` následujícím kódem:

```java
// The maximum number of participants to be returned per page, optional.
maxPageSize = 10;
// Skips participants up to a specified position in response.
skip = 0;
// Options to pass to the list method.
ListReadReceiptOptions listReadReceiptOptions = new ListReadReceiptOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedResponse<ChatMessageReadReceipt> firstPageWithResponse =
    chatThreadAsyncClient.getReadReceiptsFirstPageWithResponse(listReadReceiptOptions, Context.NONE).get();

for (ChatMessageReadReceipt readReceipt : firstPageWithResponse.getValue()) {
    // You code to handle readReceipt
}

listReadReceiptsNextPage(firstPageWithResponse.getContinuationToken(), 2);

```

Do třídy vložte následující pomocnou metodu:
```java
void listReadReceiptsNextPage(String continuationToken, int pageNumber) {
    if (continuationToken != null) {
        PagedResponse<ChatMessageReadReceipt> nextPageWithResponse =
            chatThreadAsyncClient.getReadReceiptsNextPageWithResponse(continuationToken, Context.NONE).get();

        for (ChatMessageReadReceipt readReceipt : nextPageWithResponse.getValue()) {
            // You code to handle readReceipt
        }

        listParticipantsNextPage(nextPageWithResponse.getContinuationToken(), ++pageNumber);
    }
}

```


## <a name="run-the-code"></a>Spuštění kódu

V Android Studio stiskněte tlačítko spustit pro sestavení a spuštění projektu. V konzole můžete zobrazit výstup z kódu a výstup protokolovacího nástroje z ChatClient.
