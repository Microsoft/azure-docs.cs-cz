---
title: Rychlý Start – přidání videa do vaší aplikace (JavaScript)
titleSuffix: An Azure Communication Services quickstart
description: V tomto rychlém startu se dozvíte, jak do své aplikace přidat funkce pro volání videa pomocí komunikačních služeb Azure.
author: xumo-95
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: e7f74298b8bf8209a6b1473880b33d64bd17cfd9
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108090"
---
# <a name="quickstart-add-11-video-calling-to-your-app-javascript"></a>Rychlý Start: Přidání videa 1:1 pro volání do vaší aplikace (JavaScript)

## <a name="download-code"></a>Stáhnout kód

Najděte finální kód pro tento rychlý Start na [GitHubu](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-video-calling) .

## <a name="prerequisites"></a>Požadavky
- Získejte účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/en/) Aktivní LTS a verze LTS údržby (8.11.1 a 10.14.1)
- Vytvořte aktivní prostředek komunikačních služeb. [Vytvořte prostředek služby Communications](../create-communication-resource.md?pivots=platform-azp&tabs=windows).
- Vytvoření uživatelského přístupového tokenu pro vytvoření instance klienta volání. [Naučte se vytvářet a spravovat tokeny přístupu uživatelů](../access-tokens.md?pivots=programming-language-csharp).

## <a name="setting-up"></a>Nastavení
### <a name="create-a-new-nodejs-application"></a>Vytvoření nové aplikace Node.js
Otevřete okno terminálu nebo příkazového řádku pro svoji aplikaci vytvořte nový adresář a přejděte do něj.
```console
mkdir calling-quickstart && cd calling-quickstart
```
### <a name="install-the-package"></a>Instalace balíčku
Pomocí `npm install` příkazu nainstalujete službu Azure Communication Services, která volá sadu SDK pro JavaScript.

> [!IMPORTANT]
> Tento rychlý Start používá službu Azure Communication Services pro volání verze sady SDK `1.0.0.beta-10` . 


```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```
### <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

V tomto rychlém startu se k vytvoření sady prostředků aplikace používá Webpack. Spuštěním následujícího příkazu nainstalujte a `webpack` `webpack-cli` `webpack-dev-server` npm balíčky a uveďte je jako závislosti vývoje v těchto prostředích `package.json` :

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```
Vytvořte `index.html` soubor v kořenovém adresáři vašeho projektu. Tento soubor použijeme ke konfiguraci základního rozložení, které umožní uživateli umístit videohovor 1:1.

Zde je kód:
```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - 1:1 Video Calling Sample</title>
</head>

<body>
    <h4>Azure Communication Services</h4>
    <h1>1:1 Video Calling Quickstart</h1>
    <input 
    id="callee-id-input"
    type="text"
    placeholder="Who would you like to call?"
    style="margin-bottom:1em; width: 200px;"
    />

    <div>
    <button id="call-button" type="button" disabled="true">
        start call
    </button>
        &nbsp;
    <button id="hang-up-button" type="button" disabled="true">
        hang up
    </button>
        &nbsp;
    <button id="start-Video" type="button" disabled="true">
        start video
    </button>
        &nbsp;
    <button id="stop-Video" type="button" disabled="true">
        stop video
    </button>     
    </div>

    <div>Local Video</div>
    <div style="height:200px; width:300px; background-color:black; position:relative;">
      <div id="myVideo" style="background-color: black; position:absolute; top:50%; transform: translateY(-50%);">
      </div>     
    </div>
    <div>Remote Video</div>
    <div style="height:200px; width:300px; background-color:black; position:relative;"> 
      <div id="remoteVideo" style="background-color: black; position:absolute; top:50%; transform: translateY(-50%);">
      </div>
    </div>

    <script src="./bundle.js"></script>
</body>
</html>
```

Vytvořte soubor v kořenovém adresáři vašeho projektu `client.js` s názvem, který bude obsahovat aplikační logiku pro tento rychlý Start. Přidejte následující kód pro import volajícího klienta a získejte odkazy na elementy modelu DOM.

```JavaScript
import { CallClient, CallAgent, VideoStreamRenderer, LocalVideoStream } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
const stopVideoButton = document.getElementById("stop-Video");
const startVideoButton = document.getElementById("start-Video");

