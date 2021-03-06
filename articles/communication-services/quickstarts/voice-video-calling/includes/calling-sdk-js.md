---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: f20099943d3cfa3dd4afc161c26e5582e467ca8d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589951"
---
## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Nasazený prostředek komunikačních služeb. [Vytvořte prostředek služby Communications](../../create-communication-resource.md).
- Přístupový token uživatele pro povolení volajícího klienta. Další informace najdete v tématu [vytváření a Správa přístupových tokenů](../../access-tokens.md).
- Volitelné: dokončete rychlé zprovoznění a [přidejte do své aplikace hlasové volání](../getting-started-with-calling.md).

## <a name="install-the-sdk"></a>Instalace sady SDK

> [!NOTE]
> Tento dokument používá volání webové sady ACS služby ACS.

Pomocí `npm install` příkazu můžete nainstalovat volání a běžné sady SDK komunikačních služeb Azure pro JavaScript.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save

```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce volání sady SDK služby Azure Communications:

| Název                             | Description                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| `CallClient`                      | Hlavní vstupní bod pro volání sady SDK.                                                                       |
| `CallAgent`                        | Slouží ke spuštění a správě volání.                                                                                            |
| `DeviceManager`                    | Používá se ke správě mediálních zařízení.                                                                                           |
| `AzureCommunicationTokenCredential` | Implementuje `CommunicationTokenCredential` rozhraní, které se používá k vytvoření instance `callAgent` . |

## <a name="initialize-a-callclient-instance-create-a-callagent-instance-and-access-devicemanager"></a>Inicializace instance CallClient, vytvoření instance CallAgent a přístup k deviceManager

Vytvořte novou `CallClient` instanci. Můžete ji nakonfigurovat s vlastními možnostmi, jako je například instance protokolovacího nástroje.

Pokud máte `CallClient` instanci, můžete vytvořit `CallAgent` instanci voláním `createCallAgent` metody na `CallClient` instanci. Tato funkce asynchronně vrátí `CallAgent` objekt instance.

`createCallAgent`Metoda používá `CommunicationTokenCredential` jako argument. Přijímá [token přístupu uživatele](../../access-tokens.md).

`getDeviceManager`Pro přístup k aplikaci můžete použít metodu z `CallClient` instance `deviceManager` .

```js
// Set the logger's log level
setLogLevel('verbose');
// Redirect logger output to wherever desired. By default it logs to console
AzureLogger.log = (...args) => { console.log(...args) };
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-a-call"></a>Umístit volání

Chcete-li vytvořit a spustit volání, použijte jedno z rozhraní API v `callAgent` a zadejte uživatele, kterého jste vytvořili prostřednictvím sady SDK služby Communications identity.

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

Chcete-li umístit audiovizuální volání, je nutné vytvořit výčet místních kamer pomocí `getCameras()` metody v `deviceManager` .

Po výběru kamery ji použijte k vytvoření `LocalVideoStream` instance. Předat do `videoOptions` jako položku v rámci `localVideoStream` pole do `startCall` metody.


