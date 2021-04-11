---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 479aa522462d14f295177e6b2d2fcc4707657760
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498785"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-android-ios.md)]

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Nasazený prostředek služby Azure Communication Services. [Vytvořte prostředek služby Communications](../../create-communication-resource.md).
- Přístupový token uživatele pro povolení klienta volání. [Získání přístupového tokenu uživatele](../../access-tokens.md)
- Volitelné: dokončete [Přidání hlasového volání do rychlého startu vaší aplikace](../getting-started-with-calling.md) .

## <a name="set-up-your-system"></a>Nastavení systému

### <a name="create-the-xcode-project"></a>Vytvoření projektu Xcode

V Xcode vytvořte nový projekt iOS a vyberte šablonu aplikace s **jedním zobrazením** . V tomto rychlém startu se používá [SwiftUI Framework](https://developer.apple.com/xcode/swiftui/), takže byste měli nastavit **jazyk** na **SWIFT** a **uživatelské rozhraní** na **SwiftUI**. 

V rámci tohoto rychlého startu nebudete vytvářet testy jednotek nebo testy uživatelského rozhraní. Nebojte se, že nebudete mít zaškrtnutá textová pole **Zahrnout testy jednotek** a **Zahrnout testy uživatelského rozhraní** .

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Snímek obrazovky, který zobrazuje okno pro vytvoření projektu v rámci Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Instalace balíčku a závislostí pomocí CocoaPods

1. Vytvořte souboru podfile pro vaši aplikaci takto:

   ```
   platform :ios, '13.0'
   use_frameworks!
   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.8'
     pod 'AzureCommunication', '~> 1.0.0-beta.8'
     pod 'AzureCore', '~> 1.0.0-beta.8'
   end
   ```

2. Spusťte `pod install`.
3. Otevřete `.xcworkspace` pomocí Xcode.

### <a name="request-access-to-the-microphone"></a>Požádat o přístup k mikrofonu

Chcete-li získat přístup k mikrofonu zařízení, je třeba aktualizovat seznam vlastností informací o aplikaci pomocí `NSMicrophoneUsageDescription` . Nastavte přidruženou hodnotu na `string` , která bude obsažena v dialogovém okně, které systém používá k vyžádání přístupu od uživatele.

Klikněte pravým tlačítkem myši na `Info.plist` položku stromové struktury projektu a vyberte **Otevřít jako**  >  **zdrojový kód**. Přidejte následující řádky v části nejvyšší úrovně `<dict>` a pak soubor uložte.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

Otevřete soubor *contentView. SWIFT* projektu a přidejte `import` do horní části souboru deklaraci pro import `AzureCommunicationCalling` knihovny. Kromě toho importujte `AVFoundation` . Budete ho potřebovat pro požadavky na zvukové oprávnění v kódu.

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="learn-the-object-model"></a>Informace o objektovém modelu

Následující třídy a rozhraní zpracovávají některé hlavní funkce komunikačních služeb Azure, které volají sadu SDK pro iOS.

> [!NOTE]
> V tomto rychlém startu se používá verze 1.0.0-beta. 8 volání sady SDK.


| Název                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| `CallClient` | `CallClient` je hlavní vstupní bod pro volání sady SDK.|
| `CallAgent` | `CallAgent` slouží ke spuštění a správě volání. |
| `CommunicationTokenCredential` | `CommunicationTokenCredential` se používá jako přihlašovací údaje tokenu pro vytvoření instance `CallAgent` .| 
| `CommunicationIdentifier` | `CommunicationIdentifier` slouží k reprezentaci identity uživatele. Identita může být `CommunicationUserIdentifier` , `PhoneNumberIdentifier` , nebo `CallingApplication` . |

> [!NOTE]
> Když aplikace implementuje delegáty událostí, musí uchovávat silný odkaz na objekty, které vyžadují odběry událostí. Například když `RemoteParticipant` je objekt vrácen při vyvolání `call.addParticipant` metody a aplikace nastaví delegáta na naslouchání `RemoteParticipantDelegate` , aplikace musí obsahovat silný odkaz na `RemoteParticipant` objekt. V opačném případě, pokud se tento objekt získá, delegát vyvolá závažnou výjimku, když se volající sada SDK pokusí objekt vyvolat.

## <a name="initialize-callagent"></a>Inicializovat CallAgent

Chcete-li vytvořit `CallAgent` instanci z nástroje `CallClient` , je nutné použít `callClient.createCallAgent` metodu, která asynchronně vrátí `CallAgent` objekt po jeho inicializaci.

Chcete-li vytvořit klienta volání, je nutné předat `CommunicationTokenCredential` objekt.

```swift

import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationTokenCredential?
var userCredential: CommunicationTokenCredential?
   do {
       userCredential = try CommunicationTokenCredential(with: CommunicationTokenRefreshOptions(initialToken: token, 
                                                                     refreshProactively: true,
                                                                     tokenRefresher: self.fetchTokenSync))
   } catch {
       return
}

// tokenProvider needs to be implemented by Contoso, which fetches a new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

Předejte `CommunicationTokenCredential` objekt, který jste vytvořili `CallClient` , do a nastavte zobrazovaný název.

```swift

callClient = CallClient()
let callAgentOptions:CallAgentOptions = CallAgentOptions()!
options.displayName = " iOS User"

callClient?.createCallAgent(userCredential: userCredential!,
    options: callAgentOptions) { (callAgent, error) in
        if error == nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})