let placeCallOptions;
let deviceManager;
let localVideoStream;
let rendererLocal;
let rendererRemote;
```
## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce volání sady SDK služby Azure Communications:

| Název      | Description | 
| :---        |    :----   |
| CallClient  | CallClient je hlavní vstupní bod pro volání sady SDK.      |
| CallAgent  | CallAgent se používá ke spouštění a správě volání.        |
| DeviceManager | DeviceManager se používá ke správě mediálních zařízení.    |
| AzureCommunicationTokenCredential | Třída AzureCommunicationTokenCredential implementuje rozhraní CommunicationTokenCredential, které se používá k vytvoření instance CallAgent.        |

## <a name="authenticate-the-client-and-access-devicemanager"></a>Ověření klienta a přístup k DeviceManager

Je třeba nahradit <USER_ACCESS_TOKEN> platným uživatelským tokenem přístupu pro váš prostředek. Pokud ještě nemáte k dispozici token, přečtěte si dokumentaci k tokenu uživatele. Pomocí rozhraní `CallClient` inicializujte `CallAgent` instanci s a, `CommunicationUserCredential` která nám umožní přijímat volání. Aby bylo možné získat přístup k `DeviceManager` instanci callAgent, je třeba nejprve vytvořit. Pak můžete použít `getDeviceManager` metodu na `CallClient` instanci a získat tak `DeviceManager` .

Do souboru `client.js` přidejte následující kód:

```JavaScript
async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, { displayName: 'optional ACS user name' });
    
    deviceManager = await callClient.getDeviceManager();
    callButton.disabled = false;
}
init();
```
## <a name="place-a-11-outgoing-video-call-to-a-user"></a>Vložení volání odchozího videa 1:1 do uživatele

Přidejte naslouchací proces události pro zahájení volání při kliknutí na `callButton` tlačítko:

Nejdřív je potřeba vytvořit výčet místních fotoaparátů pomocí `getCameraList` rozhraní deviceManager API. V tomto rychlém startu používáme první kameru v kolekci. Po výběru požadované kamery bude instance LocalVideoStream vytvořená a předána do `videoOptions` jako položka v rámci pole LocalVideoStream do metody volání. Po připojení se vaše volání automaticky spustí odeslání streamu videa druhému účastníkovi. 

```JavaScript
callButton.addEventListener("click", async () => {
    const videoDevices = await deviceManager.getCameras();
    const videoDeviceInfo = videoDevices[0];
    localVideoStream = new LocalVideoStream(videoDeviceInfo);
    placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};

    localVideoView();
    stopVideoButton.disabled = false;
    startVideoButton.disabled = true;

    const userToCall = calleeInput.value;
    call = callAgent.startCall(
        [{ communicationUserId: userToCall }],
        placeCallOptions
    );

    subscribeToRemoteParticipantInCall(call);

    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```  
Chcete-li vykreslit `LocalVideoStream` , je nutné vytvořit novou instanci `VideoStreamRenderer` a pak vytvořit novou `VideoStreamRendererView` instanci pomocí asynchronní `createView` metody. Pak se můžete připojit `view.target` k libovolnému prvku uživatelského rozhraní. 

```JavaScript
async function localVideoView() {
    rendererLocal = new VideoStreamRenderer(localVideoStream);
    const view = await rendererLocal.createView();
    document.getElementById("myVideo").appendChild(view.target);
}
```
Všichni vzdálení účastníci jsou k dispozici prostřednictvím `remoteParticipants` kolekce v instanci volání. Je nutné naslouchat události, `remoteParticipantsUpdated` která bude upozorněna na přidání nového účastníka do volání. Pro přihlášení k `remoteParticipants` odběru datových proudů videí je také nutné iterace kolekce opakovat. 

```JavaScript
function subscribeToRemoteParticipantInCall(callInstance) {
    callInstance.on('remoteParticipantsUpdated', e => {
        e.added.forEach( p => {
            subscribeToParticipantVideoStreams(p);
        })
    }); 
    callInstance.remoteParticipants.forEach( p => {
        subscribeToParticipantVideoStreams(p);
    })
}
```
Musíte se přihlásit k odběru `videoStreamsUpdated` události a zpracovávat přidané video streamy vzdálených účastníků. Můžete zkontrolovat `videoStreams` kolekce pro výpis datových proudů každého účastníka při přechodu do `remoteParticipants` kolekce aktuálního volání.

```JavaScript
function subscribeToParticipantVideoStreams(remoteParticipant) {
    remoteParticipant.on('videoStreamsUpdated', e => {
        e.added.forEach(v => {
            handleVideoStream(v);
        })
    });
    remoteParticipant.videoStreams.forEach(v => {
        handleVideoStream(v);
    });
}
```
Musíte se přihlásit k odběru `isAvailableChanged` události pro vykreslení `remoteVideoStream` . Pokud se `isAvailable` vlastnost změní na `true` , vzdálený účastník posílá datový proud. Vždy, když se změní dostupnost vzdáleného streamu, můžete zvolit zničení celého celku `Renderer` , jeho konkrétního `RendererView` nebo zachování, ale výsledkem bude zobrazení prázdného snímku videa.
```JavaScript
function handleVideoStream(remoteVideoStream) {
    remoteVideoStream.on('availabilityChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            remoteVideoView(remoteVideoStream);
        } else {
            rendererRemote.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        remoteVideoView(remoteVideoStream);
    }
}
```
Chcete-li vykreslit `RemoteVideoStream` , je nutné vytvořit novou instanci `VideoStreamRenderer` a pak vytvořit novou `VideoStreamRendererView` instanci pomocí asynchronní `createView` metody. Pak se můžete připojit `view.target` k libovolnému prvku uživatelského rozhraní. 

```JavaScript
async function remoteVideoView(remoteVideoStream) {
    rendererRemote = new VideoStreamRenderer(remoteVideoStream);
    const view = await rendererRemote.createView();
    document.getElementById("remoteVideo").appendChild(view.target);
}
```
## <a name="receive-an-incoming-call"></a>Přijetí příchozího volání
Pro zpracování příchozích volání musíte naslouchat `incomingCall` události `callAgent` . Po příchozím volání budete muset vytvořit výčet místních kamer a vytvořit `LocalVideoStream` instanci pro odeslání streamu videa druhému účastníkovi. Pro zpracování vzdálených streamů videa se taky musíte přihlásit k odběru `remoteParticipants` . Můžete přijmout nebo odmítnout volání prostřednictvím `incomingCall` instance. 

Přepněte implementaci do v `init()` pro zpracování příchozích volání. 

```JavaScript
callAgent.on('incomingCall', async e => {
    const videoDevices = await deviceManager.getCameras();
    const videoDeviceInfo = videoDevices[0];
    localVideoStream = new LocalVideoStream(videoDeviceInfo);
    localVideoView();

    stopVideoButton.disabled = false;
    callButton.disabled = true;
    hangUpButton.disabled = false;

    const addedCall = await e.incomingCall.accept({videoOptions: {localVideoStreams:[localVideoStream]}});
    call = addedCall;

    subscribeToRemoteParticipantInCall(addedCall);  
});
```
## <a name="end-the-current-call"></a>Ukončit aktuální volání
Přidat naslouchací proces události pro ukončení aktuálního volání při kliknutí na `hangUpButton` tlačítko:
```JavaScript
hangUpButton.addEventListener("click", async () => {
    // dispose of the renderers
    rendererLocal.dispose();
    rendererRemote.dispose();
    // end the current call
    await call.hangUp();
    // toggle button states
    hangUpButton.disabled = true;
    callButton.disabled = false;
    stopVideoButton.disabled = true;
});
```
## <a name="subscribe-to-call-updates"></a>Přihlášení k odběru aktualizací
Je nutné se přihlásit k odběru události v případě, že vzdálený účastník ukončí volání, aby vygenerovalo vyřazení videa a stavy přepínacího tlačítka. 

Přepněte implementaci do inicializačního rozhraní () pro přihlášení k odběru `callsUpdated` události. 
 ```JavaScript 
callAgent.on('callsUpdated', e => {
    e.removed.forEach(removedCall => {
        // dispose of video renders
        rendererLocal.dispose();
        rendererRemote.dispose();
        // toggle button states
        hangUpButton.disabled = true;
        callButton.disabled = false;
        stopVideoButton.disabled = true;
    })
})
```

## <a name="start-and-end-video-during-the-call"></a>Počáteční a koncové video během volání
Video můžete zastavit během aktuálního volání přidáním naslouchacího procesu události do tlačítka Zastavit video a odstranit tak zobrazovací jednotku `localVideoStream` . 
 ```JavaScript       
stopVideoButton.addEventListener("click", async () => {
    await call.stopVideo(localVideoStream);
    rendererLocal.dispose();
    startVideoButton.disabled = false;
    stopVideoButton.disabled = true;
});
```
Můžete přidat naslouchací proces události na tlačítko Spustit video a zapnout video v době, kdy se zastavilo během aktuálního volání. 
```JavaScript
startVideoButton.addEventListener("click", async () => {
    await call.startVideo(localVideoStream);
    localVideoView();
    stopVideoButton.disabled = false;
    startVideoButton.disabled = true;
});
```
## <a name="run-the-code"></a>Spuštění kódu
Použijte `webpack-dev-server` k sestavení a spuštění vaší aplikace. Spusťte následující příkaz pro vytvoření balíčku aplikace Host na místním serveru:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```
Otevřete prohlížeč a přejděte na http://localhost:8080/ . Měli byste vidět následující:

:::image type="content" source="./media/javascript/1-on-1-video-calling.png" alt-text="1 na 1 stránce volání videa":::

Můžete vytvořit volání 1:1 odchozího videa zadáním ID uživatele v textovém poli a kliknutím na tlačítko spustit volání. 

## <a name="sample-code"></a>Příklad kódu
Ukázkovou aplikaci si můžete stáhnout z [GitHubu](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-video-calling).

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete vyčistit a odebrat předplatné služby Communications Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené. Přečtěte si další informace o [vyčištění prostředků](../create-communication-resource.md?pivots=platform-azp&tabs=windows#clean-up-resources).

## <a name="next-steps"></a>Další kroky
Další informace najdete v následujících článcích:

- Podívejte se na [ukázku našeho webového volání](https://docs.microsoft.com/azure/communication-services/samples/web-calling-sample)
- Další informace o [volání funkcí sady SDK](https://docs.microsoft.com/azure/communication-services/quickstarts/voice-video-calling/calling-client-samples?pivots=platform-web)
- Další informace o [volání funkce](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/about-call-types)

