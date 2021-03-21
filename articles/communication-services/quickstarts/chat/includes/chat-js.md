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
ms.openlocfilehash: 0805537fe0791a622eb1814cc233c04d914dbecd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104612761"
---
## <a name="prerequisites"></a>Předpoklady
Než začnete, nezapomeňte:

- Vytvořte si účet Azure s aktivním předplatným. Podrobnosti najdete v článku o [Vytvoření účtu zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Nainstalovat [Node.js](https://nodejs.org/en/download/) aktivní LTS a verze LTS údržby (doporučeno 8.11.1 a 10.14.1).
- Vytvořte prostředek služby Azure Communication Services. Podrobnosti najdete v tématu [vytvoření prostředku komunikace Azure](../../create-communication-resource.md). Pro tento rychlý Start budete muset **zaznamenat koncový bod prostředku** .
- Vytvořte *tři* uživatele služby ACS a vydejte jim [token přístupu uživatele](../../access-tokens.md)k přístupovému tokenu. Nezapomeňte nastavit rozsah pro **chat** a **Poznamenejte si řetězec tokenu a také řetězec userId**. Úplná ukázka vytvoří vlákno se dvěma počátečními účastníky a pak přidá třetího účastníka do vlákna.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-web-application"></a>Vytvořit novou webovou aplikaci

Nejdřív otevřete okno terminálu nebo příkazového řádku vytvořit nový adresář pro vaši aplikaci a přejděte na něj.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Spusťte `npm init -y` , chcete-li vytvořit **package.js** pro soubor s výchozími nastaveními.

```console
npm init -y
```

### <a name="install-the-packages"></a>Nainstalovat balíčky

Pomocí `npm install` příkazu nainstalujte níže uvedené klientské knihovny komunikačních služeb pro JavaScript.

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

```

`--save`Možnost vypíše knihovnu v **package.jsv** souboru jako závislost.

### <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

V tomto rychlém startu se k vytvoření sady prostředků aplikace používá Webpack. Spusťte následující příkaz pro instalaci balíčků Webpack, Webpack-CLI a Webpack-dev-Server npm a seznamte se s nimi jako se závislostmi vývoje v **package.jsna**:

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

`webpack.config.js`V kořenovém adresáři vytvořte soubor. Zkopírujte do tohoto souboru následující konfiguraci:

```
module.exports = {
  entry: "./client.js",
  output: {
    filename: "bundle.js"
  },
  devtool: "inline-source-map",
  mode: "development"
}
```

Přidejte `start` skript na, který `package.json` použijeme pro spuštění aplikace. V `scripts` části `package.json` přidejte následující:

```
"scripts": {
  "start": "webpack serve --config ./webpack.config.js"
}
```

Vytvořte soubor **index.html** v kořenovém adresáři projektu. Tento soubor použijeme jako šablonu k přidání možnosti chatu pomocí klientské knihovny služby Azure Communications chat pro JavaScript.

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Chat Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Chat Quickstart</h1>
    <script src="./bundle.js"></script>
  </body>
</html>
```

Vytvořte soubor v kořenovém adresáři vašeho projektu s názvem **client.js** , který bude obsahovat aplikační logiku pro tento rychlý Start.

### <a name="create-a-chat-client"></a>Vytvoření chatového klienta

Pokud chcete ve webové aplikaci vytvořit chatovacího klienta, použijte **koncový bod** komunikační služby a **přístupový token** , který byl vygenerován jako součást požadovaných kroků.

Tokeny přístupu uživatele umožňují vytvářet klientské aplikace, které se přímo ověřují na komunikačních službách Azure. V tomto rychlém startu se nezabývá vytvořením úrovně služby pro správu tokenů pro vaši aplikaci chatu. Další informace o architektuře s přístupovými tokeny najdete v tématu věnovaném [konceptům](../../../concepts/chat/concepts.md) chatu a na [tokenech přístupu uživatele](../../access-tokens.md) .

V následujícím kódu **client.js** pomocí koncového bodu a přístupového tokenu přidat funkci chatu pomocí klientské knihovny služby Azure Communications chat pro JavaScript.

```JavaScript

import { ChatClient } from '@azure/communication-chat';
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

// Your unique Azure Communication service endpoint
let endpointUrl = 'https://<RESOURCE_NAME>.communication.azure.com';
// The user access token generated as part of the pre-requisites
let userAccessToken = '<USER_ACCESS_TOKEN>';

let chatClient = new ChatClient(endpointUrl, new AzureCommunicationTokenCredential(userAccessToken));
console.log('Azure Communication Chat client created!');
```
- Nahraďte **endpointUrl** koncovým bodem prostředku služby Communications Services. Další informace najdete v tématu [vytvoření prostředku komunikace Azure](../../create-communication-resource.md) (Pokud jste to ještě neudělali).
- Nahraďte **userAccessToken** tokenem, který jste vystavili.


### <a name="run-the-code"></a>Spuštění kódu

Spusťte následující příkaz pro vytvoření balíčku aplikace Host na místním serveru:
```console
npm run start
```
Otevřete prohlížeč a přejděte na http://localhost:8080/ .
V konzole nástroje pro vývojáře v prohlížeči byste měli vidět následující informace:

```console
Azure Communication Chat client created!
```

## <a name="object-model"></a>Objektový model
Následující třídy a rozhraní zpracovávají některé hlavní funkce služby Azure Communications Library chat pro JavaScript.

| Název                                   | Description                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Tato třída je potřebná pro funkci chatu. Vytvoří se jeho instance s informacemi o předplatném a použije se k vytváření, získávání a odstraňování vláken. |
| ChatThreadClient | Tato třída je potřebná pro funkci konverzačního vlákna. Získáte instanci prostřednictvím ChatClient a použijete ji k posílání, přijímání, aktualizaci a odstraňování zpráv, přidávání, odebírání a získávání uživatelů, odesílání oznámení o přečtení a čtení a čtení a k odběru událostí chatu. |


## <a name="start-a-chat-thread"></a>Spustit chatovací vlákno

Použijte `createThread` metodu k vytvoření vlákna chatu.

`createThreadRequest` slouží k popisu požadavku vlákna:

- Slouží `topic` k poskytnutí tématu tomuto chatu. Témata lze aktualizovat po vytvoření vlákna konverzace pomocí `UpdateThread` funkce.
- Slouží `participants` k vypsání účastníků, kteří mají být přidáni do konverzačního vlákna.

Po vyřešení `createChatThread` Metoda vrátí hodnotu `CreateChatThreadResult` . Tento model obsahuje `chatThread` vlastnost, kde můžete získat přístup k `id` nově vytvořenému vláknu. Pak můžete použít `id` k získání instance `ChatThreadClient` . `ChatThreadClient`Pak lze použít k provedení operace v rámci vlákna, jako je například odesílání zpráv nebo výpis účastníků.

```JavaScript
async function createChatThread() {
    let createThreadRequest = {
        topic: 'Preparation for London conference',
        participants: [{
                    id: { communicationUserId: '<USER_ID_FOR_JACK>' },
                    displayName: 'Jack'
                }, {
                    id: { communicationUserId: '<USER_ID_FOR_GEETA>' },
                    displayName: 'Geeta'
                }]
    };
    let createChatThreadResult = await chatClient.createChatThread(createThreadRequest);
    let threadId = createChatThreadResult.chatThread.id;
    return threadId;
    }

createChatThread().then(async threadId => {
    console.log(`Thread created:${threadId}`);
    // PLACEHOLDERS
    // <CREATE CHAT THREAD CLIENT>
    // <RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>
    // <SEND MESSAGE TO A CHAT THREAD>
    // <LIST MESSAGES IN A CHAT THREAD>
    // <ADD NEW PARTICIPANT TO THREAD>
    // <LIST PARTICIPANTS IN A THREAD>
    // <REMOVE PARTICIPANT FROM THREAD>
    });
```

Nahraďte **USER_ID_FOR_JACK** a **USER_ID_FOR_GEETA** identifikátory uživatelů získaných při vytváření uživatelů a tokenů ([tokeny přístupu uživatele](../../access-tokens.md)).

Po aktualizaci karty prohlížeče by se v konzole měla zobrazit následující:
```console
Thread created: <thread_id>
```

## <a name="get-a-chat-thread-client"></a>Získat klienta vlákna chatu

`getChatThreadClient`Metoda vrátí `chatThreadClient` pro vlákno, které již existuje. Dá se použít k provádění operací na vytvořeném vlákně: Přidat účastníky, poslat zprávu atd. IDvlákna je jedinečné ID existujícího konverzačního vlákna.

```JavaScript
let chatThreadClient = chatClient.getChatThreadClient(threadId);
console.log(`Chat Thread client for threadId:${threadId}`);

```
Přidejte tento kód místo `<CREATE CHAT THREAD CLIENT>` komentáře v **client.js**, aktualizujte kartu prohlížeče a zkontrolujte konzolu, měli byste vidět:
```console
Chat Thread client for threadId: <threadId>
```

## <a name="send-a-message-to-a-chat-thread"></a>Odeslání zprávy do konverzačního vlákna

Použijte `sendMessage` metodu k odeslání zprávy do vlákna identifikovaného pomocí IDvlákna.

`sendMessageRequest` slouží k popisu žádosti o zprávu:

- Slouží `content` k zadání obsahu zprávy chatu;

`sendMessageOptions` slouží k popisu volitelných parametrů operace:

- Slouží `senderDisplayName` k zadání zobrazovaného jména odesílatele.
- Slouží `type` k určení typu zprávy, například "text" nebo "HTML";

`SendChatMessageResult` je odpověď vrácená z odeslání zprávy, obsahuje ID, což je jedinečné ID zprávy.

```JavaScript
let sendMessageRequest =
{
    content: 'Hello Geeta! Can you share the deck for the conference?'
};
let sendMessageOptions =
{
    senderDisplayName : 'Jack',
    type: 'text'
};
let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
let messageId = sendChatMessageResult.id;
```

Přidejte tento kód místo `<SEND MESSAGE TO A CHAT THREAD>` komentáře v **client.js**, aktualizujte kartu prohlížeče a zaškrtněte konzolu.
```console
Message sent!, message id:<number>
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Příjem zpráv chatu z konverzačního vlákna

Díky signalizaci v reálném čase se můžete přihlásit k odběru nových příchozích zpráv a aktualizovat aktuální zprávy v paměti odpovídajícím způsobem. Komunikační služby Azure podporují [seznam událostí, ke kterým se můžete přihlásit](../../../concepts/chat/concepts.md#real-time-signaling).

```JavaScript
// open notifications channel
await chatClient.startRealtimeNotifications();
// subscribe to new notification
chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");
    // your code here
});

