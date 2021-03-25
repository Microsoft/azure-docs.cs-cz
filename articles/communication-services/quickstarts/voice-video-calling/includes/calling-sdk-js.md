---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 7fee393b694bf761cf052702a975239d6dff9a9c
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105263"
---
## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Nasazený prostředek komunikačních služeb. [Vytvořte prostředek služby Communications](../../create-communication-resource.md).
- Přístupový token uživatele pro povolení volajícího klienta. Další informace najdete v tématu [vytváření a Správa přístupových tokenů](../../access-tokens.md).
- Volitelné: dokončete rychlé zprovoznění a [přidejte do své aplikace hlasové volání](../getting-started-with-calling.md).

## <a name="install-the-client-library"></a>Instalace klientské knihovny

> [!NOTE]
> Tento dokument používá verzi 1.0.0-beta. 6 volání klientské knihovny.

Pomocí `npm install` příkazu můžete nainstalovat volání služby Azure Communication Services a běžné klientské knihovny pro JavaScript.
Tento dokument odkazuje na typy ve verzi 1.0.0-beta. 5 volání knihovny.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save

```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce komunikačních služeb Azure, které volají klientskou knihovnu:

| Název                             | Description                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| `CallClient`                      | Hlavní vstupní bod do volající klientské knihovny.                                                                       |
| `CallAgent`                        | Slouží ke spuštění a správě volání.                                                                                            |
| `DeviceManager`                    | Používá se ke správě mediálních zařízení.                                                                                           |
| `AzureCommunicationTokenCredential` | Implementuje `CommunicationTokenCredential` rozhraní, které se používá k vytvoření instance `callAgent` . |

## <a name="initialize-a-callclient-instance-create-a-callagent-instance-and-access-devicemanager"></a>Inicializace instance CallClient, vytvoření instance CallAgent a přístup k deviceManager

Vytvořte novou `CallClient` instanci. Můžete ji nakonfigurovat s vlastními možnostmi, jako je například instance protokolovacího nástroje.

Pokud máte `CallClient` instanci, můžete vytvořit `CallAgent` instanci voláním `createCallAgent` metody na `CallClient` instanci. Tato funkce asynchronně vrátí `CallAgent` objekt instance.

`createCallAgent`Metoda používá `CommunicationTokenCredential` jako argument. Přijímá [token přístupu uživatele](../../access-tokens.md).

Po vytvoření `callAgent` instance můžete k `getDeviceManager` přístupu použít metodu z `CallClient` instance `deviceManager` .

```js
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-a-call"></a>Umístit volání

Chcete-li vytvořit a spustit volání, použijte jedno z rozhraní API v `callAgent` a zadejte uživatele, kterého jste vytvořili prostřednictvím klientské knihovny služby Communications identity.

Vytvoření a spuštění volání jsou synchronní. Instance volání umožňuje přihlásit se k odběru událostí volání.

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>Nakonání volání 1: n uživateli nebo veřejné telefonní sítě

Chcete-li volat jiného uživatele komunikačních služeb, použijte `startCall` metodu na `callAgent` a předejte příjemce `CommunicationUserIdentifier` , kterého jste [vytvořili pomocí knihovny pro správu komunikačních služeb](../../access-tokens.md).

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

Pokud chcete zavolat do veřejné komutované telefonní sítě (PSTN), použijte `startCall` metodu `callAgent` a předejte ji příjemci `PhoneNumberIdentifier` . Aby bylo možné volat do veřejné telefonní služby, musí být prostředek komunikačních služeb nakonfigurovaný.

Pokud voláte číslo veřejné telefonní číslo, zadejte své alternativní ID volajícího. Alternativním ID volajícího je telefonní číslo (založené na standardu E. 164), které identifikuje volajícího ve volání ze sítě PSTN. Toto je telefonní číslo, které příjemce hovorů uvidí pro příchozí volání.

