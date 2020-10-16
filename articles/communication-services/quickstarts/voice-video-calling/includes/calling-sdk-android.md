---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 99a038b23eb0978b6e1d8a65b061c2f744852def
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92126789"
---
## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Nasazený prostředek komunikačních služeb. [Vytvořte prostředek služby Communications](../../create-communication-resource.md).
- A `User Access Token` povolí klienta volání. Další informace o [tom, jak získat `User Access Token` ](../../access-tokens.md)
- Volitelné: dokončete rychlé [zprovoznění, abyste mohli začít s přidáváním volání do aplikace](../getting-started-with-calling.md) .

## <a name="setting-up"></a>Nastavení

### <a name="install-the-package"></a>Instalace balíčku

<!-- TODO: update with instructions on how to download, install and add package to project -->
Vyhledejte na úrovni projektu Build. Gradle a ujistěte se, že jste přidali `mavenCentral()` do seznamu úložišť v části `buildscript` a. `allprojects`
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
Pak v sestavení na úrovni modulu přidejte následující řádky do oddílu závislosti.

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.2'
    ...
}

```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce komunikačních služeb Azure, které volají klientskou knihovnu:

| Název                                  | Popis                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| CallClient je hlavní vstupní bod pro volání klientské knihovny.|
| CallAgent | CallAgent se používá ke spouštění a správě volání. |
| CommunicationUserCredential | CommunicationUserCredential se používá jako přihlašovací údaje tokenu pro vytvoření instance CallAgent.|

## <a name="initialize-the-callclient-create-a-callagent-and-access-the-devicemanager"></a>Inicializujte CallClient, vytvořte CallAgent a získejte přístup k DeviceManager

Chcete-li vytvořit `CallAgent` instanci, musíte zavolat `createCallAgent` metodu na `CallClient` instanci. Tato funkce asynchronně vrátí `CallAgent` objekt instance.
`createCallAgent`Metoda přijímá `CommunicationUserCredential` jako argument, který zapouzdřuje [přístupový token](../../access-tokens.md).
Pro přístup k rozhraní `DeviceManager` musí být nejprve vytvořena instance callAgent a pak můžete použít `CallClient.getDeviceManager` metodu pro získání DeviceManager.

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationUserCredential tokenCredential = new CommunicationUserCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgent callAgent = await callClient.createCallAgent((appContext, tokenCredential).get();
DeviceManage deviceManager = await callClient.getDeviceManager().get();
```

## <a name="place-an-outgoing-call-and-join-a-group-call"></a>Umístěte odchozí volání a připojte se k volání skupiny.

Chcete-li vytvořit a spustit volání, je třeba zavolat `CallAgent.call()` metodu a zadat `Identifier` volaného (y).
Chcete-li se připojit k volání skupiny, je třeba zavolat `CallAgent.join()` metodu a poskytnout identifikátor skupiny. ID skupin musí být ve formátu GUID nebo UUID.

Vytvoření a spuštění volání jsou synchronní. Instance volání umožňuje přihlásit se k odběru všech událostí volání.

