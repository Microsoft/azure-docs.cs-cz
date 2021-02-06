---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 7d391998e7f20cff0f77f6aab7938bc375f75c9e
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99616338"
---
## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Nasazený prostředek komunikačních služeb. [Vytvořte prostředek služby Communications](../../create-communication-resource.md).
- A `User Access Token` povolí klienta volání. Další informace o [tom, jak získat `User Access Token` ](../../access-tokens.md)
- Volitelné: dokončete rychlé [zprovoznění, abyste mohli začít s přidáváním volání do aplikace](../getting-started-with-calling.md) .

## <a name="setting-up"></a>Nastavení

### <a name="install-the-client-library"></a>Instalace klientské knihovny

Pomocí `npm install` příkazu můžete nainstalovat volání služby Azure Communication Services a běžné klientské knihovny pro JavaScript.

```console
npm install @azure/communication-calling --save
```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce komunikačních služeb Azure, které volají klientskou knihovnu:

| Název                             | Description                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient je hlavní vstupní bod pro volání klientské knihovny.                                                                       |
| CallAgent                        | CallAgent se používá ke spouštění a správě volání.                                                                                            |
| DeviceManager                    | DeviceManager se používá ke správě mediálních zařízení.                                                                                           |
| AzureCommunicationTokenCredential | Třída AzureCommunicationTokenCredential implementuje rozhraní CommunicationTokenCredential, které se používá k vytvoření instance CallAgent. |


## <a name="initialize-the-callclient-create-callagent-and-access-devicemanager"></a>Inicializace CallClient, vytvoření CallAgent a přístup DeviceManager

Vytvoří instanci nové `CallClient` instance. Můžete ji nakonfigurovat s vlastními možnostmi, jako je například instance protokolovacího nástroje.
Po vytvoření instance `CallClient` je možné vytvořit `CallAgent` instanci voláním `createCallAgent` metody na `CallClient` instanci. Tato funkce asynchronně vrátí `CallAgent` objekt instance.
`createCallAgent`Metoda přijímá `CommunicationTokenCredential` jako argument, který přijímá [token přístupu uživatele](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens).
Aby bylo možné získat přístup k `DeviceManager` instanci callAgent, je třeba nejprve vytvořit. Pak můžete použít `getDeviceManager` metodu na `CallClient` instanci a získat tak DeviceManager.

```js
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-an-outgoing-call"></a>Umístit odchozí volání

Chcete-li vytvořit a spustit volání, potřebujete použít jedno z rozhraní API na CallAgent a zadat uživatele, kterého jste vytvořili prostřednictvím klientské knihovny pro správu komunikačních služeb.

Vytvoření a spuštění volání je synchronní. Instance volání umožňuje přihlásit se k odběru událostí volání.

## <a name="place-a-call"></a>Umístit volání

### <a name="place-a-11-call-to-a-user-or-pstn"></a>Nakonání volání 1:1 uživateli nebo veřejné telefonní síti
Chcete-li umístit volání jinému uživateli komunikačních služeb, volejte `call` metodu na `callAgent` a předejte CommunicationUserIdentifier volaný:

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.call([userCallee]);
```

Chcete-li umístit volání do veřejné telefonní sítě, zavolejte `call` metodu na `callAgent` a předejte PhoneNumberIdentifier volaný.
Aby bylo možné volat do veřejné telefonní služby, musí být prostředek komunikačních služeb nakonfigurovaný.
Při volání čísla veřejné telefonní číslo musíte zadat alternativní ID volajícího.
```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.call([pstnCallee], {alternateCallerId});
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Nakonání volání 1: n s uživateli a PSTN
```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.call([userCallee, pstnCallee], {alternateCallerId});
```

### <a name="place-a-11-call-with-video-camera"></a>Vložení volání 1:1 s videokamerou
> [!WARNING]
> V tuto chvíli nemůže být k dispozici více než jeden odchozí datový proud v místním videu.
Chcete-li umístit audiovizuální volání, je nutné vytvořit výčet místních fotoaparátů pomocí `getCameraList` rozhraní deviceManager API.
Jakmile vyberete požadovanou kameru, použijte ji k vytvoření `LocalVideoStream` instance a předejte ji do `videoOptions` jako položku v rámci `localVideoStream` pole do `call` metody.
Jakmile se vaše volání připojí, automaticky začne odesílat Stream videa od vybrané kamery k ostatním účastníkům. To platí také pro volání možnosti videa. přijmout () a CallAgent. Join ().
```js
const deviceManager = await callClient.getDeviceManager();
const videoDeviceInfo = deviceManager.getCameraList()[0];
localVideoStream = new LocalVideoStream(videoDeviceInfo);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.call(['acsUserId'], placeCallOptions);

