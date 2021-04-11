---
title: Rychlý Start – připojení týmu ke schůzce
author: askaur
ms.author: askaur
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 773bca81694534346019e30e9d55190af6f51e74
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105106785"
---
## <a name="joining-the-meeting-chat"></a>Spojení s chatem na schůzce 

Po povolení interoperability týmů může uživatel komunikačních služeb připojit se k volání Teams jako externí uživatel pomocí volání sady SDK. Spojení s voláním je přidá také jako účastník konverzace o schůzce, kde mohou odesílat a přijímat zprávy s ostatními uživateli v volání. Uživatel nebude mít přístup ke zprávám chatu, které byly odeslány před připojením k volání. Chcete-li se připojit ke schůzce a spustit konverzaci, můžete postupovat podle následujících kroků.

## <a name="install-the-chat-packages"></a>Instalace balíčků chatu

Pomocí `npm install` příkazu nainstalujte potřebné sady SDK služby Communications Services pro JavaScript.

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

npm install @azure/communication-calling --save
```

`--save`Možnost vypíše knihovnu v **package.jsv** souboru jako závislost.

## <a name="add-the-teams-ui-controls"></a>Přidat ovládací prvky uživatelského rozhraní týmů

Nahraďte kód v index.html následujícím fragmentem kódu.
Textová pole v horní části stránky budou použita k zadání kontextu schůzky týmů a ID vlákna schůzky. K připojení k zadané schůzce se použije tlačítko Join Teams Meeting.
V dolní části stránky se zobrazí automaticky otevírané okno konverzace. Dá se použít k posílání zpráv ve vlákně schůzky a v reálném čase se zobrazí všechny zprávy odeslané ve vlákně, zatímco uživatel ACS je členem.

```html
<!DOCTYPE html>
<html>
   <head>
      <title>Communication Client - Calling and Chat Sample</title>
      <style>
         body {box-sizing: border-box;}
         /* The popup chat - hidden by default */
         .chat-popup {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #f1f1f1;
         z-index: 9;
         }
         .message-box {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #FFFACD;
         z-index: 9;
         }
         .form-container {
         max-width: 300px;
         padding: 10px;
         background-color: white;
         }
         .form-container textarea {
         width: 90%;
         padding: 15px;
         margin: 5px 0 22px 0;
         border: none;
         background: #e1e1e1;
         resize: none;
         min-height: 50px;
         }
         .form-container .btn {
         background-color: #4CAF40;
         color: white;
         padding: 14px 18px;
         margin-bottom:10px;
         opacity: 0.6;
         border: none;
         cursor: pointer;
         width: 100%;
         }
         .container {
         border: 1px solid #dedede;
         background-color: #F1F1F1;
         border-radius: 3px;
         padding: 8px;
         margin: 8px 0;
         }
         .darker {
         border-color: #ccc;
         background-color: #ffdab9;
         margin-left: 25px;
         margin-right: 3px;
         }
         .lighter {
         margin-right: 20px;
         margin-left: 3px;
         }
         .container::after {
         content: "";
         clear: both;
         display: table;
         }
      </style>
   </head>
   <body>
      <h4>Azure Communication Services</h4>
      <h1>Calling and Chat Quickstart</h1>
          <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
          <input id="thread-id-input" type="text" placeholder="Chat thread id"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
      <div>
        <button id="join-meeting-button" type="button">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
      </div>
      <div class="chat-popup" id="chat-box">
         <div id="messages-container"></div>
         <form class="form-container">
            <textarea placeholder="Type message.." name="msg" id="message-box" required></textarea>
            <button type="button" class="btn" id="send-message">Send</button>
         </form>
      </div>
      <script src="./bundle.js"></script>
   </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>Povolit ovládací prvky uživatelského rozhraní týmů

Obsah souboru client.js nahraďte následujícím fragmentem kódu.

V rámci fragmentu kódu nahraďte 
- `SECRET CONNECTION STRING` s připojovacím řetězcem služby komunikace 
- `ENDPOINT URL` s adresou URL koncového bodu komunikační služby