```
Přidejte tento kód místo `<RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>` komentáře v **client.js**.
Aktualizujte kartu prohlížeče. v konzole nástroje by se měla zobrazit zpráva `Notification chatMessageReceived` .

Alternativně můžete načíst zprávy konverzace pomocí cyklického dotazování `listMessages` metody v určených intervalech.

```JavaScript

let pagedAsyncIterableIterator = await chatThreadClient.listMessages();
let nextMessage = await pagedAsyncIterableIterator.next();
    while (!nextMessage.done) {
        let chatMessage = nextMessage.value;
        console.log(`Message :${chatMessage.content}`);
        // your code here
        nextMessage = await pagedAsyncIterableIterator.next();
    }

```
Přidejte tento kód místo `<LIST MESSAGES IN A CHAT THREAD>` komentáře v **client.js**.
Aktualizujte kartu v konzole nástroje byste měli najít seznam zpráv odeslaných v tomto vlákně chatu.


`listMessages` Vrátí nejnovější verzi zprávy, včetně všech úprav nebo odstranění, ke kterým došlo u zprávy pomocí `updateMessage` a `deleteMessage` .
Pro odstraněné zprávy `chatMessage.deletedOn` vrátí hodnotu DateTime, která indikuje, kdy se tato zpráva odstranila. U upravených zpráv `chatMessage.editedOn` vrátí hodnotu DateTime, která indikuje, kdy byla zpráva upravena. Původní čas vytvoření zprávy lze použít pomocí `chatMessage.createdOn` , který lze použít k řazení zpráv.

`listMessages` vrátí různé typy zpráv, které mohou být identifikovány pomocí `chatMessage.type` . Tyto typy:

- `Text`: Běžná zpráva chatu odeslaná účastníkem vlákna.

- `ThreadActivity/TopicUpdate`: Systémová zpráva, která indikuje, že téma bylo aktualizováno.

- `ThreadActivity/AddParticipant`: Systémová zpráva, která indikuje, že jeden nebo více účastníků bylo přidáno do konverzačního vlákna.

- `ThreadActivity/RemoveParticipant`: Systémová zpráva, která indikuje, že účastník byl odebrán z konverzačního vlákna.

Další podrobnosti najdete v tématu [typy zpráv](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Přidat uživatele jako účastníka do konverzačního vlákna

Po vytvoření vlákna chatu můžete z něj přidat uživatele nebo je z něj odebrat. Přidáním uživatelů udělíte jim přístup k posílání zpráv do konverzačního vlákna a k přidání nebo odebrání dalších účastníků.

Před voláním `addParticipants` metody se ujistěte, že jste pro tohoto uživatele získali nový přístupový token a identitu. Uživatel bude potřebovat přístupový token, aby mohl inicializovat svého chatového klienta.

`addParticipantsRequest` popisuje objekt Request, který `participants` uvádí seznam účastníků, kteří mají být přidáni do vlákna chatu.
- `id`, Required, je identifikátor komunikace, který má být přidán do vlákna chatu.
- `displayName`volitelné, je zobrazované jméno účastníka vlákna.
- `shareHistoryTime`volitelné, je čas, od kterého je historie chatu sdílena s účastníkem. Chcete-li sdílet historii od vytvoření vlákna chatu, nastavte tuto vlastnost na jakékoli datum, které je rovno nebo menší než čas vytvoření vlákna. Pokud chcete sdílet žádnou historii předchozí až po přidání účastníka, nastavte ho na aktuální datum. Chcete-li sdílet částečnou historii, nastavte ji na datum podle svého výběru.

```JavaScript