```

### <a name="join-a-group-call"></a>Připojit se k volání skupiny
Chcete-li spustit nové volání skupiny nebo se připojit k průběžnému volání skupiny, použijte metodu JOIN a předejte objekt s `groupId` vlastností. Hodnota musí být identifikátor GUID.
```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```
### <a name="join-a-teams-meeting"></a>Připojit se k týmům na schůzce
Pokud se chcete připojit k týmu, použijte metodu JOIN a předejte odkaz na schůzku nebo souřadnice schůzky.
```js
// Join using meeting link
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);

// Join using meeting coordinates
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receiving-an-incoming-call"></a>Přijetí příchozího volání

`CallAgent`Instance generuje `incomingCall` událost, když přihlášená identita přijímá příchozí volání. Chcete-li naslouchat této události, přihlaste se k odběru následujícím způsobem:

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    //accept the call
    var call = await incomingCall.accept();

    //reject the call
    incomingCall.reject();
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

`incomingCall`Událost bude poskytovat instanci, `IncomingCall` na které můžete volání přijmout nebo odmítnout.


## <a name="call-management"></a>Správa volání

Můžete získat přístup k vlastnostem volání a provádět různé operace během volání ke správě nastavení souvisejících s videem a zvukem.

### <a name="call-properties"></a>Vlastnosti volání
* Získá jedinečné ID (řetězce) pro toto volání.
```js

const callId: string = call.id;

```

* Pokud se chcete dozvědět víc o dalších účastnících volání, zkontrolujte `remoteParticipant` kolekci v `call` instanci. Pole obsahuje `RemoteParticipant` objekty seznamu
```js
const remoteParticipants = call.remoteParticipants;
```

* Identita volajícího, pokud je volání příchozí. Identita je jedním z `CommunicationIdentifier` typů
```js

const callerIdentity = call.callerInfo.identity;

```

* Získejte stav volání.
```js

const callState = call.state;

```
Vrátí řetězec představující aktuální stav volání:
* None – počáteční stav volání
* ' Příchozí ' – označuje, že volání je příchozí, musí být buď přijato, nebo odmítnuto.
* Probíhá připojování – počáteční přechodový stav po umístění nebo přijetí volání.
* "Vyzvánění" – pro odchozí volání – indikuje, že volání bude vyzvánět pro vzdálené účastníky, je na jejich straně "příchozí".
* ' EarlyMedia ' – označuje stav, ve kterém je přehráno oznámení před připojením volání
* Připojeno – volání je připojené.
* Blokováno – volání je blokováno, žádné médium neprobíhá mezi místním koncovým bodem a vzdáleným účastníkem (y).
* "Odpojení" – přechodový stav před voláním do stavu "Odpojeno"
* Odpojeno – stav konečného volání
  * Pokud dojde ke ztrátě síťového připojení, stav přejde na odpojeno po 2 minutách.

* Chcete-li zjistit, proč dané volání skončilo, zkontrolujte `callEndReason` vlastnost.
```js

const callEndReason = call.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* Chcete-li zjistit, zda je aktuální volání příchozí nebo odchozí volání, zkontrolujte `direction` vlastnost, kterou vrátí `CallDirection` .
```js
const isIncoming = call.direction == 'Incoming';
const isOutgoing = call.direction == 'Outgoing';
```

*  Pokud chcete zkontrolovat, jestli je aktuální mikrofon ztlumený, zkontrolujte `muted` vlastnost a vrátí se `Boolean` .
```js