```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
localVideoStream = new LocalVideoStream(camera);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

Po připojení se volání automaticky spustí odeslání streamu videa z vybrané kamery druhému účastníkovi. To platí také pro `Call.Accept()` Možnosti videa a `CallAgent.join()` Možnosti videa.

### <a name="join-a-group-call"></a>Připojit se k volání skupiny

> [!NOTE]
> `groupId`Parametr je považován za systémová metadata a může ho společnost Microsoft používat pro operace, které jsou nutné ke spuštění systému. Do hodnoty Nezahrnovat osobní údaje `groupId` . Microsoft nepovažuje tento parametr za osobní údaje a jeho obsah může být viditelný pro zaměstnance Microsoftu nebo pro dlouhodobě uložené dlouhodobé služby.
>
> `groupId`Parametr vyžaduje, aby data byla ve formátu identifikátoru GUID. Doporučujeme používat náhodně generované identifikátory GUID, které nejsou považovány za osobní údaje v systémech.
>

Chcete-li spustit nové volání skupiny nebo se připojit k průběžnému volání skupiny, použijte `join` metodu a předejte objekt s `groupId` vlastností. `groupId`Hodnota musí být identifikátor GUID.

```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```

### <a name="join-a-teams-meeting"></a>Připojit se k týmům na schůzce
> [!NOTE]
> Toto rozhraní API se poskytuje jako verze Preview pro vývojáře a může se změnit na základě zpětné vazby, kterou dostaneme. Nepoužívejte toto rozhraní API v produkčním prostředí. Pokud chcete používat toto rozhraní API, použijte prosím verzi beta volání služby ACS, která volá web SDK.

Chcete-li se připojit k týmu, použijte `join` metodu a předejte odkaz na schůzku nebo souřadnice schůzky.

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
    const incomingCall = args.incomingCall; 
    // Get incoming call ID
    var incomingCallId = incomingCall.id
    // Get information about this Call. This API is provided as a preview for developers
    // and may change based on feedback that we receive. Do not use this API in a production environment.
    // To use this api please use 'beta' release of ACS Calling Web SDK
    var callInfo = incomingCall.info;

    // Get information about caller
    var callerInfo = incomingCall.callerInfo

    // Accept the call
    var call = await incomingCall.accept();

    // Reject the call
    incomingCall.reject();

    // Subscribe to callEnded event and get the call end reason
     incomingCall.on('callEnded', args => {
        console.log(args.callEndReason);
    });

    // callEndReason is also a property of IncomingCall
    var callEndReason = incomingCall.callEndReason;
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
Získat informace o volání:
> [!NOTE]
> Toto rozhraní API se poskytuje jako verze Preview pro vývojáře a může se změnit na základě zpětné vazby, kterou dostaneme. Nepoužívejte toto rozhraní API v produkčním prostředí. Pokud chcete používat toto rozhraní API, použijte prosím verzi beta volání služby ACS, která volá web SDK.
   ```js
   const callInfo = call.info;
   ```

Přečtěte si o ostatních účastnících volání kontrolou `remoteParticipants` kolekce na instanci Call:

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
  - `Connecting`: Počáteční přechodový stav při umístění nebo přijetí volání.
  - `Ringing`: Pro odchozí volání indikuje, že volání pro vzdálené účastníky je cyklické. Je `Incoming` na své straně.
  - `EarlyMedia`: Označuje stav, ve kterém je přehráno oznámení před připojením volání.
  - `Connected`: Označuje, že je volání připojeno.
  - `LocalHold`: Označuje, že volání je blokováno místním účastníkem. Mezi místním koncovým bodem a vzdálenými účastníky není natékání žádného média.
  - `RemoteHold`: Označuje, že volání bylo blokováno vzdáleným účastníkem. Mezi místním koncovým bodem a vzdálenými účastníky není natékání žádného média.
  - `InLobby`: Určuje, zda je uživatel v předsálí.
  - `Disconnecting`: Přechodový stav před tím, než volání přejde do `Disconnected` stavu.
  - `Disconnected`: Konečný stav volání. Pokud dojde ke ztrátě síťového připojení, stav se změní na `Disconnected` po dvou minutách.

Zjistěte, proč bylo volání ukončeno kontrolou `callEndReason` vlastnosti:

   ```js
   const callEndReason = call.callEndReason;
   const callEndReasonCode = callEndReason.code // (number) code associated with the reason
   const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
   ```

Zjistěte, zda je aktuální volání příchozí nebo odchozí `direction` . Zkontrolujte vlastnost. Vrátí `CallDirection` .

  ```js
   const isIncoming = call.direction == 'Incoming';
   const isOutgoing = call.direction == 'Outgoing';
   ```

Zkontroluje, jestli je aktuální mikrofon ztlumený. Vrátí `Boolean` .

   ```js
   const muted = call.isMuted;
   ```

Zjistíte, jestli se datový proud pro sdílení obrazovky odesílá z daného koncového bodu, a to tak, že zkontrolujete jeho `isScreenSharingOn` vlastnost. Vrátí `Boolean` .

   ```js
   const isScreenSharingOn = call.isScreenSharingOn;
   ```

Zkontrolujte aktivní streamy videa kontrolou `localVideoStreams` kolekce. Vrací `LocalVideoStream` objekty.

   ```js
   const localVideoStreams = call.localVideoStreams;
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