let addParticipantsRequest =
{
    participants: [
        {
            id: { communicationUserId: '<NEW_PARTICIPANT_USER_ID>' },
            displayName: 'Jane'
        }
    ]
};

await chatThreadClient.addParticipants(addParticipantsRequest);

```
Nahradit **NEW_PARTICIPANT_USER_ID** [novým ID uživatele](../../access-tokens.md) přidejte tento kód místo `<ADD NEW PARTICIPANT TO THREAD>` komentáře do **client.js**

## <a name="list-users-in-a-chat-thread"></a>Vypsat uživatele ve vlákně chatu
```JavaScript
async function listParticipants() {
   let pagedAsyncIterableIterator = await chatThreadClient.listParticipants();
   let next = await pagedAsyncIterableIterator.next();
   while (!next.done) {
      let user = next.value;
      console.log(`User :${user.displayName}`);
      next = await pagedAsyncIterableIterator.next();
   }
}
await listParticipants();
```
Přidejte tento kód místo `<LIST PARTICIPANTS IN A THREAD>` komentáře v **client.js**, aktualizujte kartu prohlížeče a zkontrolujte konzolu, měli byste vidět informace o uživatelích ve vlákně.

## <a name="remove-user-from-a-chat-thread"></a>Odebrání uživatele z konverzačního vlákna

Podobně jako při přidávání účastníka můžete odebrat účastníky z konverzačního vlákna. Aby bylo možné odebrat, budete muset sledovat ID přidaných účastníků.

Použijte `removeParticipant` metodu, kde `participant` je uživatel komunikace, který má být odebrán z vlákna.

```JavaScript

await chatThreadClient.removeParticipant({ communicationUserId: <PARTICIPANT_ID> });
await listParticipants();
```
Nahraďte **PARTICIPANT_ID** číslem ID uživatele použitým v předchozím kroku (<NEW_PARTICIPANT_USER_ID>).
Přidejte tento kód místo `<REMOVE PARTICIPANT FROM THREAD>` komentáře v **client.js**,