const muted = call.isMicrophoneMuted;

```

* Chcete-li zjistit, zda je datový proud sdílení obrazovky odesílán z daného koncového bodu, zkontrolujte `isScreenSharingOn` vlastnost a vrátí `Boolean` .
```js

const isScreenSharingOn = call.isScreenSharingOn;

```

* Pokud chcete zkontrolovat aktivní streamy videa, zkontrolujte `localVideoStreams` kolekci, která obsahuje `LocalVideoStream` objekty.
```js

const localVideoStreams = call.localVideoStreams;

```

### <a name="call-ended-event"></a>Událost ukončení volání

`Call`Instance emituje událost v `callEnded` případě, že volání skončí. Chcete-li naslouchat této události, přihlaste se následujícím způsobem:

```js
const callEndHander = async (args: { callEndReason: CallEndReason }) => {
    console.log(args.callEndReason)
};

call.on('callEnded', callEndHander);
```

### <a name="mute-and-unmute"></a>Ztlumení a ztlumení

Chcete-li ztlumit nebo zrušit ztlumení místního koncového bodu, můžete použít `mute` `unmute` asynchronní rozhraní API a:

```js

//mute local device 
await call.mute();

//unmute local device 
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>Spuštění a zastavení odesílání místního videa


Chcete-li spustit video, je nutné vytvořit výčet kamer pomocí `getCameraList` metody `deviceManager` objektu. Pak vytvořte novou instanci `LocalVideoStream` předání požadované kamery do `startVideo` metody jako argument:


```js
const localVideoStream = new LocalVideoStream(videoDeviceInfo);
await call.startVideo(localVideoStream);

```

Po úspěšném spuštění odesílání videa `LocalVideoStream` bude instance přidána do `localVideoStreams` kolekce v instanci volání.

```js

call.localVideoStreams[0] === localVideoStream;

```

Pokud chcete zastavit místní video, předejte `localVideoStream` instanci dostupnou v `localVideoStreams` kolekci:

```js

await call.stopVideo(localVideoStream);

```

V době, kdy se video posílá vyvoláním na instanci, můžete přepínat na jiné zařízení fotoaparátu `switchSource` `localVideoStream` .

```js
const source callClient.getDeviceManager().getCameraList()[1];
localVideoStream.switchSource(source);

```

## <a name="remote-participants-management"></a>Vzdálená správa účastníků

Všichni vzdálení účastníci jsou zastoupeni podle `RemoteParticipant` typu a jsou k dispozici prostřednictvím `remoteParticipants` kolekce na instanci volání.

### <a name="list-participants-in-a-call"></a>Výpis účastníků ve volání
`remoteParticipants`Kolekce vrátí seznam vzdálených účastníků v daném volání:

```js

call.remoteParticipants; // [remoteParticipant, remoteParticipant....]

```

### <a name="remote-participant-properties"></a>Vlastnosti vzdáleného účastníka
Vzdálený účastník má sadu vlastností a kolekcí, které jsou k němu přidružené.
#### <a name="communicationidentifier"></a>CommunicationIdentifier
Získat identifikátor pro tohoto vzdáleného účastníka.
```js
const identifier = remoteParticipant.identifier;
```
Může to být jeden z typů ' CommunicationIdentifier ':
  * {communicationUserId: ' <ACS_USER_ID ' >} – objekt představující uživatele ACS
  * {phoneNumber: <E. 164>} – objekt představující telefonní číslo ve formátu E. 164
  * {microsoftTeamsUserId: ' <TEAMS_USER_ID> ', anonymní?: Boolean; Cloud?: "public" | "DoD" | "GCCH"} – objekt, který představuje uživatele týmů

#### <a name="state"></a>State
Získat stav tohoto vzdáleného účastníka.
```js

const state = remoteParticipant.state;
```
Stav může být jedna z
* Nečinné – počáteční stav
* Probíhá připojování – přechodový stav, zatímco se účastník připojuje k volání.
* Připojeno – účastník je připojený k volání.
* ' Hold ' – účastník je blokován
* ' EarlyMedia ' – před připojením účastníka k volání se přehraje oznámení.
* ' Odpojeno ' – konečný stav – účastník je odpojen od volání
  * Pokud vzdálený účastník ztratí své připojení k síti, pak vzdálený stav účastníka přejde na odpojeno po 2 minutách.

