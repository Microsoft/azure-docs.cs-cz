---
title: Rychlý Start – přidání volání do aplikace pro iOS pomocí komunikačních služeb Azure
description: V tomto rychlém startu se dozvíte, jak používat komunikační služby Azure, které volají sadu SDK pro iOS.
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 22c9d8f8bdf3e6195bf152fa0431ad5ce9bcdfeb
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "106072875"
---
V tomto rychlém startu se dozvíte, jak spustit volání pomocí komunikačních služeb Azure, které volají sadu SDK pro iOS.

[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-android-ios.md)]

> [!NOTE]
> Tento dokument používá verzi 1.0.0-beta. 8 volání sady SDK.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu budete potřebovat následující požadavky:

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Počítač Mac se systémem [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532)spolu s platným certifikátem pro vývojáře nainstalovaným do řetězce klíčů.
- Nasazený prostředek komunikačních služeb. [Vytvořte prostředek služby Communications](../../create-communication-resource.md).
- [Přístupový token uživatele](../../access-tokens.md) pro komunikační službu Azure

## <a name="setting-up"></a>Nastavení

### <a name="creating-the-xcode-project"></a>Vytvoření projektu Xcode

V Xcode vytvořte nový projekt iOS a vyberte šablonu aplikace s **jedním zobrazením** . V tomto kurzu se používá [SwiftUI Framework](https://developer.apple.com/xcode/swiftui/), takže byste měli nastavit **jazyk** na **SWIFT** a **uživatelské rozhraní** **SwiftUI**. V tomto rychlém startu nebudete vytvářet testy. Můžete si klidně zrušit kontrolu **včetně testů**.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Snímek obrazovky znázorňující okno nového projektu v rámci Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Instalace balíčku a závislostí pomocí CocoaPods

1. Chcete-li vytvořit souboru podfile pro vaši aplikaci, otevřete terminál a přejděte do složky projektu a spusťte příkaz ```pod init```
3. Přidejte následující kód do souboru podfile a uložte (Ujistěte se, že "Target" odpovídá názvu vašeho projektu):

   ```
   platform :ios, '13.0'
   use_frameworks!

   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.8'
     pod 'AzureCommunication', '~> 1.0.0-beta.8'
     pod 'AzureCore', '~> 1.0.0-beta.8'
   end
   ```

3. Spusťte `pod install`.
3. Otevřete `.xcworkspace` s Xcode.

### <a name="request-access-to-the-microphone"></a>Požádat o přístup k mikrofonu

Aby bylo možné získat přístup k mikrofonu zařízení, je třeba aktualizovat seznam vlastností informací o aplikaci pomocí `NSMicrophoneUsageDescription` . Nastavili jste přidruženou hodnotu na `string` , která bude obsažena v dialogovém okně, které systém používá k vyžádání přístupu od uživatele.

Klikněte pravým tlačítkem myši na `Info.plist` položku stromové struktury projektu a vyberte **Otevřít jako**  >  **zdrojový kód**. Přidejte následující řádky do části nejvyšší úrovně `<dict>` a pak soubor uložte.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

Otevřete soubor **contentView. SWIFT** projektu a přidejte `import` do horní části souboru deklaraci, která se má importovat `AzureCommunicationCalling library` . Kromě toho `AVFoundation` budeme v kódu potřebovat pro žádost o oprávnění ke zvuku.

```swift
import AzureCommunicationCalling
import AVFoundation
```

Nahraďte implementaci `ContentView` struktury některými jednoduchými ovládacími prvky uživatelského rozhraní, které umožní uživateli iniciovat a ukončit volání. K těmto ovládacím prvkům v tomto rychlém startu budeme připojovat obchodní logiku.

```swift
struct ContentView: View {
    @State var callee: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Who would you like to call?", text: $callee)
                    Button(action: startCall) {
                        Text("Start Call")
                    }.disabled(callAgent == nil)
                    Button(action: endCall) {
                        Text("End Call")
                    }.disabled(call == nil)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
        }
    }

    func startCall() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                // Add start call logic
            }
        }
    }

    func endCall() {
        // Add end call logic
    }
}
```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce volání sady SDK služby Azure Communications:

| Název                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | CallClient je hlavní vstupní bod pro volání sady SDK.|
| CallAgent | CallAgent se používá ke spouštění a správě volání. |
| CommunicationTokenCredential | CommunicationTokenCredential se používá jako přihlašovací údaje tokenu pro vytvoření instance CallAgent.| 
| CommunicationUserIdentifier | CommunicationUserIdentifier se používá k reprezentaci identity uživatele, která může být jedna z následujících: CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication. |

## <a name="authenticate-the-client"></a>Ověření klienta

Inicializujte `CallAgent` instanci pomocí přístupového tokenu uživatele, který nám umožní přijmout a přijímat volání. Do `onAppear` zpětného volání v **contentView. SWIFT** přidejte následující kód:

```swift
var userCredential: CommunicationTokenCredential?
do {
    userCredential = try CommunicationTokenCredential(token: "<USER ACCESS TOKEN>")
} catch {
    print("ERROR: It was not possible to create user credential.")
    return
}

self.callClient = CallClient()

// Creates the call agent
self.callClient?.createCallAgent(userCredential: userCredential) { (agent, error) in
    if error != nil {
        print("ERROR: It was not possible to create a call agent.")
        return
    }

    if let agent = agent {
        self.callAgent = agent
        print("Call agent successfully created.")
    }
}
```

Musíte nahradit `<USER ACCESS TOKEN>` platným uživatelským tokenem pro přístup k vašemu prostředku. Pokud ještě nemáte k dispozici token, přečtěte si dokumentaci k [tokenu uživatele](../../access-tokens.md) .

## <a name="start-a-call"></a>Spustit volání

`startCall`Metoda je nastavena jako akce, která bude provedena při klepnutí na tlačítko *Spustit volání* . Aktualizujte implementaci a spusťte volání s `ASACallAgent` :

```swift
func startCall()
{
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            // start call logic
            let callees:[CommunicationIdentifier] = [CommunicationUserIdentifier(identifier: self.callee)]
            self.call = self.callAgent?.call(participants: callees, options: StartCallOptions())
        }
    }
}
```

Můžete také použít vlastnosti v nástroji `StartCallOptions` k nastavení počátečních možností pro volání (to znamená, že umožňuje spuštění volání s neztlumeným mikrofonem).

## <a name="end-a-call"></a>Ukončení volání

Implementujte `endCall` metodu pro ukončení aktuálního volání při klepnutí na tlačítko *Ukončit volání* .

```swift
func endCall()
{    
    self.call!.hangup(HangupOptions()) { (error) in
        if (error != nil) {
            print("ERROR: It was not possible to hangup the call.")
        }
    }
}
```

## <a name="run-the-code"></a>Spuštění kódu

Můžete sestavit a spustit aplikaci v simulátoru iOS tak, že vyberete možnost  >  **spuštění** produktu nebo pomocí klávesové zkratky (&#8984;-R).

:::image type="content" source="../media/ios/quick-start-make-call.png" alt-text="Konečný vzhled a chování aplikace rychlý Start":::

Odchozí volání VOIP můžete vytvořit zadáním ID uživatele v textovém poli a klepnutím na tlačítko **Spustit volání** . Volání se `8:echo123` připojí ke robotu s odezvou. to je skvělé pro začátek a ověření, jestli vaše zvuková zařízení fungují. 

> [!NOTE]
> Při prvním volání vás systém vyzve k zadání přístupu k mikrofonu. V produkční aplikaci byste měli použít `AVAudioSession` rozhraní API ke [kontrole stavu oprávnění](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) a řádné aktualizaci chování aplikace, když není udělené oprávnění.

## <a name="sample-code"></a>Příklad kódu

Ukázkovou aplikaci si můžete stáhnout z [GitHubu](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/Add%20Voice%20Calling) .