> [!NOTE]
> Volání veřejné telefonní sítě je aktuálně v privátní verzi Preview. Pro přístup [platí pro program předčasného](https://aka.ms/ACS-EarlyAdopter)připravení.

Pro volání 1:1 použijte následující kód:

```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

Pro volání 1: n použijte následující kód:

```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>Vložení volání 1:1 s videokamerou

> [!IMPORTANT]
> V tuto chvíli nemůže být k dispozici více než jeden odchozí datový proud v místním videu.

Chcete-li umístit audiovizuální volání, je nutné zadat své kamery pomocí `getCameras()` metody v `deviceManager` .

Po výběru kamery ji použijte k vytvoření `LocalVideoStream` instance. Předat do `videoOptions` jako položku v rámci `localVideoStream` pole do `startCall` metody.

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
videoDeviceInfo = cameras[0];
localVideoStream = new LocalVideoStream(videoDeviceInfo);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

Po připojení se volání automaticky spustí odeslání streamu videa z vybrané kamery druhému účastníkovi. To platí také pro `Call.Accept()` Možnosti videa a `CallAgent.join()` Možnosti videa.

### <a name="join-a-group-call"></a>Připojit se k volání skupiny

Chcete-li spustit nové volání skupiny nebo se připojit k průběžnému volání skupiny, použijte `join` metodu a předejte objekt s `groupId` vlastností. `groupId`Hodnota musí být identifikátor GUID.

```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```

### <a name="join-a-teams-meeting"></a>Připojit se k týmům na schůzce

Chcete-li se připojit k týmu, použijte `join` metodu a předejte odkaz na schůzku nebo souřadnice.

Připojte se pomocí odkazu na schůzku:

```js
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);
```

Připojte se pomocí souřadnic schůzky:

```js
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receive-an-incoming-call"></a>Přijetí příchozího volání

`callAgent`Instance generuje `incomingCall` událost, když přihlášená identita obdrží příchozí volání. Chcete-li naslouchat této události, přihlaste se k odběru pomocí jedné z následujících možností:

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    //Get information about caller
    var callerInfo = incomingCall.callerInfo

    //Accept the call
    var call = await incomingCall.accept();

    //Reject the call
    incomingCall.reject();
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

`incomingCall`Událost zahrnuje `incomingCall` instanci, kterou můžete přijmout nebo odmítnout.

## <a name="manage-calls"></a>Spravovat volání

Během volání můžete získat přístup k vlastnostem volání a spravovat nastavení videa a zvuku.

### <a name="check-call-properties"></a>Ověřit vlastnosti volání

Získání jedinečného ID (řetězce) pro volání:

   ```js
    const callId: string = call.id;
   ```

Přečtěte si o ostatních účastnících volání kontrolou `remoteParticipant` kolekce:

   ```js
   const remoteParticipants = call.remoteParticipants;
   ```

Identifikace volajícího příchozího volání:

   ```js
   const callerIdentity = call.callerInfo.identifier;
   ```

   `identifier` je jedním z `CommunicationIdentifier` typů.

Získat stav volání:

   ```js
   const callState = call.state;
   ```

   Vrátí řetězec představující aktuální stav volání:

  - `None`: Počáteční stav volání.
  - `Incoming`: Označuje, že volání je příchozí. Musí být buď přijatý, nebo odmítnutý.
  - `Connecting`: Počáteční přechodový stav při umístění nebo přijetí volání.
  - `Ringing`: Pro odchozí volání indikuje, že volání pro vzdálené účastníky je cyklické. Je `Incoming` na své straně.
  - `EarlyMedia`: Označuje stav, ve kterém je přehráno oznámení před připojením volání.
  - `Connected`: Označuje, že je volání připojeno.
  - `LocalHold`: Označuje, že volání je blokováno místním účastníkem. Mezi místním koncovým bodem a vzdálenými účastníky není natékání žádného média.
  - `RemoteHold`: Označuje, že volání bylo blokováno vzdáleným účastníkem. Mezi místním koncovým bodem a vzdálenými účastníky není natékání žádného média.
  - `Disconnecting`: Přechodový stav před tím, než volání přejde do `Disconnected` stavu.
  - `Disconnected`: Konečný stav volání. Pokud dojde ke ztrátě síťového připojení, stav se změní na `Disconnected` po dvou minutách.

Zjistěte, proč bylo volání ukončeno kontrolou `callEndReason` vlastnosti:

   ```js
   const callEndReason = call.callEndReason;
   // callEndReason.code (number) code associated with the reason
   // callEndReason.subCode (number) subCode associated with the reason
   ```

Zjistěte, zda je aktuální volání příchozí nebo odchozí `direction` . Zkontrolujte vlastnost. Vrátí `CallDirection` .

  ```js
   const isIncoming = call.direction == 'Incoming';
   const isOutgoing = call.direction == 'Outgoing';
   ```

Zkontroluje, jestli je aktuální mikrofon ztlumený. Vrátí `Boolean` .

   ```js
   const muted = call.isMicrophoneMuted;
   ```

Zjistíte, jestli se datový proud pro sdílení obrazovky odesílá z daného koncového bodu, a to tak, že zkontrolujete jeho `isScreenSharingOn` vlastnost. Vrátí `Boolean` .

   ```js
   const isScreenSharingOn = call.isScreenSharingOn;
   ```

Zkontrolujte aktivní streamy videa kontrolou `localVideoStreams` kolekce. Vrací `LocalVideoStream` objekty.

   ```js
   const localVideoStreams = call.localVideoStreams;
   ```

### <a name="check-a-callended-event"></a>Ověřit událost callEnded

`call`Instance emituje událost v `callEnded` případě, že volání skončí. Chcete-li naslouchat této události, přihlaste se k odběru pomocí následujícího kódu:

```js
const callEndHander = async (args: { callEndReason: CallEndReason }) => {
    console.log(args.callEndReason)
};

call.on('callEnded', callEndHander);
```

### <a name="mute-and-unmute"></a>Ztlumení a ztlumení

Chcete-li ztlumit nebo zrušit ztlumení místního koncového bodu, můžete použít `mute` `unmute` rozhraní API a asynchronní:

```js

//mute local device
await call.mute();

//unmute local device
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>Spuštění a zastavení odesílání místního videa

Chcete-li spustit video, je nutné zadat kamery pomocí `getCameras` metody `deviceManager` objektu. Pak vytvořte novou instanci `LocalVideoStream` předáním požadované kamery do `startVideo` metody jako argument:

```js
const localVideoStream = new LocalVideoStream(videoDeviceInfo);
await call.startVideo(localVideoStream);
```

Po úspěšném spuštění odesílání videa `LocalVideoStream` je instance přidána do `localVideoStreams` kolekce v instanci volání.

```js
call.localVideoStreams[0] === localVideoStream;
```

Pokud chcete zastavit místní video, předejte `localVideoStream` instanci, která je k dispozici v `localVideoStreams` kolekci:

```js
await call.stopVideo(localVideoStream);
```

V případě, že se video posílá vyvoláním na instanci, můžete přepnout na jiné kamery `switchSource` `localVideoStream` .

```js
const cameras = await callClient.getDeviceManager().getCameras();
localVideoStream.switchSource(cameras[1]);
```

## <a name="manage-remote-participants"></a>Správa vzdálených účastníků

Všichni vzdálení účastníci jsou zastoupeni `remoteParticipant` a jsou k dispozici prostřednictvím `remoteParticipants` kolekce v instanci volání.

### <a name="list-the-participants-in-a-call"></a>Výpis účastníků ve volání

`remoteParticipants`Kolekce vrátí seznam vzdálených účastníků ve volání:

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
```

### <a name="access-remote-participant-properties"></a>Přístup k vlastnostem vzdáleného účastníka

Vzdálení účastníci mají sadu přidružených vlastností a kolekcí:

- `CommunicationIdentifier`: Získá identifikátor vzdáleného účastníka. Identita je jedním z těchto `CommunicationIdentifier` typů:

  ```js
  const identifier = remoteParticipant.identifier;
  ```

  Může to být jeden z následujících `CommunicationIdentifier` typů:

  - `{ communicationUserId: '<ACS_USER_ID'> }`: Objekt představující uživatele ACS.
  - `{ phoneNumber: '<E.164>' }`: Objekt představující telefonní číslo ve formátu E. 164.
  - `{ microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" }`: Object představující uživatele týmů.

- `state`: Získá stav vzdáleného účastníka.

  ```js
  const state = remoteParticipant.state;
  ```

  Stav může být:

  - `Idle`: Počáteční stav.
  - `Connecting`: Přechodový stav při připojování účastníka k volání.
  - `Ringing`: Účastník je Ring.
  - `Connected`: Účastník je připojen ke volání.
  - `Hold`: Účastník je podržený.
  - `EarlyMedia`: Oznámení, které se přehrává před tím, než se účastník připojí k volání.
  - `Disconnected`: Konečný stav. Účastník je odpojen od volání. Pokud vzdálený účastník ztratí své připojení k síti, jejich stav se změní na `Disconnected` po dvou minutách.

- `callEndReason`: Chcete-li zjistit, proč účastník opustil hovor, ověřte `callEndReason` vlastnost:

  ```js
  const callEndReason = remoteParticipant.callEndReason;
  // callEndReason.code (number) code associated with the reason
  // callEndReason.subCode (number) subCode associated with the reason
  ```

- `isMuted` stav: Chcete-li zjistit, zda je vzdálený účastník ztlumený, ověřte `isMuted` vlastnost. Vrátí `Boolean` .

  ```js
  const isMuted = remoteParticipant.isMuted;
  ```

- `isSpeaking` stav: Chcete-li zjistit, zda vzdálený účastník mluví, ověřte `isSpeaking` vlastnost. Vrátí `Boolean` .

  ```js
  const isSpeaking = remoteParticipant.isSpeaking;
  ```

- `videoStreams`: Chcete-li zkontrolovat všechny streamy videa, které daný účastník odesílá v rámci tohoto volání, zkontrolujte `videoStreams` kolekci. Obsahuje `RemoteVideoStream` objekty.

  ```js
  const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]
  ```

### <a name="add-a-participant-to-a-call"></a>Přidání účastníka do volání

Chcete-li přidat účastníka (buď uživatele nebo telefonní číslo) k volání, můžete použít `addParticipant` . Zadejte jeden z `Identifier` typů. Vrátí `remoteParticipant` instanci.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>Odebrání účastníka z volání

Chcete-li z volání odebrat účastníka (buď uživatele, nebo telefonní číslo), můžete vyvolat `removeParticipant` . Musíte předat jeden z těchto `Identifier` typů. Tím se vyřeší asynchronní po odebrání účastníka z volání. Účastník je také odebrán z `remoteParticipants` kolekce.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>Vykreslovat streamy videa vzdáleného účastníka

Pokud chcete zobrazit seznam streamů a sdílení obrazovky pro vzdálené účastníky, Prozkoumejte tyto `videoStreams` kolekce:

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.mediaStreamType;
```

Aby bylo možné vykreslit `RemoteVideoStream` , je nutné se přihlásit k odběru `isAvailableChanged` události. Pokud se `isAvailable` vlastnost změní na `true` , vzdálený účastník posílá datový proud. Poté vytvořte novou instanci `Renderer` a pak vytvořte novou `RendererView` instanci pomocí asynchronní `createView` metody.  Pak se můžete připojit `view.target` k libovolnému prvku uživatelského rozhraní.

Když se změní dostupnost vzdáleného datového proudu, můžete odstranit `Renderer` , zničit konkrétní `RendererView` instanci nebo zachovat vše. Zobrazovací jednotky připojené k nedostupnému datovému proudu budou mít za následek prázdný rámec videa.

```js
function subscribeToRemoteVideoStream(remoteVideoStream: RemoteVideoStream) {
    let renderer: Renderer = new Renderer(remoteVideoStream);
    const displayVideo = () => {
        const view = await renderer.createView();
        htmlElement.appendChild(view.target);
    }
    remoteVideoStream.on('availabilityChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            displayVideo();
        } else {
            renderer.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        displayVideo();
    }
}
```

### <a name="remote-video-stream-properties"></a>Vlastnosti vzdáleného streamu videa

Proudy vzdálených videí mají následující vlastnosti:

- `id`: ID vzdáleného streamu videa.

  ```js
  const id: number = remoteVideoStream.id;
  ```

- `Stream.size`: Výška a šířka vzdáleného streamu videa.

  ```js
  const size: {width: number; height: number} = remoteVideoStream.size;
  ```

- `mediaStreamType`: Může být `Video` nebo `ScreenSharing` .

  ```js
  const type: MediaStreamType = remoteVideoStream.mediaStreamType;
  ```

- `isAvailable`: Zda koncový bod vzdáleného účastníka aktivně odesílá datový proud.

  ```js
  const type: boolean = remoteVideoStream.isAvailable;
  ```

### <a name="renderer-methods-and-properties"></a>Metody a vlastnosti vykreslovacího modulu

Vytvořte `rendererView` instanci, kterou lze připojit v uživatelském rozhraní aplikace pro vykreslení vzdáleného streamu videa:

  ```js
  renderer.createView()
  ```

Dispose `renderer` a všechny přidružené `rendererView` instance:

  ```js
  renderer.dispose()
  ```

### <a name="rendererview-methods-and-properties"></a>Metody a vlastnosti RendererView

Při vytváření `rendererView` můžete zadat `scalingMode` `isMirrored` vlastnosti a. `scalingMode` může být `Stretch` , `Crop` , nebo `Fit` . Je `isMirrored` -li parametr zadán, vykreslený datový proud je vrácen svisle.

```js
const rendererView: RendererView = renderer.createView({ scalingMode, isMirrored });
```

Každá `RendererView` instance má `target` vlastnost, která představuje plochu vykreslování. Připojte tuto vlastnost v uživatelském rozhraní aplikace:

```js
document.body.appendChild(rendererView.target);
```

Můžete aktualizovat voláním `scalingMode` `updateScalingMode` metody:

```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Správa zařízení

V nástroji `deviceManager` můžete zadat místní zařízení, která můžou přenášet vaše audio a video streamy ve volání. Pomůže vám taky požádat o oprávnění k přístupu k mikrofonu a kameře jiného uživatele pomocí rozhraní API nativního prohlížeče.

Můžete získat přístup `deviceManager` voláním `callClient.getDeviceManager()` metody:

> [!IMPORTANT]
> Musíte mít objekt, abyste `callAgent` mohli získat přístup `deviceManager` .

```js
const deviceManager = await callClient.getDeviceManager();
```

### <a name="get-local-devices"></a>Získat místní zařízení

Pro přístup k místním zařízením můžete použít metody výčtu v `deviceManager` .

```js
//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-the-default-microphone-and-speaker"></a>Nastavení výchozího mikrofonu a mluvčího

V nástroji `deviceManager` můžete nastavit výchozí zařízení, které použijete ke spuštění volání. Pokud nejsou nastavené výchozí hodnoty klienta, služba Communication Services použije výchozí nastavení operačního systému.

```js
// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(AudioDeviceInfo);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(AudioDeviceInfo);
```

### <a name="local-camera-preview"></a>Místní kamera verze Preview

`deviceManager` `Renderer` K zahájení vykreslování datových proudů z místní kamery můžete použít a. Tento datový proud se nebude posílat jiným účastníkům; je to místní kanál verze Preview.

```js
const cameras = await deviceManager.getCameras();
const localVideoDevice = cameras[0];
const localCameraStream = new LocalVideoStream(localVideoDevice);
const renderer = new Renderer(localCameraStream);
const view = await renderer.createView();
document.body.appendChild(view.target);

```

### <a name="request-permission-to-camera-and-microphone"></a>Požádat o oprávnění ke kameře a mikrofonu

Vyzvat uživatele k udělení oprávnění pro fotoaparát a mikrofon:

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```

To se vyřeší s objektem, který určuje, jestli `audio` a `video` oprávnění byla udělena:

```js
console.log(result.audio);
console.log(result.video);
```

## <a name="record-calls"></a>Volání záznamů

[!INCLUDE [Private Preview Notice](../../../includes/private-preview-include-section.md)]

Záznam volání je rozšířenou funkcí základního `Call` rozhraní API. Nejdřív musíte získat objekt rozhraní API pro nahrávání funkcí:

```js
const callRecordingApi = call.api(Features.Recording);
```

Potom Chcete-li zkontrolovat, zda je hovor zaznamenáván, zkontrolujte `isRecordingActive` vlastnost `callRecordingApi` . Vrátí `Boolean` .

```js
const isResordingActive = callRecordingApi.isRecordingActive;
```

Můžete se také přihlásit k odběru změn záznamů:

```js
const isRecordingActiveChangedHandler = () => {
  console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);

```

## <a name="transfer-calls"></a>Přenos volání

Přenos volání je rozšířená funkce základního `Call` rozhraní API. Nejdřív musíte získat objekt rozhraní API pro přenos funkcí:

```js
const callTransferApi = call.api(Features.Transfer);
```

Přenosy volání zahrnují tři strany:

- *Přenosový* uživatel: osoba, která zahájí žádost o přenos.
- *Nabyvatel*: osoba, která se přenáší.
- *Cíl přenosu*: osoba, která se přenáší do.

Přenosy postupujte podle následujících kroků:

1. Mezi *přenosem* a *nabyvatelem* již existuje připojené volání. *Přenos* se rozhodne přenést volání z *nabyvatele* do *cíle přenosu*.
1. *Přenos* volá `transfer` rozhraní API.
1. *Nabyvatel* rozhodne, zda `accept` nebo požadavek na `reject` přenos do *cíle přenosu* pomocí `transferRequested` události.
1. *Cíl přenosu* přijme příchozí volání pouze v případě, že *nabyvatel* přijme požadavek na přenos.

Pro přenos aktuálního volání můžete použít `transfer` rozhraní API. `transfer` vybere volitelnou `transferCallOptions` možnost, která vám umožní nastavit `disableForwardingAndUnanswered` příznak:

- `disableForwardingAndUnanswered = false`: Pokud *cíl přenosu* neodpoví za volání přenosu, přenos se řídí přesměrováním *cíle přenosu* a nezodpovězenými nastaveními.
- `disableForwardingAndUnanswered = true`: Pokud *cíl přenosu* neodpoví na hovor přenosu, skončí pokus o přenos.

```js
// transfer target can be an ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

`transfer`Rozhraní API umožňuje přihlásit se k odběru `transferStateChanged` `transferRequested` událostí a. `transferRequested`Událost přichází z `call` instance `transferStateChanged` . událost a přenos `state` a `error` pocházejí z `transfer` instance.

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if a transfer request failed
```

*Nabyvatel* může přijmout nebo odmítnout žádost o přenos iniciované *přenosem* v `transferRequested` události pomocí `accept()` nebo `reject()` `transferRequestedEventArgs` . Můžete získat přístup k `targetParticipant` informacím `accept` a `reject` metodám v nástroji `transferRequestedEventArgs` .

```js
// Transferee to accept the transfer request
callTransferApi.on('transferRequested', args => {
  args.accept();
});

// Transferee to reject the transfer request
callTransferApi.on('transferRequested', args => {
  args.reject();
});
```

## <a name="learn-about-eventing-models"></a>Další informace o modelech událostí

Zkontrolujte aktuální hodnoty a přihlaste se k odběru událostí aktualizace pro budoucí hodnoty.

### <a name="properties"></a>Vlastnosti

```js
// Inspect the current value
console.log(object.property);

// Subscribe to value updates
object.on('propertyChanged', () => {
    // Inspect new value
    console.log(object.property)
});

// Unsubscribe from updates:
object.off('propertyChanged', () => {});



// Example for inspecting a call state
console.log(call.state);
call.on('stateChanged', () => {
    console.log(call.state);
});
call.off('stateChanged', () => {});
```

### <a name="collections"></a>Kolekce

```js
// Inspect the current collection
object.collection.forEach(v => {
    console.log(v);
});

// Subscribe to collection updates
object.on('collectionUpdated', e => {
    // Inspect new values added to the collection
    e.added.forEach(v => {
        console.log(v);
    });
    // Inspect values removed from the collection
    e.removed.forEach(v => {
        console.log(v);
    });
});

// Unsubscribe from updates:
object.off('collectionUpdated', () => {});

// Example for subscribing to remote participants and their video streams
call.remoteParticipants.forEach(p => {
    subscribeToRemoteParticipant(p);
})

call.on('remoteParticipantsUpdated', e => {
    e.added.forEach(p => { subscribeToRemoteParticipant(p) })
    e.removed.forEach(p => { unsubscribeFromRemoteParticipant(p) })
});

function subscribeToRemoteParticipant(p) {
    console.log(p.state);
    p.on('stateChanged', () => { console.log(p.state); });
    p.videoStreams.forEach(v => { subscribeToRemoteVideoStream(v) });
    p.on('videoStreamsUpdated', e => { e.added.forEach(v => { subscribeToRemoteVideoStream(v) }) })
}
```