#### <a name="call-end-reason"></a>Důvod volání metody end
Chcete-li zjistit, proč účastník opustil hovor, zkontrolujte `callEndReason` vlastnost:
```js
const callEndReason = remoteParticipant.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```
#### <a name="is-muted"></a>Je ztlumený
Pokud chcete zkontrolovat, jestli je tento vzdálený účastník ztlumený, zkontrolujte `isMuted` vlastnost, která vrátí. `Boolean`
```js
const isMuted = remoteParticipant.isMuted;
```
#### <a name="is-speaking"></a>Je speaking
Chcete-li zkontrolovat, zda tento vzdálený účastník mluví nebo není, zkontrolujte `isSpeaking` vlastnost, kterou vrátí `Boolean`
```js
const isSpeaking = remoteParticipant.isSpeaking;
```

#### <a name="video-streams"></a>Streamy videa
Pokud chcete zkontrolovat všechny streamy videa, které daný účastník posílá při volání, zkontrolujte `videoStreams` shromažďování, obsahuje `RemoteVideoStream` objekty.
```js

const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]

```


### <a name="add-a-participant-to-a-call"></a>Přidání účastníka do volání

Chcete-li přidat účastníka do volání (buď uživatele, nebo telefonní číslo), můžete vyvolat `addParticipant` .
Zadejte jeden z typů Identifier.
Tím se synchronně vrátí instance vzdáleného účastníka.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-participant-from-a-call"></a>Odebrat účastníka volání

Chcete-li odebrat účastníka ze volání (buď uživatele, nebo telefonního čísla), můžete vyvolat `removeParticipant` .
Musíte předat jeden z typů ' identifikátor ', který se vyřeší asynchronně po odebrání účastníka z volání.
Účastník bude také odebrán z `remoteParticipants` kolekce.

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
const streamType: MediaStreamType = remoteVideoStream.type;
```
 
Aby bylo možné vykreslit `RemoteVideoStream` , je nutné se přihlásit k odběru `isAvailableChanged` události.
Pokud se `isAvailable` vlastnost změní na `true` , vzdálený účastník posílá datový proud.
Jakmile k tomu dojde, vytvořte novou instanci `Renderer` a pak vytvořte novou `RendererView` instanci pomocí asynchronní `createView` metody.  Pak se můžete připojit `view.target` k libovolnému prvku uživatelského rozhraní.
Vždy, když se změní dostupnost vzdáleného streamu, můžete zvolit zničení celého zobrazovacího panelu, jeho konkrétního `RendererView` nebo zachování, ale výsledkem bude zobrazení prázdného snímku videa.

```js
let renderer: Renderer = new Renderer(remoteParticipantStream);
const displayVideo = () => {
    const view = await renderer.createView();
    htmlElement.appendChild(view.target);
}
remoteParticipantStream.on('availabilityChanged', async () => {
    if (remoteParticipantStream.isAvailable) {
        displayVideo();
    } else {
        renderer.dispose();
    }
});
if (remoteParticipantStream.isAvailable) {
    displayVideo();
}
```

### <a name="remote-video-stream-properties"></a>Vlastnosti vzdáleného streamu videa
Proudy vzdálených videí mají následující vlastnosti:

* `Id` – ID vzdáleného streamu videa
```js
const id: number = remoteVideoStream.id;
```

* `StreamSize` -velikost (šířka/výška) vzdáleného streamu videa
```js
const size: {width: number; height: number} = remoteVideoStream.size;
```

* `MediaStreamType` – může být ' video ' nebo ' ScreenSharing '
```js
const type: MediaStreamType = remoteVideoStream.type;
```
* `isAvailable` – Určuje, jestli vzdálený koncový bod účastníka aktivně posílá Stream.
```js
const type: boolean = remoteVideoStream.isAvailable;
```

### <a name="renderer-methods-and-properties"></a>Metody a vlastnosti vykreslovacího modulu

* Vytvořte `RendererView` instanci, která může být později připojena v uživatelském rozhraní aplikace pro vykreslení vzdáleného streamu videa.
```js
renderer.createView()
```

* Odstranění zobrazovací jednotky a všech přidružených `RendererView` instancí.
```js
renderer.dispose()
```


### <a name="rendererview-methods-and-properties"></a>Metody a vlastnosti RendererView
Při vytváření můžete `RendererView` zadat `scalingMode` a `mirrored` Vlastnosti.
Pokud je zadaný, režim škálování může být Stretch, oříznutý nebo vešel `Mirrored` , vykreslený datový proud se Překlopí svisle.

```js
const rendererView: RendererView = renderer.createView({ scalingMode, mirrored });
```
Kterákoli `RendererView` z daných instancí má `target` vlastnost, která představuje plochu vykreslování. Tato akce musí být připojena v uživatelském rozhraní aplikace:
```js
document.body.appendChild(rendererView.target);
```

Později můžete režim škálování aktualizovat vyvoláním `updateScalingMode` metody.
```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Správa zařízení