### <a name="place-a-11-call-to-a-user"></a>Vložení volání 1:1 uživateli
Chcete-li umístit volání jinému uživateli komunikačních služeb, volejte `call` metodu na `callAgent` a předejte objekt s `communicationUserId` klíčem.
```java
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
CommunicationUser acsUserId = new CommunicationUser(<USER_ID>);
CommunicationUser participants[] = new CommunicationUser[]{ acsUserId };
call oneToOneCall = callAgent.call(appContext, participants, startCallOptions);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Nakonání volání 1: n s uživateli a PSTN
> [!WARNING]
> V současném volání veřejné telefonní sítě není k dispozici volání 1: n uživateli a číslo veřejné telefonní číslo, které je nutné zadat pro telefonní číslo volaného.
Aby bylo možné volat do veřejné telefonní služby, je potřeba nakonfigurovat prostředek komunikačních služeb:
```java
CommunicationUser acsUser1 = new CommunicationUser(<USER_ID>);
PhoneNumber acsUser2 = new PhoneNumber("<PHONE_NUMBER>");
CommunicationIdentifier participants[] = new CommunicationIdentifier[]{ acsUser1, acsUser2 };
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
Call groupCall = callAgent.call(participants, startCallOptions);
```

### <a name="place-a-11-call-with-video-camera"></a>Vložení volání 1:1 s videokamerou
> [!WARNING]
> V současné době se podporuje jenom jeden odchozí datový proud místního videa, který zadává volání s videem, takže je nutné vytvořit výčet místních kamer pomocí `deviceManager` `getCameraList` rozhraní API.
Jakmile vyberete požadovanou kameru, použijte ji k vytvoření `LocalVideoStream` instance a předejte ji do `videoOptions` jako položku v `localVideoStream` poli do `call` metody.
Po připojení se volání automaticky spustí odeslání streamu videa z vybrané kamery do ostatních účastníků.
```java
Context appContext = this.getApplicationContext();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameraList().get(0);
LocalVideoStream currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
VideoOptions videoOptions = new VideoOptions(currentVideoStream);

CommunicationUser[] participants = new CommunicationUser[]{ new CommunicationUser("<acs user id>") };
StartCallOptions startCallOptions = new StartCallOptions();
startCallOptions.setVideoOptions(videoOptions);
Call call = callAgent.call(context, participants, startCallOptions);
```

### <a name="join-a-group-call"></a>Připojit se k volání skupiny
Chcete-li spustit nové volání skupiny nebo se zapojit do průběžného volání skupiny, musíte zavolat metodu JOIN a předat objektu `groupId` vlastnost. Hodnota musí být identifikátor GUID.
```java
Context appContext = this.getApplicationContext();
GroupCallContext groupCallContext = new groupCallContext("<GUID>");
JoinCallOptions joinCallOptions = new JoinCallOptions();

call = callAgent.join(context, groupCallContext, joinCallOptions);
```

## <a name="push-notifications"></a>Nabízená oznámení

### <a name="overview"></a>Přehled
Mobilní nabízená oznámení jsou místní oznámení, která vidíte na mobilních zařízeních. Pro volání se budeme soustředit na nabízená oznámení VoIP (Voice over Internet Protocol). Zaregistruje se na nabízená oznámení, zpracuje nabízená oznámení a pak zruší registraci nabízených oznámení.

### <a name="prerequisites"></a>Předpoklady

Účet Firebase nastavený s povoleným cloudovým zasíláním zpráv (FCM) a službou Firebase Cloud Messaging Service připojenou k instanci centra oznámení Azure. Další informace najdete v tématu [oznámení o komunikačních službách](https://docs.microsoft.com/azure/communication-services/concepts/notifications) .
Kromě toho kurz předpokládá, že k sestavení aplikace používáte Android Studio verze 3,6 nebo vyšší.

Pro aplikaci pro Android se vyžaduje sada oprávnění, aby bylo možné přijímat zprávy oznámení z Firebase cloudového zasílání zpráv. Do `AndroidManifest.xml` souboru přidejte následující sadu oprávnění hned za *<manifest... >* nebo pod *</application>* značku

```XML
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
```

### <a name="register-for-push-notifications"></a>Registrace nabízených oznámení

Aby bylo možné registrovat nabízená oznámení, musí aplikace zavolat `registerPushNotification()` na instanci *CallAgent* s registračním tokenem zařízení.

Pokud chcete získat token registrace zařízení, přidejte do souboru *Build. Gradle* v modulu aplikace klientskou knihovnu Firebase tak, že v části přidáte následující řádky, `dependencies` Pokud tam ještě není:

```
    // Add the client library for Firebase Cloud Messaging
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:20.2.4'
```

V souboru *Build. Gradle* na úrovni projektu přidejte následující `dependencies` části do oddílu, pokud tam ještě není:

```
    classpath 'com.google.gms:google-services:4.3.3'