```

## <a name="place-an-outgoing-call"></a>Umístit odchozí volání

Chcete-li vytvořit a spustit volání, je nutné volat jedno z rozhraní API v `CallAgent` a zadat identitu komunikačních služeb uživatele, kterého jste zřídili pomocí sady SDK pro správu komunikačních služeb.

Vytvoření a spuštění volání jsou synchronní. Zobrazí se instance volání, která vám umožní přihlásit se k odběru všech událostí volání.

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Nakoná volání 1:1 uživateli nebo volání 1: n s uživateli a veřejnou telefonní síť.

```swift

let callees = [CommunicationUser(identifier: 'UserId')]
let oneToOneCall = self.callAgent.call(participants: callees, options: StartCallOptions())

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Nakonání volání 1: n s uživateli a PSTN
Chcete-li umístit volání do veřejné telefonní služby, musíte zadat telefonní číslo získané pomocí komunikačních služeb.

```swift

let pstnCallee = PhoneNumberIdentifier(phoneNumber: '+1999999999')
let callee = CommunicationUserIdentifier(identifier: 'UserId')
let groupCall = self.callAgent.call(participants: [pstnCallee, callee], options: StartCallOptions())

```

### <a name="place-a-11-call-with-video"></a>Vložení volání 1:1 s videem
Informace o tom, jak získat instanci správce zařízení, najdete v části o [správě zařízení](#manage-devices).

```swift

let camera = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: camera)
let videoOptions = VideoOptions(localVideoStream: localVideoStream)

let startCallOptions = StartCallOptions()
startCallOptions?.videoOptions = videoOptions

let callee = CommunicationUserIdentifier(identifier: 'UserId')
let call = self.callAgent?.call(participants: [callee], options: startCallOptions)

```

### <a name="join-a-group-call"></a>Připojit se k volání skupiny
Chcete-li se připojit ke volání, je nutné volat jedno z rozhraní API na `CallAgent` .

```swift

let groupCallLocator = GroupCallLocator(groupId: UUID(uuidString: "uuid_string"))!
let call = self.callAgent?.join(with: groupCallLocator, joinCallOptions: JoinCallOptions())

```

### <a name="subscribe-to-an-incoming-call"></a>Přihlášení k odběru příchozího volání
Přihlaste se k odběru události příchozího volání.

```
final class IncomingCallHandler: NSObject, CallAgentDelegate, IncomingCallDelegate
{
    // Event raised when there is an incoming call
    public func onIncomingCall(_ callAgent: CallAgent!, incomingcall: IncomingCall!) {
        self.incomingCall = incomingcall
        // Subscribe to get OnCallEnded event
        self.incomingCall?.delegate = self
    }

    // Event raised when incoming call was not answered
    public func onCallEnded(_ incomingCall: IncomingCall!, args: PropertyChangedEventArgs!) {
        self.incomingCall = nil
    }
}
```

### <a name="accept-an-incoming-call"></a>Přijmout příchozí volání
Chcete-li přijmout volání, zavolejte `accept` metodu na objekt volání. Nastavte delegáta na `CallAgent` .

```swift
final class CallHandler: NSObject, CallAgentDelegate
{
    public var incomingCall: Call?
 
    public func onCallsUpdated(_ callAgent: CallAgent!, args: CallsUpdatedEventArgs!) {
        if let incomingCall = args.addedCalls?.first(where: { $0.isIncoming }) {
            self.incomingCall = incomingCall
        }
    }
}

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: firstCamera)
let acceptCallOptions = AcceptCallOptions()
acceptCallOptions!.videoOptions = VideoOptions(localVideoStream:localVideoStream!)
if let incomingCall = CallHandler().incomingCall {
   incomingCall.accept(options: acceptCallOptions) { (call, error) in
               if error == nil {
                   print("Incoming call accepted")
               } else {
                   print("Failed to accept incoming call")
               }
           }
} else {
   print("No incoming call found to accept")
}
```

## <a name="set-up-push-notifications"></a>Nastavení nabízených oznámení

Mobilní nabízené oznámení je místní oznámení, které získáte v mobilním zařízení. Pro volání se zaměříme na nabízená oznámení VoIP (Voice over Internet Protocol). 

Následující části popisují, jak registrovat nabízená oznámení, zpracovávat je a rušit jejich registraci. Než tyto úlohy spustíte, dokončete tyto požadavky:

1. V Xcode přejdete na **možnosti podepisování &**. Přidejte schopnost výběrem možnosti **+** a pak vyberte **nabízená oznámení**.
2. Přidejte další možnosti výběrem **+ Možnosti** a pak vyberte **režimy pozadí**.
3. V části **režimy pozadí** zaškrtněte políčko **hlas přes IP adresu** a pro **Vzdálená oznámení** .

:::image type="content" source="../media/ios/xcode-push-notification.png" alt-text="Snímek obrazovky, který ukazuje, jak přidat funkce v Xcode." lightbox="../media/ios/xcode-push-notification.png":::

### <a name="register-for-push-notifications"></a>Registrace nabízených oznámení

Pokud se chcete zaregistrovat k nabízeným oznámením, zavolejte `registerPushNotification()` na `CallAgent` instanci s registračním tokenem zařízení.

Registrace nabízených oznámení musí nastat po úspěšné inicializaci. Po `callAgent` zničení objektu `logout` bude volána metoda, která automaticky zruší registraci nabízených oznámení.


```swift

let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken: deviceToken) { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
}

```

### <a name="handle-push-notifications"></a>Zpracování nabízených oznámení
Chcete-li dostávat nabízená oznámení pro příchozí volání, zavolejte `handlePushNotification()` na `CallAgent` instanci s datovou částí slovníku.

```swift

let callNotification = IncomingCallInformation.from(payload: pushPayload?.dictionaryPayload)

callAgent.handlePush(notification: callNotification) { (error) in
    if (error != nil) {
        print("Handling of push notification failed")
    } else {
        print("Handling of push notification was successful")
    }
}

```
### <a name="unregister-push-notifications"></a>Zrušení registrace nabízených oznámení

Aplikace můžou kdykoli zrušit registraci nabízených oznámení. Jednoduše zavolejte `unregisterPushNotification` metodu na `CallAgent` .

> [!NOTE]
> Aplikace nejsou automaticky odregistrovány od nabízených oznámení při odhlášení.

```swift

callAgent.unregisterPushNotifications { (error) in
    if (error != nil) {
        print("Unregister of push notification failed, please try again")
    } else {
        print("Unregister of push notification was successful")
    }
}

```

## <a name="perform-mid-call-operations"></a>Provádění operací se středním voláním

Během volání můžete provádět různé operace ke správě nastavení souvisejících s videem a zvukem.

### <a name="mute-and-unmute"></a>Ztlumení a ztlumení

Chcete-li ztlumit nebo zrušit ztlumení místního koncového bodu, můžete použít `mute` `unmute` rozhraní API a asynchronní.

```swift
call!.mute { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
}

```

Použijte následující kód k asynchronnímu ztlumení místního koncového bodu.

```swift
call!.unmute { (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
}
```

### <a name="start-and-stop-sending-local-video"></a>Spuštění a zastavení odesílání místního videa

Pokud chcete začít odesílat místní video jiným účastníkům ve volání, použijte `startVideo` rozhraní API a předejte ho `localVideoStream` `camera` .

```swift

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: firstCamera)

call!.startVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video started successfully")
    } else {
        print("Local video failed to start")
    }
}

```

Po zahájení odesílání videa `LocalVideoStream` je instance přidána do `localVideoStreams` kolekce v instanci volání.

```swift

call.localVideoStreams[0]

```

Chcete-li zastavit místní video, předejte `localVideoStream` instanci vrácenou z vyvolání `call.startVideo` . Toto je asynchronní akce.

```swift

call!.stopVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    } else {
        print("Local video failed to stop")
    }
}

```

## <a name="manage-remote-participants"></a>Správa vzdálených účastníků

Všichni vzdálení účastníci jsou zastoupeni `RemoteParticipant` typem a jsou k dispozici prostřednictvím `remoteParticipants` kolekce v instanci volání.

### <a name="list-participants-in-a-call"></a>Výpis účastníků ve volání

```swift

call.remoteParticipants

```

### <a name="get-remote-participant-properties"></a>Získat vlastnosti vzdáleného účastníka

```swift

// [RemoteParticipantDelegate] delegate - an object you provide to receive events from this RemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision a token for another user
var identity = remoteParticipant.identity

// ParticipantStateIdle = 0, ParticipantStateEarlyMedia = 1, ParticipantStateConnecting = 2, ParticipantStateConnected = 3, ParticipantStateOnHold = 4, ParticipantStateInLobby = 5, ParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [Error] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// RemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [RemoteVideoStream, RemoteVideoStream, ...]

```

### <a name="add-a-participant-to-a-call"></a>Přidání účastníka do volání

Chcete-li přidat účastníka do volání (buď uživatele, nebo telefonní číslo), můžete vyvolat `addParticipant` . Tento příkaz bude synchronně vracet instanci vzdáleného účastníka.

```swift

let remoteParticipantAdded: RemoteParticipant = call.add(participant: CommunicationUserIdentifier(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>Odebrání účastníka z volání
Chcete-li odebrat účastníka z volání (buď uživatele, nebo telefonní číslo), můžete `removeParticipant` rozhraní API vyvolat. Tato akce bude vyřešena asynchronně.

```swift

call!.remove(participant: remoteParticipantAdded) { (error) in
    if (error == nil) {
        print("Successfully removed participant")
    } else {
        print("Failed to remove participant")
    }
}

```

## <a name="render-remote-participant-video-streams"></a>Vykreslovat streamy videa vzdáleného účastníka

Vzdálení účastníci můžou během volání zahájit sdílení videa nebo obrazovky.

### <a name="handle-video-sharing-or-screen-sharing-streams-of-remote-participants"></a>Zpracování datových proudů ve sdílení videa nebo sdílení obrazovky vzdálených účastníků

Chcete-li zobrazit seznam datových proudů vzdálených účastníků, zkontrolujte `videoStreams` kolekce.

```swift

var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]