```javascript
// run using
// npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationUserCredential } from "@azure/communication-common";
import { CommunicationIdentityClient } from "@azure/communication-identity";
import { ChatClient } from "@azure/communication-chat";

let call;
let callAgent;
let chatClient;
let chatThreadClient;

const meetingLinkInput = document.getElementById('teams-link-input');
const threadIdInput = document.getElementById('thread-id-input');
const callButton = document.getElementById("join-meeting-button");
const hangUpButton = document.getElementById("hang-up-button");
const callStateElement = document.getElementById('call-state');

const messagesContainer = document.getElementById("messages-container");
const chatBox = document.getElementById("chat-box");
const sendMessageButton = document.getElementById("send-message");
const messagebox = document.getElementById("message-box");

var userId = '';
var messages = '';

async function init() {
  const connectionString = "<SECRET CONNECTION STRING>";
  const endpointUrl = "<ENDPOINT URL>";

  const identityClient = new CommunicationIdentityClient(connectionString);

  let identityResponse = await identityClient.createUser();
  userId = identityResponse.communicationUserId;
  console.log(
    `\nCreated an identity with ID: ${identityResponse.communicationUserId}`
  );

  let tokenResponse = await identityClient.issueToken(identityResponse, [
    "voip",
    "chat",
  ]);
  const { token, expiresOn } = tokenResponse;
  console.log(
    `\nIssued an access token that expires at ${expiresOn}:`
  );
  console.log(token);

  const callClient = new CallClient();
  const tokenCredential = new AzureCommunicationUserCredential(token);
  callAgent = await callClient.createCallAgent(tokenCredential);
  callButton.disabled = false;

  chatClient = new ChatClient(
    endpointUrl,
    new AzureCommunicationUserCredential(token)
  );

  console.log('Azure Communication Chat client created!');
}

init();

callButton.addEventListener("click", async () => {
  // join with meeting link
  call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
  call.on('callStateChanged', () => {
        callStateElement.innerText = call.state;
  })
  // toggle button and chat box states
  chatBox.style.display = "block";
  hangUpButton.disabled = false;
  callButton.disabled = true;

  messagesContainer.innerHTML = messages;
  
  console.log(call);

  // open notifications channel
  await chatClient.startRealtimeNotifications();

  // subscribe to new message notifications
  chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");
    
    if (e.sender.communicationUserId != userId) {
       renderReceivedMessage(e.content);
    }
    else {
       renderSentMessage(e.content);
    }
  });
  chatThreadClient = await chatClient.getChatThreadClient(threadIdInput.value);
});

async function renderReceivedMessage(message) {
   messages += '<div class="container lighter">' + message + '</div>';
   messagesContainer.innerHTML = messages;
}

async function renderSentMessage(message) {
   messages += '<div class="container darker">' + message + '</div>';
   messagesContainer.innerHTML = messages;
}

hangUpButton.addEventListener("click", async () => 
  {
    // end the current call
    await call.hangUp();

    // toggle button states
    hangUpButton.disabled = true;
    callButton.disabled = false;
    callStateElement.innerText = '-';

    // toggle chat states
    chatBox.style.display = "none";
    messages = "";
  });

sendMessageButton.addEventListener("click", async () =>
  {
      let message = messagebox.value;

      let sendMessageRequest = { content: message };
      let sendMessageOptions = { senderDisplayName : 'Jack' };
      let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
      let messageId = sendChatMessageResult.id;

      messagebox.value = '';
      console.log(`Message sent!, message id:${messageId}`);
  });
```

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Získání týmů pro uživatele komunikačních služeb v rámci konverzačního vlákna

Propojení týmů a chat můžete načíst pomocí rozhraní Graph API, které jsou podrobně popsané v [dokumentaci ke grafům](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta). Komunikační služby, které volá sadu SDK, přijímají úplný tým odkaz na schůzku. Tento odkaz se vrátí jako součást `onlineMeeting` prostředku, který je přístupný v rámci [ `joinWebUrl` vlastnosti](/graph/api/resources/onlinemeeting?view=graph-rest-beta) s [rozhraními API graphu](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta). můžete také získat `threadId` . Odpověď bude mít `chatInfo` objekt, který obsahuje `threadID` . 

Požadované informace o schůzce a ID vlákna můžete také získat z adresy URL **připojení ke schůzce** v rámci pozvánky na schůzku v týmu.
Odkaz na schůzku týmu vypadá takto: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here` . `threadId`Odkaz bude na místo, kde `meeting_chat_thread_id` se nachází v odkazu. Před použitím zajistěte, aby `meeting_chat_thread_id` byl neřídicí znak. Měl by být v následujícím formátu: `19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2`


## <a name="run-the-code"></a>Spuštění kódu

Uživatelé nástroje Webpack můžou použít `webpack-dev-server` k sestavení a spuštění vaší aplikace. Spusťte následující příkaz pro vytvoření balíčku aplikace hostitele na místním webovém serveru:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Otevřete prohlížeč a přejděte na http://localhost:8080/ . Měli byste vidět následující:

:::image type="content" source="../acs-join-teams-meeting-chat-quickstart.png" alt-text="Snímek obrazovky dokončené aplikace JavaScriptu":::

Do textových polí vložte odkaz na schůzku týmů a ID vlákna. Stiskněte *připojit týmy* na schůzku, aby se připojili k týmům. Po přijetí uživatele služby ACS do schůzky můžete chatovat z aplikace služby komunikace. Chcete-li spustit konverzaci, přejděte k poli v dolní části stránky.

> [!NOTE] 
> V současné době jsou pro scénáře interoperability s týmy podporovány pouze zprávy, které jsou odesílány, přijímány a upravovány. Další funkce, jako je například psaní ukazatelů a komunikačních služeb uživatele, kteří přidávají nebo odebírají jiné uživatele z jednání týmů, ještě nejsou podporovány.  