`DeviceManager` umožňuje vytvořit výčet místních zařízení, která se dají použít při volání přenosů zvukových a video datových proudů. Umožňuje taky požádat uživatele o oprávnění k přístupu ke svému mikrofonu a kameře pomocí rozhraní API nativního prohlížeče.

Můžete přistupovat k `deviceManager` `callClient.getDeviceManager()` metodě volání metody.
> [!WARNING]
> `callAgent`Pro získání přístupu k DeviceManager se v současné době musí vytvořit objekt, aby se vytvořila první instance.

```js

const deviceManager = await callClient.getDeviceManager();

```

### <a name="enumerate-local-devices"></a>Zobrazení výčtu místních zařízení

Chcete-li získat přístup k místním zařízením, můžete použít metody výčtu na Správce zařízení. Výčet je synchronní akce.

```js

//  Get a list of available video devices for use.
const localCameras = deviceManager.getCameraList(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = deviceManager.getMicrophoneList(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = deviceManager.getSpeakerList(); // [AudioDeviceInfo, AudioDeviceInfo...]

```

### <a name="set-default-microphonespeaker"></a>Nastavit výchozí mikrofon/mluvčí

Správce zařízení umožňuje nastavit výchozí zařízení, které se použije při spuštění volání.
Pokud nejsou nastavené výchozí hodnoty klienta, komunikační služby se vrátí do výchozího nastavení operačního systému.

```js

// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.getMicrophone();

// Set the microphone device to use.
await deviceManager.setMicrophone(AudioDeviceInfo);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.getSpeaker();

// Set the speaker device to use.
await deviceManager.setSpeaker(AudioDeviceInfo);

```

### <a name="local-camera-preview"></a>Místní kamera verze Preview

`DeviceManager` `Renderer` K zahájení vykreslování datových proudů z místní kamery můžete použít a. Tento datový proud se nebude posílat jiným účastníkům; je to místní kanál verze Preview. Toto je asynchronní akce.

```js
const localVideoDevice = deviceManager.getCameraList()[0];
const localCameraStream = new LocalVideoStream(localVideoDevice);
const renderer = new Renderer(localCameraStream);
const view = await renderer.createView();
document.body.appendChild(view.target);

```

### <a name="request-permission-to-cameramicrophone"></a>Požádat o oprávnění ke kameře nebo mikrofonu

Vyzvat uživatele k udělení oprávnění pro fotoaparát a mikrofon s následujícími oprávněními:

```js
const result = await deviceManager.askDevicePermission(audio: true, video: true);
```
Tato akce se vyřeší asynchronně s objektem, který označuje `audio` , jestli `video` byla udělená oprávnění:
```js
console.log(result.audio);
console.log(result.video);
```

Aktuální stav oprávnění pro daný typ lze zkontrolovat voláním `getPermissionState` :

