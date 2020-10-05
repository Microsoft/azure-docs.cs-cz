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
ms.openlocfilehash: d0754ea2d7e8f8f59ec475be8e27fcffd058c11f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91376754"
---
## <a name="prerequisites"></a>Předpoklady
Než začnete, nezapomeňte:

- Vytvořte si účet Azure s aktivním předplatným. Podrobnosti najdete v článku o [Vytvoření účtu zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Nainstalovat [Node.js](https://nodejs.org/en/download/) aktivní LTS a verze LTS údržby (doporučeno 8.11.1 a 10.14.1).
- Vytvořte prostředek služby Azure Communication Services. Podrobnosti najdete v tématu [vytvoření prostředku komunikace Azure](../../create-communication-resource.md). Pro tento rychlý Start budete muset zaznamenat **koncový bod** prostředku.
- [Přístupový token uživatele](../../access-tokens.md). Ujistěte se, že jste nastavili obor na "chat" a poznamenali jste řetězec tokenu a také řetězec userId.

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

Pomocí textového editoru vytvořte soubor s názvem **start-chat.js** v kořenovém adresáři projektu. Do tohoto souboru přidáte veškerý zdrojový kód pro tento rychlý Start v následujících oddílech.

### <a name="install-the-packages"></a>Nainstalovat balíčky

Pomocí `npm install` příkazu nainstalujte níže uvedené klientské knihovny komunikačních služeb pro JavaScript.

```console
npm install @azure/communication-common --save

npm install @azure/communication-administration --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

```

`--save`Možnost vypíše knihovnu v **package.jsv** souboru jako závislost.

### <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

V tomto rychlém startu se k vytvoření sady prostředků aplikace používá Webpack. Spusťte následující příkaz pro instalaci balíčků Webpack, Webpack-CLI a Webpack-dev-Server npm a seznamte se s nimi jako se závislostmi vývoje v **package.jsna**:

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

Vytvořte soubor **index.html** v kořenovém adresáři projektu. Tento soubor použijeme jako šablonu k přidání možnosti chatu pomocí klientské knihovny služby Azure Communications chat pro JavaScript.

Zde je kód:

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

Pokud chcete ve webové aplikaci vytvořit chatovacího klienta, použijte koncový bod komunikační služby a přístupový token, který byl vygenerován jako součást požadavků. Tokeny přístupu uživatele umožňují vytvářet klientské aplikace, které se přímo ověřují na komunikačních službách Azure. Po vygenerování těchto tokenů na serveru je předejte zpátky do klientského zařízení. Je nutné použít `AzureCommunicationUserCredential` třídu z rozhraní `Common client library` k předání tokenu klientovi chatu.

Vytvořte soubor **client.js** v kořenovém adresáři projektu. Tento soubor použijeme k přidání možnosti chatu pomocí klientské knihovny Azure Communications chat pro JavaScript.

```JavaScript

import { ChatClient } from '@azure/communication-chat';
import { AzureCommunicationUserCredential } from '@azure/communication-common';

// Your unique Azure Communication service endpoint
let endpointUrl = 'https://<RESOURCE_NAME>.communication.azure.com';
let userAccessToken = '<USER_ACCESS_TOKEN>';

let chatClient = new ChatClient(endpointUrl, new AzureCommunicationUserCredential(userAccessToken));
console.log('Azure Communication Chat client created!');
```
Nahraďte **koncový bod** objektem, který jste vytvořili dříve, na základě dokumentace k [Vytvoření zdroje komunikace Azure](../../create-communication-resource.md) .
Nahraďte **USER_ACCESS_TOKEN** tokenem vydaným na základě dokumentace [přístupového tokenu uživatele](../../access-tokens.md) .
Přidat tento kód do souboru **client.js**


### <a name="run-the-code"></a>Spuštění kódu
Použijte `webpack-dev-server` k sestavení a spuštění vaší aplikace. Spusťte následující příkaz pro vytvoření balíčku aplikace Host na místním serveru:
```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```
Otevřete prohlížeč a přejděte na http://localhost:8080/ .
V konzole nástroje pro vývojáře v prohlížeči byste měli vidět následující informace:

```console
Azure Communication Chat client created!
```

## <a name="object-model"></a>Objektový model 
Následující třídy a rozhraní zpracovávají některé hlavní funkce služby Azure Communications Library chat pro JavaScript.

| Název                                   | Popis                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Tato třída je potřebná pro funkci chatu. Vytvoří se jeho instance s informacemi o předplatném a použije se k vytváření, získávání a odstraňování vláken. |
| ChatThreadClient | Tato třída je potřebná pro funkci konverzačního vlákna. Získáte instanci prostřednictvím ChatClient a použijete ji k posílání, přijímání, aktualizaci a odstraňování zpráv, přidávání, odebírání a získávání uživatelů, odesílání oznámení o přečtení a čtení a čtení a k odběru událostí chatu. |


## <a name="start-a-chat-thread"></a>Spustit chatovací vlákno

Použijte `createThread` metodu k vytvoření vlákna chatu.

`createThreadRequest` slouží k popisu požadavku vlákna:

- Použijte `topic` k poskytnutí tématu tomuto chatu; Téma lze aktualizovat poté, co je vlákno konverzace vytvořeno pomocí `UpdateThread` funkce. 
- Slouží `members` k vypsání členů, kteří mají být přidáni do konverzačního vlákna;

Po vyřešení se `createChatThread` Metoda vrátí, `threadId` která se používá k provádění operací v nově vytvořeném konverzačním vlákně, jako je přidání členů do konverzačního vlákna, odesílání zpráv, odstraňování zpráv atd.

```Javascript
async function createChatThread() {
   let createThreadRequest = {
       topic: 'Preparation for London conference',
       members: [{
                   user: { communicationUserId: '<USER_ID_FOR_JACK>' },
                   displayName: 'Jack'
               }, {
                   user: { communicationUserId: '<USER_ID_FOR_GEETA>' },
                   displayName: 'Geeta'
               }]
   };
   let chatThreadClient= await chatClient.createChatThread(createThreadRequest);
   let threadId = chatThreadClient.threadId;
   return threadId;
}

createChatThread().then(async threadId => {
   console.log(`Thread created:${threadId}`);
   // PLACEHOLDERS
   // <CREATE CHAT THREAD CLIENT>
   // <RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>
   // <SEND MESSAGE TO A CHAT THREAD>
   // <LIST MESSAGES IN A CHAT THREAD>
   // <ADD NEW MEMBER TO THREAD>
   // <LIST MEMBERS IN A THREAD>
   // <REMOVE MEMBER FROM THREAD>
});
```

Nahradit **USER_ID_FOR_JACK** a **USER_ID_FOR_GEETA** identifikátory uživatelů získaných z předchozího kroku (vytvořit uživatele a vystavit [tokeny přístupu uživatele](../../access-tokens.md))

Když aktualizujete kartu prohlížeče, měli byste vidět následující v konzole nástroje.
```console
Thread created: <threadId>
```

## <a name="get-a-chat-thread-client"></a>Získat klienta vlákna chatu

`getChatThreadClient`Metoda vrátí `chatThreadClient` pro vlákno, které již existuje. Dá se použít k provádění operací na vytvořeném vlákně: přidat členy, poslat zprávu atd. IDvlákna je jedinečné ID existujícího konverzačního vlákna.

```JavaScript

let chatThreadClient = await chatClient.getChatThreadClient(threadId);
console.log(`Chat Thread client for threadId:${chatThreadClient.threadId}`);

```
Přidejte tento kód místo `<CREATE CHAT THREAD CLIENT>` komentáře v **client.js**, aktualizujte kartu prohlížeče a zkontrolujte konzolu, měli byste vidět:
```console
Chat Thread client for threadId: <threadId>
```

## <a name="send-a-message-to-a-chat-thread"></a>Odeslání zprávy do konverzačního vlákna

Použijte `sendMessage` metodu k odeslání zprávy chatu k vláknu, které jste právě vytvořili, identifikovanému pomocí IDvlákna.

`sendMessageRequest` Popisuje povinná pole žádosti o zprávu chatu:

- Slouží `content` k zadání obsahu zprávy chatu;

`sendMessageOptions` Popisuje volitelná pole žádosti o zprávu chatu:

- Slouží `priority` k zadání úrovně priority zprávy chatu, jako je například Normal nebo high; tuto vlastnost lze použít k tomu, aby uživatel příjemce ve vaší aplikaci upozornil na zprávu nebo spustil vlastní obchodní logiku.   
- Slouží `senderDisplayName` k zadání zobrazovaného jména odesílatele.

Odpověď `sendChatMessageResult` obsahuje "ID", což je jedinečné ID této zprávy.

```JavaScript

let sendMessageRequest =
{
    content: 'Hello Geeta! Can you share the deck for the conference?'
};
let sendMessageOptions =
{
    priority: 'Normal',
    senderDisplayName : 'Jack'
};
let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
let messageId = sendChatMessageResult.id;
console.log(`Message sent!, message id:${messageId}`);

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

- `Text`: Běžná zpráva chatu odeslaná členem vlákna.

- `ThreadActivity/TopicUpdate`: Systémová zpráva, která indikuje, že téma bylo aktualizováno.

- `ThreadActivity/AddMember`: Systémová zpráva, která indikuje, že jeden nebo více členů bylo přidáno do konverzačního vlákna.

- `ThreadActivity/RemoveMember`: Systémová zpráva, která indikuje, že člen byl odebrán z konverzačního vlákna.

Další podrobnosti najdete v tématu [typy zpráv](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Přidat uživatele jako člena do vlákna chatu

Po vytvoření vlákna chatu můžete z něj přidat uživatele nebo je z něj odebrat. Přidáním uživatelů udělíte jim přístup k posílání zpráv do konverzačního vlákna a k přidání nebo odebrání jiných členů. Před voláním `addMembers` metody se ujistěte, že jste pro tohoto uživatele získali nový přístupový token a identitu. Uživatel bude potřebovat přístupový token, aby mohl inicializovat svého chatového klienta.

`addMembersRequest` popisuje objekt Request, který `members` obsahuje seznam členů, které mají být přidány do konverzačního vlákna;
- `user`, požadováno, je uživatel komunikace, který má být přidán do vlákna chatu.
- `displayName`volitelné, je zobrazované jméno člena vlákna.
- `shareHistoryTime`volitelné, je čas, od kterého je historie chatu sdílena s členem. Chcete-li sdílet historii od vytvoření vlákna chatu, nastavte tuto vlastnost na jakékoli datum, které je rovno nebo menší než čas vytvoření vlákna. Chcete-li sdílet žádnou historii předchozí po přidání člena, nastavte jej na aktuální datum. Chcete-li sdílet částečnou historii, nastavte ji na datum podle svého výběru.

```JavaScript

let addMembersRequest =
{
    members: [
        {
            user: { communicationUserId: '<NEW_MEMBER_USER_ID>' },
            displayName: 'Jane'
        }
    ]
};

await chatThreadClient.addMembers(addMembersRequest);

```
Nahradit **NEW_MEMBER_USER_ID** [novým ID uživatele](../../access-tokens.md) přidejte tento kód místo `<ADD NEW MEMBER TO THREAD>` komentáře do **client.js**

## <a name="list-users-in-a-chat-thread"></a>Vypsat uživatele ve vlákně chatu
```JavaScript
async function listThreadMembers() {
   let pagedAsyncIterableIterator = await chatThreadClient.listMembers();
   let next = await pagedAsyncIterableIterator.next();
   while (!next.done) {
      let user = next.value;
      console.log(`User :${user.displayName}`);
      next = await pagedAsyncIterableIterator.next();
   }
}
await listThreadMembers();
```
Přidejte tento kód místo `<LIST MEMBERS IN A THREAD>` komentáře v **client.js**, aktualizujte kartu prohlížeče a zkontrolujte konzolu, měli byste vidět informace o uživatelích ve vlákně.

## <a name="remove-user-from-a-chat-thread"></a>Odebrání uživatele z konverzačního vlákna

Podobně jako při přidávání člena můžete odebrat členy z konverzačního vlákna. Aby bylo možné odebrat, budete muset sledovat ID členů, které jste přidali.

Použijte `removeMember` metodu, kde `member` je uživatel komunikace, který má být odebrán z vlákna.

```JavaScript

await chatThreadClient.removeMember({ communicationUserId: <MEMBER_ID> });
await listThreadMembers();
```
Nahraďte **MEMBER_ID** číslem ID uživatele použitým v předchozím kroku (<NEW_MEMBER_USER_ID>).
Přidejte tento kód místo `<REMOVE MEMBER FROM THREAD>` komentáře v **client.js**,