```

Přidejte následující modul plug-in na začátek souboru, pokud tam ještě není:

```
apply plugin: 'com.google.gms.google-services'
```

Vyberte *synchronizovat hned* na panelu nástrojů. Přidejte následující fragment kódu pro získání tokenu registrace zařízení vygenerovaného klientskou knihovnou zasílání zpráv Firebase pro instanci klientské aplikace nezapomeňte přidat níže uvedené importy do hlavičky hlavní aktivity instance. Jsou vyžadovány, aby fragment mohl načíst token:

```
import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.iid.FirebaseInstanceId;
import com.google.firebase.iid.InstanceIdResult;
```

Přidejte tento fragment kódu pro načtení tokenu:

```
        FirebaseInstanceId.getInstance().getInstanceId()
                .addOnCompleteListener(new OnCompleteListener<InstanceIdResult>() {
                    @Override
                    public void onComplete(@NonNull Task<InstanceIdResult> task) {
                        if (!task.isSuccessful()) {
                            Log.w("PushNotification", "getInstanceId failed", task.getException());
                            return;
                        }

                        // Get new Instance ID token
                        String deviceToken = task.getResult().getToken();
                        // Log
                        Log.d("PushNotification", "Device Registration token retrieved successfully");
                    }
                });
```
Zaregistrujte token registrace zařízení pomocí klientské knihovny volajících služeb pro příchozí nabízená oznámení volání:

```java
String deviceRegistrationToken = "<Device Token from previous section>";
try {
    callAgent.registerPushNotification(deviceRegistrationToken).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while registering for Incoming Calls Push Notifications.")
}
```

### <a name="push-notification-handling"></a>Manipulace s nabízenými oznámeními

Chcete-li přijímat příchozí nabízená oznámení volání, zavolejte *handlePushNotification ()* na instanci *CallAgent* s datovou částí.

Pokud chcete datovou část získat z Firebase cloudového zasílání zpráv, Začněte vytvořením nové služby (soubor > novou službu > Service >), která rozšiřuje třídu klientské knihovny *FirebaseMessagingService* Firebase a přepíše `onMessageReceived` metodu. Tato metoda je obslužná rutina události volána, když Firebase Cloud Messaging doručuje nabízené oznámení do aplikace.

```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    private java.util.Map<String, String> pushNotificationMessageDataFromFCM;

    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        // Check if message contains a notification payload.
        if (remoteMessage.getNotification() != null) {
            Log.d("PushNotification", "Message Notification Body: " + remoteMessage.getNotification().getBody());
        }
        else {
            pushNotificationMessageDataFromFCM = remoteMessage.getData();
        }
    }
}
```
Do tohoto souboru přidejte následující definici služby `AndroidManifest.xml` uvnitř <application> značky:

```
        <service
            android:name=".MyFirebaseMessagingService"
            android:exported="false">
            <intent-filter>
                <action android:name="com.google.firebase.MESSAGING_EVENT" />
            </intent-filter>
        </service>