```

### <a name="get-remote-video-stream-properties"></a>Získat vlastnosti vzdáleného streamu videa

```swift

var type: MediaStreamType = remoteParticipantVideoStream.type // 'MediaStreamTypeVideo'

var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available

var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream

```

### <a name="render-remote-participant-streams"></a>Vykreslovat proudy vzdálených účastníků

Pro zahájení vykreslování vzdálených proudů účastníků použijte následující kód.

```swift

let renderer: Renderer? = Renderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView: RendererView? = renderer?.createView(with: RenderingOptions(scalingMode: ScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(scalingMode: ScalingMode.fit)

```

### <a name="get-remote-video-renderer-methods-and-properties"></a>Získat metody a vlastnosti nástroje pro vykreslování vzdálených videí

```swift
// [Synchronous] dispose() - dispose renderer and all `RendererView` associated with this renderer. To be called when you have removed all associated views from the UI.
remoteVideoRenderer.dispose()
```

## <a name="manage-devices"></a>Správa zařízení

`DeviceManager` umožňuje vytvořit výčet místních zařízení, která se dají použít při volání přenosu zvukových nebo video datových proudů. Umožňuje taky požádat uživatele o oprávnění k přístupu k mikrofonu nebo kameře. K objektu můžete přistupovat `deviceManager` `callClient` .

```swift

self.callClient!.getDeviceManager { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    }
```

### <a name="enumerate-local-devices"></a>Zobrazení výčtu místních zařízení

Pro přístup k místním zařízením můžete použít metody výčtu ve Správci zařízení. Výčet je synchronní akce.

```swift
// enumerate local cameras
var localCameras = deviceManager.cameras! // [VideoDeviceInfo, VideoDeviceInfo...]
// enumerate local cameras
var localMicrophones = deviceManager.microphones! // [AudioDeviceInfo, AudioDeviceInfo...]
// enumerate local cameras
var localSpeakers = deviceManager.speakers! // [AudioDeviceInfo, AudioDeviceInfo...]
``` 

### <a name="set-the-default-microphone-or-speaker"></a>Nastavení výchozího mikrofonu nebo mluvčího

Správce zařízení můžete použít k nastavení výchozího zařízení, které se použije při spuštění volání. Pokud nejsou nastavené výchozí hodnoty zásobníku, komunikační služby se vrátí do výchozího nastavení operačního systému.

```swift
// get first microphone
var firstMicrophone = self.deviceManager!.cameras!.first
// [Synchronous] set microphone
deviceManager.setMicrophone(microphoneDevice: firstMicrophone)
// get first speaker
var firstSpeaker = self.deviceManager!.speakers!
// [Synchronous] set speaker
deviceManager.setSpeaker(speakerDevice: firstSpeaker)
```

### <a name="get-a-local-camera-preview"></a>Získat náhled místní kamery

Můžete použít `Renderer` k zahájení vykreslování datového proudu z místní kamery. Tento datový proud se nebude posílat jiným účastníkům; je to místní kanál verze Preview. Toto je asynchronní akce.

```swift

let camera: VideoDeviceInfo = self.deviceManager!.getCameraList()![0]
let localVideoStream: LocalVideoStream = LocalVideoStream(camera: camera)
let renderer: Renderer = Renderer(localVideoStream: localVideoStream)
self.view = try renderer!.createView()

```

### <a name="get-local-camera-preview-properties"></a>Získat vlastnosti místní kamery ve verzi Preview

Zobrazovací jednotka má sadu vlastností a metod, které umožňují řídit vykreslování.

```swift

// Constructor can take in LocalVideoStream or RemoteVideoStream
let localRenderer = Renderer(localVideoStream:localVideoStream)
let remoteRenderer = Renderer(remoteVideoStream:remoteVideoStream)

// [StreamSize] size of the rendering view
localRenderer.size

// [RendererDelegate] an object you provide to receive events from this Renderer instance
localRenderer.delegate

// [Synchronous] create view
try! localRenderer.createView()

// [Synchronous] create view with rendering options
try! localRenderer.createView(with: RenderingOptions(scalingMode: ScalingMode.fit))

// [Synchronous] dispose rendering view
localRenderer.dispose()

```

## <a name="subscribe-to-notifications"></a>Přihlásit se k odběru oznámení

Můžete se přihlásit k odběru většiny vlastností a kolekcí, které budou oznamovány při změně hodnot.

### <a name="properties"></a>Vlastnosti
Chcete-li se přihlásit k odběru `property changed` událostí, použijte následující kód.

```swift
call.delegate = self
// Get the property of the call state by getting on the call's state member
public func onCallStateChanged(_ call: Call!,
                               args: PropertyChangedEventArgs!)
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

 // to unsubscribe
 self.call.delegate = nil

```

### <a name="collections"></a>Kolekce
Chcete-li se přihlásit k odběru `collection updated` událostí, použijte následující kód.

```swift
call.delegate = self
// Collection contains the streams that were added or removed only
public func onLocalVideoStreamsChanged(_ call: Call!,
                                       args: LocalVideoStreamsUpdatedEventArgs!)
{
    print(args.addedStreams.count)
    print(args.removedStreams.count)
}
// to unsubscribe
self.call.delegate = nil
```