Chcete-li spustit video, je nutné vytvořit výčet kamer pomocí `getCameras` metody `deviceManager` objektu. Pak vytvořte novou instanci `LocalVideoStream` s požadovanou kamerou a potom předejte `LocalVideoStream` objekt do `startVideo` metody:

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
const localVideoStream = new LocalVideoStream(camera);
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
const camera = cameras[1];
localVideoStream.switchSource(camera);
```

## <a name="manage-remote-participants"></a>Správa vzdálených účastníků

Všichni vzdálení účastníci jsou zastoupeni podle `RemoteParticipant` typu a jsou k dispozici prostřednictvím `remoteParticipants` kolekce na instanci volání.

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
  - `{ id: string }`: repredenting identifikátor objektu, který nevyhovuje žádnému z ostatních typů identifikátorů

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
  - `InLobby`: Označuje, že vzdálený účastník je v předsálí.
  - `Disconnected`: Konečný stav. Účastník je odpojen od volání. Pokud vzdálený účastník ztratí své připojení k síti, jejich stav se změní na `Disconnected` po dvou minutách.

- `callEndReason`: Chcete-li zjistit, proč účastník opustil hovor, ověřte `callEndReason` vlastnost:

  ```js
  const callEndReason = remoteParticipant.callEndReason;
  const callEndReasonCode = callEndReason.code // (number) code associated with the reason
  const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
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
- `displayName`: Pro získání zobrazovaného jména pro tohoto vzdáleného účastníka Zkontrolujte vlastnost, která `displayName` vrací řetězec. 

  ```js
  const displayName = remoteParticipant.displayName;
  ```

### <a name="add-a-participant-to-a-call"></a>Přidání účastníka do volání

Chcete-li přidat účastníka (buď uživatele nebo telefonní číslo) k volání, můžete použít `addParticipant` . Zadejte jeden z `Identifier` typů. Synchronně vrátí `remoteParticipant` instanci. `remoteParticipantsUpdated`Událost z volání je vyvolána při úspěšném přidání účastníka do volání.

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

Aby bylo možné vykreslit `RemoteVideoStream` , je nutné se přihlásit k odběru `isAvailableChanged` události. Pokud se `isAvailable` vlastnost změní na `true` , vzdálený účastník posílá datový proud. Poté vytvořte novou instanci `VideoStreamRenderer` a pak vytvořte novou `VideoStreamRendererView` instanci pomocí asynchronní `createView` metody.  Pak se můžete připojit `view.target` k libovolnému prvku uživatelského rozhraní.

Vždy, když se změní dostupnost vzdáleného streamu, můžete zvolit zničení celého celku `VideoStreamRenderer` , jeho konkrétního `VideoStreamRendererView` nebo zachování, ale výsledkem bude zobrazení prázdného snímku videa.