```

- Jakmile je datová část načtena, může být předána klientské knihovně *komunikačních služeb* , aby byla zpracována voláním metody *HandlePushNotification* v instanci *CallAgent* . `CallAgent`Instance je vytvořena voláním `createCallAgent(...)` metody `CallClient` třídy.

```java
try {
    callAgent.handlePushNotification(pushNotificationMessageDataFromFCM).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while handling the Incoming Calls Push Notifications.");
}
```

Po úspěšném zpracování zprávy nabízených oznámení a obslužné rutiny všechny události jsou správně registrovány, aplikace bude vyzvánět.

### <a name="unregister-push-notifications"></a>Zrušení registrace nabízených oznámení

Aplikace můžou kdykoli zrušit registraci nabízených oznámení. Zavolejte `unregisterPushNotification()` metodu na callAgent pro zrušení registrace.

```java
try {
    callAgent.unregisterPushNotifications().get();
}
catch(Exception e) {
    System.out.println("Something went wrong while un-registering for all Incoming Calls Push Notifications.")
}
```

## <a name="call-management"></a>Správa volání
Můžete získat přístup k vlastnostem volání a provádět různé operace během volání ke správě nastavení souvisejících s videem a zvukem.

### <a name="call-properties"></a>Vlastnosti volání

Získat jedinečné ID pro toto volání:

```java
String callId = call.getCallId();
```

Další informace o dalších účastníkůch v kolekci inspekce volání `remoteParticipant` v `call` instanci:

```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants();
```

Identita volajícího, pokud je volání příchozí:

```java
CommunicationIdentifier callerId = call.getCallerId();
```

Získat stav volání: 

```java
CallState callState = call.getState();
```

Vrátí řetězec představující aktuální stav volání:
* None – počáteční stav volání
* ' Příchozí ' – označuje, že volání je příchozí, musí být buď přijato, nebo odmítnuto.
* Probíhá připojování – počáteční přechodový stav po umístění nebo přijetí volání.
* "Vyzvánění" – pro odchozí volání – indikuje, že volání bude vyzvánět pro vzdálené účastníky, jedná se o "příchozí", ale na svou stranu
* ' EarlyMedia ' – označuje stav, ve kterém je přehráno oznámení před připojením k volání.
* Připojeno – volání je připojené.
* Blokováno – volání je blokováno, žádné médium neprobíhá mezi místním koncovým bodem a vzdáleným účastníkem (y).
* "Odpojení" – přechodový stav před voláním do stavu "Odpojeno"
* Odpojeno – stav konečného volání


Chcete-li zjistit, proč bylo volání ukončeno, zkontrolujte `callEndReason` vlastnost. Obsahuje kód/podkód: 

```java
CallEndReason callEndReason = call.getCallEndReason();
int code = callEndReason.getCode();
int subCode = callEndReason.getSubCode();
```

Chcete-li zjistit, zda aktuální volání je příchozí volání, zkontrolujte `isIncoming` vlastnost:

```java
boolean isIncoming = call.getIsIncoming();
```

Pokud chcete zjistit, jestli je aktuální mikrofon ztlumený, zkontrolujte `muted` vlastnost:

```java
boolean muted = call.getIsMicrophoneMuted();
```

Pokud chcete zkontrolovat aktivní streamy videa, zkontrolujte `localVideoStreams` kolekci:

```java
List<LocalVideoStream> localVideoStreams = call.getLocalVideoStreams();
```

### <a name="mute-and-unmute"></a>Ztlumení a ztlumení

Chcete-li ztlumit nebo zrušit ztlumení místního koncového bodu, můžete použít `mute` `unmute` asynchronní rozhraní API a:

```java
call.mute().get();
call.unmute().get();
```

### <a name="start-and-stop-sending-local-video"></a>Spuštění a zastavení odesílání místního videa

Chcete-li spustit video, je nutné vytvořit výčet fotoaparátů pomocí `getCameraList` rozhraní API na `deviceManager` objektu. Pak vytvořte novou instanci `LocalVideoStream` předání požadované kamery a předejte ji do `startVideo` rozhraní API jako argument:

```java
VideoDeviceInfo desiredCamera = <get-video-device>;
Context appContext = this.getApplicationContext();
currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
videoOptions = new VideoOptions(currentVideoStream);
Future startVideoFuture = call.startVideo(currentVideoStream);
startVideoFuture.get();
```

Po úspěšném spuštění odesílání videa `LocalVideoStream` bude instance přidána do `localVideoStreams` kolekce v instanci volání.

```java
currentVideoStream == call.getLocalVideoStreams().get(0);
```

Pokud chcete zastavit místní video, předejte `localVideoStream` instanci dostupnou v `localVideoStreams` kolekci:

```java
call.stopVideo(localVideoStream).get();
```

V době, kdy se video posílá vyvoláním na instanci, můžete přepínat na jiné zařízení fotoaparátu `switchSource` `localVideoStream` .
```java
localVideoStream.switchSource(source).get();
```

## <a name="remote-participants-management"></a>Vzdálená správa účastníků

Všichni vzdálení účastníci jsou zastoupeni podle `RemoteParticipant` typu a jsou k dispozici prostřednictvím `remoteParticipants` kolekce v instanci volání.

### <a name="list-participants-in-a-call"></a>Výpis účastníků ve volání
`remoteParticipants`Kolekce vrátí seznam vzdálených účastníků v daném volání:
```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants(); // [remoteParticipant, remoteParticipant....]
```

### <a name="remote-participant-properties"></a>Vlastnosti vzdáleného účastníka
Každý vzdálený účastník má k sadu vlastností a kolekcí, které jsou k němu přidruženy:

* Získat identifikátor pro tohoto vzdáleného účastníka.
Identita je jedním z typů identifikátorů.
```java
CommunicationIdentifier participantIdentity = remoteParticipant.getIdentifier();
```

* Získat stav tohoto vzdáleného účastníka.
```java
ParticipantState state = remoteParticipant.getState();
```
Stav může být jedna z
* Nečinné – počáteční stav
* Probíhá připojování – přechodový stav, zatímco se účastník připojuje k volání.
* Připojeno – účastník je připojený k volání.
* ' Hold ' – účastník je blokován
* ' EarlyMedia ' – před připojením účastníka k volání se přehraje oznámení.
* ' Odpojeno ' – konečný stav – účastník je odpojen od volání


* Chcete-li zjistit, proč účastník opustil volání, zkontrolujte `callEndReason` vlastnost:
```java
CallEndReason callEndReason = remoteParticipant.getCallEndReason();
```

* Pokud chcete zkontrolovat, jestli je tento vzdálený účastník ztlumený, zkontrolujte `isMuted` vlastnost:
```java
boolean isParticipantMuted = remoteParticipant.getIsMuted();
```

* Chcete-li zkontrolovat, zda tento vzdálený účastník mluví nebo není, zkontrolujte `isSpeaking` vlastnost:
```java
boolean isParticipantSpeaking = remoteParticipant.getIsSpeaking();
```

* Pokud chcete zkontrolovat všechny streamy videa, které daný účastník posílá v tomto volání, zkontrolujte `videoStreams` kolekci:
```java
List<RemoteVideoStream> videoStreams = remoteParticipant.getVideoStreams(); // [RemoteVideoStream, RemoteVideoStream, ...]
```


### <a name="add-a-participant-to-a-call"></a>Přidání účastníka do volání

Chcete-li přidat účastníka do volání (buď uživatele, nebo telefonní číslo), můžete vyvolat `addParticipant` . Tím se synchronně vrátí instance vzdáleného účastníka.

```java
const acsUser = new CommunicationUser("<acs user id>");
const acsPhone = new PhoneNumber("<phone number>");
RemoteParticipant remoteParticipant1 = call.addParticipant(acsUser);
RemoteParticipant remoteParticipant2 = call.addParticipant(acsPhone);
```

### <a name="remove-participant-from-a-call"></a>Odebrat účastníka volání
Chcete-li odebrat účastníka ze volání (buď uživatele, nebo telefonního čísla), můžete vyvolat `removeParticipant` .
Tím se asynchronně vyřeší po odebrání účastníka z volání.
Účastník bude také odebrán z `remoteParticipants` kolekce.
```java
RemoteParticipant remoteParticipant = call.getParticipants().get(0);
call.removeParticipant(acsUser).get();
call.removeParticipant(acsPhone).get();
```

## <a name="render-remote-participant-video-streams"></a>Vykreslovat streamy videa vzdáleného účastníka
Pokud chcete zobrazit seznam streamů a sdílení obrazovky pro vzdálené účastníky, Prozkoumejte tyto `videoStreams` kolekce:
```java
RemoteParticipant remoteParticipant = call.getRemoteParticipants().get(0);
RemoteVideoStream remoteParticipantStream = remoteParticipant.getVideoStreams().get(0);
MediaStreamType streamType = remoteParticipantStream.getType(); // of type MediaStreamType.Video or MediaStreamType.ScreenSharing
```
 
Pokud chcete vykreslit `RemoteVideoStream` od vzdáleného účastníka, musíte se přihlásit k odběru `OnVideoStreamsUpdated` události.

Změna `isAvailable` vlastnosti na hodnotu true v rámci události znamená, že vzdálený účastník aktuálně posílá datový proud. Jakmile k tomu dojde, vytvořte novou instanci a `Renderer` pak vytvořte novou `RendererView` pomocí asynchronního `createView` rozhraní API a připojte se `view.target` kdekoli v uživatelském rozhraní aplikace.

Vždy, když se změní dostupnost vzdáleného streamu, můžete zvolit zničení celého zobrazovacího panelu, jeho konkrétního `RendererView` nebo zachování, ale výsledkem bude zobrazení prázdného snímku videa.

```java
Renderer remoteVideoRenderer = new Renderer(remoteParticipantStream, appContext);
View uiView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
layout.addView(uiView);

