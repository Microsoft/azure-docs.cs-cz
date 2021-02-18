---
title: zahrnout soubor
description: zahrnout soubor
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 2/16/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 7e62bbc5929eaf23a9b7be12de222105bc2529cd
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653549"
---
## <a name="prerequisites"></a>Požadavky
Než začnete, nezapomeňte:

- Vytvořte si účet Azure s aktivním předplatným. Podrobnosti najdete v článku o [Vytvoření účtu zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Nainstalovat [Android Studio](https://developer.android.com/studio), budeme pomocí Android Studio vytvořit aplikaci pro Android, která pro rychlý Start spustí instalaci závislostí.
- Vytvořte prostředek služby Azure Communication Services. Podrobnosti najdete v tématu [vytvoření prostředku komunikace Azure](../../create-communication-resource.md). Pro tento rychlý Start budete muset **zaznamenat koncový bod prostředku** .
- Vytvořte **dva** uživatele komunikačních služeb a vydejte jim [token přístupu uživatele](../../access-tokens.md)k přístupovému tokenu uživatele. Nezapomeňte nastavit rozsah pro **chat** a **Poznamenejte si řetězec tokenu a také řetězec userId**. V tomto rychlém startu vytvoříme vlákno s počátečním účastníkem a potom do tohoto vlákna přidá druhý účastník.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-android-application"></a>Vytvoření nové aplikace pro Android

1. Otevřete Android Studio a vyberte `Create a new project` . 
2. V dalším okně vyberte `Empty Activity` jako šablonu projektu.
3. Při volbě možnosti zadejte `ChatQuickstart` název projektu.
4. Klikněte na tlačítko Další a vyberte adresář, ve kterém chcete vytvořit projekt.

### <a name="install-the-libraries"></a>Instalace knihoven

K instalaci nezbytných závislostí komunikačních služeb použijeme Gradle. Z příkazového řádku přejděte do kořenového adresáře `ChatQuickstart` projektu. Otevřete soubor Build. Gradle aplikace a přidejte následující závislosti do `ChatQuickstart` cíle:

```
implementation 'com.azure.android:azure-communication-common:1.0.0-beta.5'
implementation 'com.azure.android:azure-communication-chat:1.0.0-beta.5'
```

V Android Studio klikněte na ' synchronizovat nyní '.

#### <a name="alternative-to-install-libraries-through-maven"></a>Jiné Instalace knihoven pomocí Maven
Pokud chcete knihovnu importovat do projektu pomocí sestavovacího systému [Maven](https://maven.apache.org/) , přidejte ji do `dependencies` oddílu souboru vaší aplikace `pom.xml` , zadejte ID artefaktu a verzi, kterou chcete použít:

```xml
<dependency>
  <groupId>com.azure.android</groupId>
  <artifactId>azure-communication-chat</artifactId>
  <version>1.0.0-beta.5</version>
</dependency>
```


### <a name="setup-the-placeholders"></a>Nastavení zástupných symbolů

Otevřete soubor a upravte ho `MainActivity.java` . V tomto rychlém startu přidáme náš kód do a `MainActivity` zobrazíme výstup v konzole. Tento rychlý Start neřeší sestavování uživatelského rozhraní. V horní části souboru importujte `Communication common` `Communication chat` knihovny a:

```
import com.azure.android.communication.chat.*;
import com.azure.android.communication.common.*;
```

Zkopírujte následující kód do souboru `MainActivity` :

```
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
        } catch (Exception e){
            System.out.println("Quickstart failed: " + e.getMessage());
        }
    }
```

V následujících krocích nahradíme zástupné symboly ukázkovým kódem pomocí knihovny chatu služby Azure Communications.


### <a name="create-a-chat-client"></a>Vytvoření chatového klienta

Nahraďte komentář `<CREATE A CHAT CLIENT>` následujícím kódem (vložte příkazy import do horní části souboru):

```java
import com.azure.android.communication.chat.ChatClient;
import com.azure.android.core.http.HttpHeader;

final String endpoint = "https://<resource>.communication.azure.com";
final String userAccessToken = "<user_access_token>";

ChatClient client = new ChatClient.Builder()
    .endpoint(endpoint)
    .credentialInterceptor(chain -> chain.proceed(chain.request()
        .newBuilder()
        .header(HttpHeader.AUTHORIZATION, userAccessToken)
        .build());
```

1. Použijte `AzureCommunicationChatServiceAsyncClient.Builder` ke konfiguraci a vytvoření instance `AzureCommunicationChatClient` .
2. Nahraďte `<resource>` prostředkem komunikačních služeb.
3. Nahraďte `<user_access_token>` platným přístupovým tokenem komunikační služby.

## <a name="object-model"></a>Objektový model
Následující třídy a rozhraní zpracovávají některé hlavní funkce služby Azure Communications Library chat pro JavaScript.

| Název                                   | Description                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Tato třída je potřebná pro funkci chatu. Vytvoří se jeho instance s informacemi o předplatném a použije se k vytváření, získávání a odstraňování vláken. |
| ChatThreadClient | Tato třída je potřebná pro funkci konverzačního vlákna. Získáte instanci prostřednictvím ChatClient a použijete ji k posílání, přijímání, aktualizaci a odstraňování zpráv, přidávání, odebírání a získávání uživatelů, odesílání oznámení o přečtení a čtení a čtení a k odběru událostí chatu. |

## <a name="start-a-chat-thread"></a>Spustit chatovací vlákno

Pomocí naší `ChatClient` služby vytvoříme nové vlákno s počátečním uživatelem.

Nahraďte komentář `<CREATE A CHAT THREAD>` následujícím kódem:

```java
//  The list of ChatParticipant to be added to the thread.
List<ChatParticipant> participants = new ArrayList<>();
// The communication user ID you created before, required.
final String id = "<user_id>";
// The display name for the thread participant.
final String displayName = "initial participant";
participants.add(new ChatParticipant()
    .setId(id)
    .setDisplayName(displayName));

// The topic for the thread.
final String topic = "General";
// The model to pass to the create method.
CreateChatThreadRequest thread = new CreateChatThreadRequest()
    .setTopic(topic)
    .setParticipants(participants);

// optional, set a repeat request ID 
final String repeatabilityRequestID = '123';

client.createChatThread(thread, repeatabilityRequestID, new Callback<CreateChatThreadResult>() {
    public void onSuccess(CreateChatThreadResult result, okhttp3.Response response) {
        // MultiStatusResponse is the result returned from creating a thread.
        // It has a 'multipleStatus' property which represents a list of IndividualStatusResponse.
        String threadId;
        List<IndividualStatusResponse> statusList = result.getMultipleStatus();
        for (IndividualStatusResponse status : statusList) {
            if (status.getId().endsWith("@thread.v2")
                && status.getType().contentEquals("Thread")) {
                threadId = status.getId();
                break;
            }
        }
        // Take further action.
    }

    public void onFailure(Throwable throwable, okhttp3.Response response) {
        // Handle error.
    }
});
```

Nahraďte `<user_id>` platným ID uživatele komunikační služby. `threadId`V pozdějších krocích použijeme odpověď z odpovědi vrácené do obslužné rutiny dokončení.

## <a name="get-a-chat-thread-client"></a>Získat klienta vlákna chatu

Teď, když jsme vytvořili chatovací vlákno, získáte `ChatThreadClient` k provádění operací v rámci tohoto vlákna. Nahraďte komentář `<CREATE A CHAT THREAD CLIENT>` následujícím kódem:

```
ChatThreadClient threadClient =
        new ChatThreadClient.Builder()
            .endpoint(<endpoint>))
            .build();
```

Nahraďte `<endpoint>` vaším koncovým bodem komunikační služby.

## <a name="send-a-message-to-a-chat-thread"></a>Odeslání zprávy do konverzačního vlákna

Nahraďte komentář `<SEND A MESSAGE>` následujícím kódem:

```java
// The chat message content, required.
final String content = "Test message 1";
// The display name of the sender, if null (i.e. not specified), an empty name will be set.
final String senderDisplayName = "An important person";
SendChatMessageRequest message = new SendChatMessageRequest()
    .setType(ChatMessageType.TEXT)
    .setContent(content)
    .setSenderDisplayName(senderDisplayName);

// The unique ID of the thread.
final String threadId = "<thread_id>";
threadClient.sendChatMessage(threadId, message, new Callback<String>() {
    @Override
    public void onSuccess(String messageId, Response response) {
        // A string is the response returned from sending a message, it is an id, 
        // which is the unique ID of the message.
        final String chatMessageId = messageId;
        // Take further action.
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        // Handle error.
    }
});
```

Nahraďte `<thread_id>` ID vlákna, do kterého se posílá zpráva.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Přidat uživatele jako účastníka do konverzačního vlákna

Nahraďte komentář `<ADD A USER>` následujícím kódem:

```java
//  The list of ChatParticipant to be added to the thread.
List<ChatParticipant> participants = new ArrayList<>();
// The CommunicationUser.identifier you created before, required.
final String id = "<user_id>";
// The display name for the thread participant.
final String displayName = "a new participant";
participants.add(new ChatParticipant().setId(id).setDisplayName(displayName));
// The model to pass to the add method.
AddChatParticipantsRequest participants = new AddChatParticipantsRequest()
    .setParticipants(participants);

// The unique ID of the thread.
final String threadId = "<thread_id>";
threadClient.addChatParticipants(threadId, participants, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, Response response) {
        // Take further action.
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        // Handle error.
    }
});
```

1. Nahraďte `<user_id>` ID uživatele komunikačních služeb uživatele, který se má přidat. 
2. Nahraďte `<thread_id>` ID vlákna, do kterého uživatel přidává.

## <a name="list-users-in-a-thread"></a>Výpis uživatelů ve vlákně

Nahraďte komentář `<LIST USERS>` následujícím kódem:

```java
// The unique ID of the thread.
final String threadId = "<thread_id>";

// The maximum number of participants to be returned per page, optional.
final int maxPageSize = 10;

// Skips participants up to a specified position in response.
final int skip = 0;

threadClient.listChatParticipantsPages(threadId,
    maxPageSize,
    skip,
    new Callback<AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>>>() {
    @Override
    public void onSuccess(AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>> firstPage,
        Response response) {
        // pageCollection enables enumerating list of chat participants.
        pageCollection.getFirstPage(new Callback<Page<ChatParticipant>>() {
            @Override
            public void onSuccess(Page<ChatParticipant> firstPage, Response response) {
                for (ChatParticipant participant : firstPage.getItems()) {
                    // Take further action.
                }
                retrieveNextParticipantsPages(firstPage.getPageId(), pageCollection);
            }

            @Override
            public void onFailure(Throwable throwable, Response response) {
                // Handle error.
            }
         }
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        // Handle error.
    }
});

void listChatParticipantsNext(String nextLink,
    AsyncPagedDataCollection<Page<ChatParticipant>> pageCollection) {
        @Override
        public void onSuccess(Page<ChatParticipant> nextPage, Response response) {
            for (ChatParticipant participant : nextPage.getItems()) {
                // Take further action.
            }
            if (nextPage.getPageId() != null) {
                retrieveNextParticipantsPages(nextPage.getPageId(), pageCollection);
            }
        }

        @Override
        public void onFailure(Throwable throwable, Response response) {
            // Handle error.
        }
}
```

Nahraďte `<thread_id>` identifikátorem vlákna, pro který vydáváte uživatele.

## <a name="remove-user-from-a-chat-thread"></a>Odebrání uživatele z konverzačního vlákna

Nahraďte komentář `<REMOVE A USER>` následujícím kódem:

```java
// The unique ID of the thread.
final String threadId = "<thread_id>";
// The unique ID of the participant.
final String participantId = "<participant_id>";
threadClient.removeChatParticipant(threadId, participantId, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, Response response) {
        // Take further action.
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        // Handle error.
    }
});
```

1. Nahraďte `<thread_id>` ID vlákna, ze kterého se odebírá uživatel.
1. Nahraďte `<participant_id>` ID uživatele komunikačních služeb odebraného účastníka.

## <a name="run-the-code"></a>Spuštění kódu

V Android Studio stiskněte tlačítko spustit pro sestavení a spuštění projektu. V konzole můžete zobrazit výstup z kódu a výstup protokolovacího nástroje z ChatClient.