```js

const result = deviceManager.getPermissionState('Microphone'); // for microphone permission state
const result = deviceManager.getPermissionState('Camera'); // for camera permission state

console.log(result); // 'Granted' | 'Denied' | 'Prompt' | 'Unknown';

```

## <a name="call-recording-management"></a>Správa záznamů volání

Záznam volání je rozšířenou funkcí základního `Call` rozhraní API. Nejdřív musíte získat objekt rozhraní API pro nahrávání funkcí:

```js
const callRecordingApi = call.api(Features.Recording);
```

Poté, pokud chcete zkontrolovat, zda je hovor zaznamenáván, zkontrolujte `isRecordingActive` vlastnost třídy, kterou `callRecordingApi` vrátí `Boolean` .

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

## <a name="call-transfer-management"></a>Správa přenosu hovorů

Přenos volání je rozšířená funkce základního `Call` rozhraní API. Nejdřív je potřeba získat objekt rozhraní API pro přenos funkcí:

```js
const callTransferApi = call.api(Features.Transfer);
```

Přenos volání zahrnuje tři strany *přenosu*, *nabyvatel* a *cíl přenosu*. Tok přenosu pracuje následujícím způsobem:

1. Mezi předanými *přenosy* a *nabyvatelem* již existuje připojené volání.
2. *přenosové* rozhodnutí se rozhodne přenést hovor (  ->  *cíl převodu* nabyvatele).
3. rozhraní API pro volání *přenosů* `transfer`
4. *nabyvatel* se rozhodne, zda `accept` nebo `reject` požadavek přenosu pro *přenos cíle* prostřednictvím `transferRequested` události.
5. *cíl přenosu* obdrží příchozí volání pouze v případě, že žádost o přenos proběhla v žádosti *nabyvatele* . `accept`

### <a name="transfer-terminology"></a>Terminologie přenosu

- Transferer – ten, který iniciuje žádost o přenos
- Nabyvatel – ten, který přenáší převodce na cíl přenosu
- Cíl přenosu – ten, který je cílem přenosu do

Pro přenos aktuálního volání můžete použít `transfer` synchronní rozhraní API. `transfer` vybere volitelnou, `TransferCallOptions` která vám umožní nastavit `disableForwardingAndUnanswered` příznak:

- `disableForwardingAndUnanswered` = false – Pokud *cíl přenosu* neodpoví na volání přenosu, pak se bude řídit přesměrováním *cíle přenosu* a nezodpovězeným nastavením.
- `disableForwardingAndUnanswered` = true – Pokud *cíl přenosu* neodpoví na volání přenosu, pak pokus o přenos skončí.

```js
// transfer target can be ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

Přenos umožňuje přihlásit se k odběru `transferStateChanged` `transferRequested` událostí a. `transferRequsted` událost přichází z `call` instance, `transferStateChanged` události a přenosu `state` a `error` přichází z `transfer` instance.

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if transfer request failed
```

Nabyvatel může přijmout nebo odmítnout žádost o přenos iniciované přenosem v `transferRequested` události prostřednictvím `accept()` nebo `reject()` `transferRequestedEventArgs` . K informacím můžete získat přístup `targetParticipant` , `accept` `reject` metody v `transferRequestedEventArgs` .

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

## <a name="eventing-model"></a>Model událostí

Můžete se přihlásit k odběru většiny vlastností a kolekcí, které budou oznamovány při změně hodnot.

### <a name="properties"></a>Vlastnosti
Přihlášení k odběru `property changed` událostí:

```js

const eventHandler = () => {
    // check current value of a property, value is not passed to callback
    console.log(object.property);
};
object.on('propertyNameChanged',eventHandler);

// To unsubscribe:

object.off('propertyNameChanged',eventHandler);

```

### <a name="collections"></a>Kolekce
Přihlášení k odběru `collection updated` událostí:

```js

const eventHandler = (e) => {
    // check added elements
    console.log(e.added);
    // check removed elements
    console.log(e.removed);
};
object.on('collectionNameUpdated',eventHandler);

// To unsubscribe:

object.off('collectionNameUpdated',eventHandler);

```
