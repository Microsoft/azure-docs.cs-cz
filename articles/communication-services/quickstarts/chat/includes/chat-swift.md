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
ms.openlocfilehash: 24a5c92164e0eace41224edfd2153c6142f7ea49
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251570"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-chat.md)]

## <a name="prerequisites"></a>Požadavky
Než začnete, nezapomeňte:

- Vytvořte si účet Azure s aktivním předplatným. Podrobnosti najdete v článku o [Vytvoření účtu zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Nainstalujte [Xcode](https://developer.apple.com/xcode/) a [CocoaPods](https://cocoapods.org/). Xcode použijete k vytvoření aplikace pro iOS pro rychlý Start a CocoaPods k instalaci závislostí.
- Vytvořte prostředek služby Azure Communication Services. Podrobnosti najdete v tématu [rychlý Start: vytvoření a Správa prostředků komunikačních služeb](../../create-communication-resource.md). V tomto rychlém startu je potřeba zaznamenat koncový bod prostředku.
- Vytvořte dva uživatele v komunikačních službách Azure a vydejte jim [token přístupu uživatele](../../access-tokens.md). Nezapomeňte nastavit obor na `chat` a poznamenat si `token` řetězec a také `userId` řetězec. V tomto rychlém startu vytvoříte vlákno s počátečním účastníkem a potom do vlákna přidáte druhého účastníka.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-ios-application"></a>Vytvoření nové aplikace pro iOS

Otevřete Xcode a vyberte **vytvořit nový projekt Xcode**. Pak jako platformu a **aplikaci** pro šablonu vyberte **iOS** .

Jako název projektu zadejte **ChatQuickstart**. Pak jako **životní cyklus vyberte jako** rozhraní, **UIKit aplikaci delegáta** **a jako** jazyk.

Vyberte **Další** a vyberte adresář, ve kterém chcete vytvořit projekt.

### <a name="install-the-libraries"></a>Instalace knihoven

Pomocí CocoaPods nainstalujte nezbytné závislosti komunikačních služeb.

Z příkazového řádku, pokračujte v kořenovém adresáři `ChatQuickstart` projektu iOS. Vytvořte souboru podfile pomocí následujícího příkazu: `pod init` .

Otevřete souboru podfile a přidejte následující závislosti do `ChatQuickstart` cíle:

```
pod 'AzureCommunication', '~> 1.0.0-beta.11'
pod 'AzureCommunicationChat', '~> 1.0.0-beta.11'
```

Nainstalujte závislosti pomocí následujícího příkazu: `pod install` . Všimněte si, že se tím také vytvoří pracovní prostor Xcode.

Po spuštění `pod install` znovu otevřete projekt v Xcode tak, že vyberete nově vytvořený `.xcworkspace` .

### <a name="set-up-the-placeholders"></a>Nastavení zástupných symbolů

Otevřete pracovní prostor `ChatQuickstart.xcworkspace` v Xcode a pak otevřete `ViewController.swift` .

V tomto rychlém startu přidáte svůj kód do a `viewController` zobrazíte výstup v konzole Xcode. Tento rychlý Start neřeší sestavování uživatelského rozhraní v iOS. 

V horní části `viewController.swift` , importujte `AzureCommunication` `AzureCommunicatonChat` knihovny a:

```
import AzureCommunication
import AzureCommunicationChat
```

Zkopírujte následující kód do `viewDidLoad()` metody `ViewController` :

```
override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.

        let semaphore = DispatchSemaphore(value: 0)
        DispatchQueue.global(qos: .background).async {
            do {
                // <CREATE A CHAT CLIENT>
                
                // <CREATE A CHAT THREAD>

                // <LIST ALL CHAT THREADS>

                // <GET A CHAT THREAD CLIENT>

                // <SEND A MESSAGE>

                // <SEND A READ RECEIPT >

                // <RECEIVE MESSAGES>

                // <ADD A USER>
                
                // <LIST USERS>
            } catch {
                print("Quickstart failed: \(error.localizedDescription)")
            }
        }
    }
```

Pro demonstrační účely budeme k synchronizaci kódu použít semafor. V následujících krocích nahradíte zástupné symboly ukázkovým kódem pomocí knihovny chatu služby Azure Communications.


### <a name="create-a-chat-client"></a>Vytvoření chatového klienta

Nahraďte komentář `<CREATE A CHAT CLIENT>` následujícím kódem:

```
let endpoint = "<ACS_RESOURCE_ENDPOINT>"
let credential =
try CommunicationTokenCredential(
    token: "<ACCESS_TOKEN>"
)
let options = AzureCommunicationChatClientOptions()

let chatClient = try ChatClient(
    endpoint: endpoint,
    credential: credential,
    withOptions: options
)
```

Nahraďte `<ACS_RESOURCE_ENDPOINT>` koncovým bodem prostředku služby Azure Communication Services. Nahraďte `<ACCESS_TOKEN>` platným přístupovým tokenem komunikační služby.

Tento rychlý Start nezahrnuje vytvoření vrstvy služby pro správu tokenů pro vaši aplikaci chatu, ale doporučuje se to. Další informace najdete v části "architektura chatu" v tématu [koncepce chatu](../../../concepts/chat/concepts.md).

Další informace o tokenech přístupu uživatele najdete v tématu [rychlý Start: vytváření a Správa přístupových tokenů](../../access-tokens.md).

## <a name="object-model"></a>Objektový model 

Následující třídy a rozhraní zpracovávají některé hlavní funkce služby Azure Communications Chat SDK pro JavaScript.

| Název                                   | Description                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ChatClient` | Tato třída je potřebná pro funkci chatu. Vytvoří se jeho instance s informacemi o předplatném a použije se k vytváření, získávání a odstraňování vláken. |
| `ChatThreadClient` | Tato třída je potřebná pro funkci konverzačního vlákna. Získáte instanci prostřednictvím `ChatClient` a použijete ji k posílání, přijímání, aktualizaci a odstraňování zpráv. Můžete ho taky použít k přidání, odebrání a získání uživatelů, posílání oznámení o přečtení a čtení a k přihlášení k odběru událostí chatu. |

## <a name="start-a-chat-thread"></a>Spustit chatovací vlákno

Nyní můžete použít `ChatClient` k vytvoření nového vlákna s počátečním uživatelem.

Nahraďte komentář `<CREATE A CHAT THREAD>` následujícím kódem:

```
let request = CreateChatThreadRequest(
    topic: "Quickstart",
    participants: [
        ChatParticipant(
            id: CommunicationUserIdentifier("<USER_ID>"),
            displayName: "Jack"
        )
    ]
)

var threadId: String?
chatClient.create(thread: request) { result, _ in
    switch result {
    case let .success(result):
        threadId = result.chatThread?.id

    case .failure:
        fatalError("Failed to create thread.")
    }
    semaphore.signal()
}
semaphore.wait()
```

Nahraďte `<USER_ID>` platným ID uživatele komunikační služby.

Semafor se tady používá pro čekání na obslužnou rutinu dokončení, než budete pokračovat. V pozdějších krocích použijete `threadId` odpověď z odpovědi vrácené do obslužné rutiny dokončení.

## <a name="list-all-chat-threads"></a>Zobrazit seznam všech vláken chatu

Po vytvoření konverzačního vlákna můžeme vypsat všechny podprocesy chatu voláním `listChatThreads` metody on `ChatClient` . Nahraďte komentář `<LIST ALL CHAT THREADS>` následujícím kódem:

```
chatClient.listThreads { result, _ in
    switch result {
    case let .success(chatThreadItems):
        var iterator = chatThreadItems.syncIterator
            while let chatThreadItem = iterator.next() {
                print("Thread id: \(chatThreadItem.id)")
            }
    case .failure:
        print("Failed to list threads")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="get-a-chat-thread-client"></a>Získat klienta vlákna chatu

Nyní, když jste vytvořili chatovací vlákno, můžete získat a `ChatThreadClient` provést operace v rámci tohoto vlákna.

Nahraďte komentář `<GET A CHAT THREAD CLIENT>` následujícím kódem:

```
let chatThreadClient = try chatClient.createClient(forThread: threadId!)
```

## <a name="send-a-message-to-a-chat-thread"></a>Odeslání zprávy do konverzačního vlákna

Nahraďte komentář `<SEND A MESSAGE>` následujícím kódem:

```
let message = SendChatMessageRequest(
    content: "Hello!",
    senderDisplayName: "Jack"
)

var messageId: String?

chatThreadClient.send(message: message) { result, _ in
    switch result {
    case let .success(result):
        print("Message sent, message id: \(result.id)")
        messageId = result.id
    case .failure:
        print("Failed to send message")
    }
    semaphore.signal()
}
semaphore.wait()
```

Nejprve sestavíte `SendChatMessageRequest` , který obsahuje zobrazovaný název a zobrazované jméno odesílatele. Tento požadavek může také obsahovat čas historie sdílení, pokud ho chcete zahrnout. Odpověď vrácená obslužné rutině dokončení obsahuje ID zprávy, která byla odeslána.


## <a name="send-a-read-receipt"></a>Odeslat účtenku pro čtení

Můžete odeslat oznámení o čtení pro konkrétní zprávu voláním `ChatThreadClients` `sendReadReceipt` metody. Nahraďte komentář `<SEND A READ RECEIPT>` následujícím kódem:

```
if let id = messageId {
    chatThreadClient.sendReadReceipt(forMessage: id) { result, _ in
        switch result {
        case .success:
            print("Read receipt sent")
        case .failure:
            print("Failed to send read receipt")
        }
        semaphore.signal()
    }
    semaphore.wait()
} else {
    print("Cannot send read receipt without a message id")
}
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Příjem zpráv chatu z konverzačního vlákna

Můžete přijímat zprávy z konverzačního vlákna voláním `listMessages()` metody z `ChatThreadClient` . Seznam zpráv obsahuje systémové zprávy i zprávy odesílané uživateli. Další informace o typech zpráv, které můžete přijímat, najdete v tématu [typy zpráv](https://docs.microsoft.com/azure/communication-services/concepts/chat/concepts#message-types) .

Nahraďte komentář `<RECEIVE MESSAGES>` následujícím kódem:

```
chatThreadClient.listMessages { result, _ in
    switch result {
    case let .success(messages):
        var iterator = messages.syncIterator
        while let message = iterator.next() {
            print("Received message of type \(message.type)")
        }

    case .failure:
        print("Failed to receive messages")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Přidat uživatele jako účastníka do konverzačního vlákna

Nahraďte komentář `<ADD A USER>` následujícím kódem:

```
let user = ChatParticipant(
    id: CommunicationUserIdentifier("<USER_ID>"),
    displayName: "Jane"
)

chatThreadClient.add(participants: [user]) { result, _ in
    switch result {
    case let .success(result):
        (result.invalidParticipants != nil) ? print("Added participant") : print("Error adding participant")
    case .failure:
        print("Failed to add the participant")
    }
    semaphore.signal()
}
semaphore.wait()
```

Nahraďte `<USER_ID>` ID uživatele komunikačních služeb uživatele, který se má přidat.

Pokud přidáváte účastníka do vlákna, vrácená odpověď může obsahovat chyby. Tyto chyby reprezentují selhání při přidávání konkrétních účastníků.

## <a name="list-users-in-a-thread"></a>Výpis uživatelů ve vlákně

Nahraďte komentář `<LIST USERS>` následujícím kódem:

```
chatThreadClient.listParticipants { result, _ in
    switch result {
    case let .success(participants):
        var iterator = participants.syncIterator
        while let participant = iterator.next() {
            let user = participant.id as! CommunicationUserIdentifier
            print("User with id: \(user.identifier)")
        }
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```

## <a name="run-the-code"></a>Spuštění kódu

V Xcode stiskněte tlačítko Spustit a sestavte a spusťte projekt. V konzole můžete zobrazit výstup z kódu a výstup protokolovacího nástroje z ChatClient.