```js
function subscribeToRemoteVideoStream(remoteVideoStream: RemoteVideoStream) {
    let videoStreamRenderer: VideoStreamRenderer = new VideoStreamRenderer(remoteVideoStream);
    const displayVideo = () => {
        const view = await videoStreamRenderer.createView();
        htmlElement.appendChild(view.target);
    }
    remoteVideoStream.on('isAvailableChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            displayVideo();
        } else {
            videoStreamRenderer.dispose();
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

- `mediaStreamType`: Může být `Video` nebo `ScreenSharing` .

  ```js
  const type: MediaStreamType = remoteVideoStream.mediaStreamType;
  ```

- `isAvailable`: Zda koncový bod vzdáleného účastníka aktivně odesílá datový proud.

  ```js
  const type: boolean = remoteVideoStream.isAvailable;
  ```

### <a name="videostreamrenderer-methods-and-properties"></a>Metody a vlastnosti VideoStreamRenderer
Vytvořte `VideoStreamRendererView` instanci, která může být připojena v uživatelském rozhraní aplikace pro vykreslení vzdáleného streamu videa, použijte asynchronní `createView()` metodu, která se vyřeší, když je datový proud připraven k vykreslení a vrátí objekt s `target` vlastností, která představuje `video` prvek, který lze připojit kdekoli ve stromu modelu DOM.

  ```js
  videoStreamRenderer.createView()
  ```

Dispose `videoStreamRenderer` a všechny přidružené `VideoStreamRendererView` instance:

  ```js
  videoStreamRenderer.dispose()
  ```

### <a name="videostreamrendererview-methods-and-properties"></a>Metody a vlastnosti VideoStreamRendererView

Když vytvoříte `VideoStreamRendererView` , můžete zadat `scalingMode` `isMirrored` vlastnosti a. `scalingMode` může být `Stretch` , `Crop` , nebo `Fit` . Je `isMirrored` -li parametr zadán, vykreslený datový proud je vrácen svisle.

```js
const videoStreamRendererView: VideoStreamRendererView = await videoStreamRenderer.createView({ scalingMode, isMirrored });
```

Každá `VideoStreamRendererView` instance má `target` vlastnost, která představuje plochu vykreslování. Připojte tuto vlastnost v uživatelském rozhraní aplikace:

```js
htmlElement.appendChild(view.target);
```

Můžete aktualizovat voláním `scalingMode` `updateScalingMode` metody:

```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Správa zařízení

V nástroji `deviceManager` můžete vytvořit výčet místních zařízení, která můžou přenášet vaše audio a video streamy ve volání. Můžete ji také použít k vyžádání oprávnění pro přístup k mikrofonům a fotoaparátům místního zařízení.

Můžete získat přístup `deviceManager` voláním `callClient.getDeviceManager()` metody:

```js
const deviceManager = await callClient.getDeviceManager();
```

### <a name="get-local-devices"></a>Získat místní zařízení

Pro přístup k místním zařízením můžete použít metody výčtu v `deviceManager` . Výčet je asynchronní akce.

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
await deviceManager.selectMicrophone(localMicrophones[0]);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(localSpeakers[0]);
```

### <a name="local-camera-preview"></a>Místní kamera verze Preview

`deviceManager` `VideoStreamRenderer` K zahájení vykreslování datových proudů z místní kamery můžete použít a. Tento datový proud se nebude posílat jiným účastníkům; je to místní kanál verze Preview.

```js
const cameras = await deviceManager.getCameras();
const camera = cameras[0];
const localCameraStream = new LocalVideoStream(camera);
const videoStreamRenderer = new VideoStreamRenderer(localCameraStream);
const view = await videoStreamRenderer.createView();
htmlElement.appendChild(view.target);

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
> [!NOTE]
> Toto rozhraní API se poskytuje jako verze Preview pro vývojáře a může se změnit na základě zpětné vazby, kterou dostaneme. Nepoužívejte toto rozhraní API v produkčním prostředí. Pokud chcete používat toto rozhraní API, použijte prosím verzi beta volání služby ACS, která volá web SDK.

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
> [!NOTE]
> Toto rozhraní API se poskytuje jako verze Preview pro vývojáře a může se změnit na základě zpětné vazby, kterou dostaneme. Nepoužívejte toto rozhraní API v produkčním prostředí. Pokud chcete používat toto rozhraní API, použijte prosím verzi beta volání služby ACS, která volá web SDK.

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
