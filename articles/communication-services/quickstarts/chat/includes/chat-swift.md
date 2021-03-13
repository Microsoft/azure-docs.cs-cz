---
title: zahrnout soubor
description: zahrnout soubor
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 2/11/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 75983fe89c7f8643701da96493d1b6c9ca1c159f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103021532"
---
## <a name="prerequisites"></a>Požadavky
Než začnete, nezapomeňte:

- Vytvořte si účet Azure s aktivním předplatným. Podrobnosti najdete v článku o [Vytvoření účtu zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Nainstalujte [Xcode](https://developer.apple.com/xcode/) a [Cocoapods](https://cocoapods.org/). k vytvoření aplikace pro iOS pro rychlý Start a Cocoapods k instalaci závislostí použijeme Xcode.
- Vytvořte prostředek služby Azure Communication Services. Podrobnosti najdete v tématu [vytvoření prostředku komunikace Azure](../../create-communication-resource.md). Pro tento rychlý Start budete muset **zaznamenat koncový bod prostředku** .
- Vytvořte **dva** uživatele služby ACS a vydejte jim [token přístupu uživatele](../../access-tokens.md)k přístupovému tokenu. Nezapomeňte nastavit rozsah pro **chat** a **Poznamenejte si řetězec tokenu a také řetězec userId**. V tomto rychlém startu vytvoříme vlákno s počátečním účastníkem a potom do tohoto vlákna přidá druhý účastník.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-ios-application"></a>Vytvoření nové aplikace pro iOS

Otevřete Xcode a vyberte `Create a new Xcode project` .

V dalším okně vyberte `iOS` jako platformu a `App` pro šablonu.

Při volbě možnosti zadejte `ChatQuickstart` název projektu. Jako `Storyboard` `UIKit App Delegate` životní cyklus a jako jazyk vyberte jako rozhraní `Swift` .

Klikněte na tlačítko Další a vyberte adresář, ve kterém chcete vytvořit projekt.

### <a name="install-the-libraries"></a>Instalace knihoven

K instalaci nezbytných závislostí komunikačních služeb použijeme Cocoapods.

Z příkazového řádku přejděte do kořenového adresáře projektu pro `ChatQuickstart` iOS.

Vytvořte souboru podfile: `pod init`

Otevřete souboru podfile a přidejte následující závislosti do `ChatQuickstart` cíle:
```
pod 'AzureCommunication', '~> 1.0.0-beta.9'
pod 'AzureCommunicationChat', '~> 1.0.0-beta.9'
```

Instalace závislostí vytvoří také pracovní prostor Xcode: `pod install`

**Po spuštění pod instalací znovu otevřete projekt v Xcode výběrem nově vytvořeného `.xcworkspace` .**

### <a name="setup-the-placeholders"></a>Nastavení zástupných symbolů

Otevřete pracovní prostor `ChatQuickstart.xcworkspace` v Xcode a pak otevřete `ViewController.swift` .

V tomto rychlém startu přidáme náš kód do a `viewController` zobrazíme výstup v konzole Xcode. Tento rychlý Start neřeší vytváření uživatelského rozhraní v iOS. 

V horní části `viewController.swift` importu `AzureCommunication` `AzureCommunicatonChat` knihoven a:

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
                
                // <CREATE A CHAT THREAD CLIENT>
                
                // <SEND A MESSAGE>
                
                // <ADD A USER>
                
                // <LIST USERS>
                
                // <REMOVE A USER>
            } catch {
                print("Quickstart failed: \(error.localizedDescription)")
            }
        }
    }