remoteParticipant.addOnVideoStreamsUpdatedListener(e -> onRemoteParticipantVideoStreamsUpdated(p, e));

void onRemoteParticipantVideoStreamsUpdated(RemoteParticipant participant, RemoteVideoStreamsEvent args) {
    for(RemoteVideoStream stream : args.getAddedRemoteVideoStreams()) {
        if(stream.getIsAvailable()) {
            startRenderingVideo();
        } else {
            renderer.dispose();
        }
    }
}
```

### <a name="remote-video-stream-properties"></a>Vlastnosti vzdáleného streamu videa
Vzdálený video stream má několik vlastností.

* `Id` – ID vzdáleného streamu videa
```java
int id = remoteVideoStream.getId();
```

* `MediaStreamType` – Může být ' video ' nebo ' ScreenSharing '
```java
MediaStreamType type = remoteVideoStream.getType();
```

* `isAvailable` – Určuje, jestli vzdálený koncový bod účastníka aktivně posílá Stream.
```java
boolean availability = remoteVideoStream.getIsAvailable();
```

### <a name="renderer-methods-and-properties"></a>Metody a vlastnosti vykreslovacího modulu
Objekt rendereru následující rozhraní API

* Vytvořte `RendererView` instanci, která může být později připojena v uživatelském rozhraní aplikace pro vykreslení vzdáleného streamu videa.
```java
// Create a view for a video stream
renderer.createView()
```
* Dispose Renderer a vše `RendererView` přidružené k tomuto rendereru Má být volána, když jste odebrali všechna přidružená zobrazení z uživatelského rozhraní.
```java
renderer.dispose()
```

* `StreamSize` -velikost (šířka/výška) vzdáleného streamu videa
```java
StreamSize renderStreamSize = remoteVideoStream.getSize();
int width = renderStreamSize.getWidth();
int height = renderStreamSize.getHeight();
```


### <a name="rendererview-methods-and-properties"></a>Metody a vlastnosti RendererView
Při vytváření `RendererView` můžete zadat `scalingMode` `mirrored` vlastnosti a, které se použijí pro toto zobrazení: Režim škálování může být Stretch | Oříznout | Možnost přizpůsobit, pokud `mirrored` je nastavena na hodnotu `true` , vykreslený datový proud bude vrácen svisle.

```java
Renderer remoteVideoRenderer = new Renderer(remoteVideoStream, appContext);
RendererView rendererView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
```

Vytvořené RendererView lze následně připojit k uživatelskému rozhraní aplikace pomocí následujícího fragmentu kódu:
```java
layout.addView(rendererView);
```

Později můžete aktualizovat režim škálování vyvoláním `updateScalingMode` rozhraní API u objektu RendererView pomocí jedné z ScalingMode. Stretch | ScalingMode. oříznutí | ScalingMode. přizpůsobit jako argument.
```java
// Update the scale mode for this view.
rendererView.updateScalingMode(ScalingMode.Crop)
```


## <a name="device-management"></a>Správa zařízení

`DeviceManager` umožňuje vytvořit výčet místních zařízení, která se dají použít při volání přenosů zvukových a video datových proudů. Umožňuje taky požádat uživatele o oprávnění k přístupu ke svému mikrofonu a kameře pomocí rozhraní API nativního prohlížeče.

Můžete získat přístup `deviceManager` metodou volání `callClient.getDeviceManager()` .
> [!WARNING]
> `callAgent`Pro získání přístupu k DeviceManager se v současné době musí vytvořit objekt, aby se vytvořila první instance.

```java
DeviceManager deviceManager = callClient.getDeviceManager().get();
```

### <a name="enumerate-local-devices"></a>Zobrazení výčtu místních zařízení

Chcete-li získat přístup k místním zařízením, můžete použít metody výčtu na Device Manager. Výčet je synchronní akce.

```java
//  Get a list of available video devices for use.
List<VideoDeviceInfo> localCameras = deviceManager.getCameraList(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
List<AudioDeviceInfo> localMicrophones = deviceManager.getMicrophoneList(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
List<AudioDeviceInfo> localSpeakers = deviceManager.getSpeakerList(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-default-microphonespeaker"></a>Nastavit výchozí mikrofon/mluvčí

Správce zařízení umožňuje nastavit výchozí zařízení, které se použije při spuštění volání.
Pokud nejsou nastavené výchozí hodnoty klienta, komunikační služby se vrátí do výchozího nastavení operačního systému.

```java

// Get the microphone device that is being used.
AudioDeviceInfo defaultMicrophone = deviceManager.getMicrophoneList().get(0);

// Set the microphone device to use.
deviceManager.setMicrophone(defaultMicrophone);

// Get the speaker device that is being used.
AudioDeviceInfo defaultSpeaker = deviceManager.getSpeakerList().get(0);

// Set the speaker device to use.
deviceManager.setSpeaker(defaultSpeaker);
```

### <a name="local-camera-preview"></a>Místní kamera verze Preview

`DeviceManager` `Renderer` K zahájení vykreslování datových proudů z místní kamery můžete použít a. Tento datový proud se nebude posílat jiným účastníkům; je to místní kanál verze Preview. Toto je asynchronní akce.

```java
VideoDeviceInfo videoDevice = <get-video-device>;
Context appContext = this.getApplicationContext();
currentVideoStream = new LocalVideoStream(videoDevice, appContext);
videoOptions = new VideoOptions(currentVideoStream);

Renderer previewRenderer = new Renderer(currentVideoStream, appContext);
View uiView previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));

// Attach the renderingSurface to a viewable location on the app at this point
layout.addView(uiView);
```

## <a name="eventing-model"></a>Model událostí
Můžete se přihlásit k odběru většiny vlastností a kolekcí, které budou oznamovány při změně hodnot.

### <a name="properties"></a>Vlastnosti
Přihlášení k odběru `property changed` událostí:

```java
// subscribe
PropertyChangedListener callStateChangeListener = new PropertyChangedListener()
{
    @Override
    public void onPropertyChanged(PropertyChangedEvent args)
    {
        Log.d("The call state has changed.");
    }
}
call.addOnCallStateChangedListener(callStateChangeListener);

//unsubscribe
call.removeOnCallStateChangedListener(callStateChangeListener);
```

### <a name="collections"></a>Kolekce
Přihlášení k odběru `collection updated` událostí:

```java
LocalVideoStreamsChangedListener localVideoStreamsChangedListener = new LocalVideoStreamsChangedListener()
{
    @Override
    public void onLocalVideoStreamsUpdated(LocalVideoStreamsEvent localVideoStreamsEventArgs) {
        Log.d(localVideoStreamsEventArgs.getAddedStreams().size());
        Log.d(localVideoStreamsEventArgs.getRemovedStreams().size());
    }
}
call.addOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
// To unsubscribe
call.removeOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
```