```

K synchronizaci našeho kódu pro demonstrační účely použijeme semafor. V následujících krocích nahradíme zástupné symboly ukázkovým kódem pomocí knihovny chatu služby Azure Communications.


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

Nahraďte `<ACS_RESOURCE_ENDPOINT>` koncovým bodem prostředku ACS.
Nahraďte `<ACCESS_TOKEN>` platným přístupovým tokenem ACS.

V tomto rychlém startu se nezabývá vytvořením vrstvy služby pro správu tokenů pro aplikaci Chat, i když se doporučuje. Další informace o [architektuře chatu](../../../concepts/chat/concepts.md) najdete v následující dokumentaci.

Přečtěte si další informace o [tokenech přístupu uživatele](../../access-tokens.md).

## <a name="object-model"></a>Objektový model 
Následující třídy a rozhraní zpracovávají některé hlavní funkce služby Azure Communications Library chat pro JavaScript.

| Název                                   | Description                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Tato třída je potřebná pro funkci chatu. Vytvoří se jeho instance s informacemi o předplatném a použije se k vytváření, získávání a odstraňování vláken. |
| ChatThreadClient | Tato třída je potřebná pro funkci konverzačního vlákna. Získáte instanci prostřednictvím ChatClient a použijete ji k posílání, přijímání, aktualizaci a odstraňování zpráv, přidávání, odebírání a získávání uživatelů, odesílání oznámení o přečtení a čtení a čtení a k odběru událostí chatu. |

## <a name="start-a-chat-thread"></a>Spustit chatovací vlákno

Nyní použijeme náš `ChatClient` k vytvoření nového vlákna s počátečním uživatelem.

Nahraďte komentář `<CREATE A CHAT THREAD>` následujícím kódem:

```
let request = CreateThreadRequest(
    topic: "Quickstart",
    participants: [
        Participant(
            id: CommunicationUserIdentifier("<USER_ID>"),
            displayName: "Jack"
        )
    ]
)

var threadId: String?
chatClient.create(thread: request) { result, _ in
    switch result {
    case let .success(result):
        threadId = result.thread?.id

    case .failure:
        fatalError("Failed to create thread.")
    }
    semaphore.signal()
}
semaphore.wait()
```

Nahraďte `<USER_ID>` platným ID uživatele komunikační služby.

Tady používáme semafor pro čekání na obslužnou rutinu dokončení, než budete pokračovat. `threadId`V pozdějších krocích budeme používat odpověď vrácenou do obslužné rutiny dokončení.

## <a name="get-a-chat-thread-client"></a>Získat klienta vlákna chatu

Teď, když jsme vytvořili chatovací vlákno, získáte `ChatThreadClient` k provádění operací v rámci tohoto vlákna.

Nahraďte komentář `<CREATE A CHAT THREAD CLIENT>` následujícím kódem:

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

chatThreadClient.send(message: message) { result, _ in
    switch result {
    case let .success(result):
        print("Message sent, message id: \(result.id)")
    case .failure:
        print("Failed to send message")
    }
    semaphore.signal()
}
semaphore.wait()
```

Nejdřív sestavíme, `SendChatMessageRequest` který obsahuje zobrazované jméno a zobrazované jméno odesílatele (volitelně může obsahovat i čas historie sdílené složky). Odpověď vrácená obslužné rutině dokončení obsahuje ID zprávy, která byla odeslána.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Přidat uživatele jako účastníka do konverzačního vlákna

Nahraďte komentář `<ADD A USER>` následujícím kódem:

```
let user = Participant(
    id: CommunicationUserIdentifier("<USER_ID>"),
    displayName: "Jane"
)

chatThreadClient.add(participants: [user]) { result, _ in
    switch result {
    case let .success(result):
        (result.errors != nil) ? print("Added participant") : print("Error adding participant")
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```

Nahraďte `<USER_ID>` ID uživatele služby ACS, které má uživatel přidat.

Při přidávání účastníka do vlákna můžou odpověď vrácené dokončení obsahovat chyby. Tyto chyby reprezentují selhání při přidávání konkrétních účastníků.

## <a name="list-users-in-a-thread"></a>Výpis uživatelů ve vlákně

Nahraďte komentář `<LIST USERS>` následujícím kódem:

```
chatThreadClient.listParticipants { result, _ in
    switch result {
    case let .success(participants):
        var iterator = participants.syncIterator
        while let participant = iterator.next() {
            let user = participant.id as! CommunicationUserIdentifier
            print(user.identifier)
        }
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```


## <a name="remove-user-from-a-chat-thread"></a>Odebrání uživatele z konverzačního vlákna

Nahraďte komentář `<REMOVE A USER>` následujícím kódem:

```
chatThreadClient
    .remove(
        participant: CommunicationUserIdentifier("<USER_ID>")
    ) { result, _ in
        switch result {
        case .success:
            print("Removed user from the thread.")
        case .failure:
            print("Failed to remove user from the thread.")
        }
    }
```

Nahraďte `<USER ID>` ID uživatele komunikačních služeb odebíraného účastníkem.

## <a name="run-the-code"></a>Spuštění kódu

V Xcode stiskněte tlačítko Spustit a sestavte a spusťte projekt. V konzole můžete zobrazit výstup z kódu a výstup protokolovacího nástroje z ChatClient.


