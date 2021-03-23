---
title: Rychlý Start – přidání spojení s týmy, které se účastní aplikace pro iOS pomocí komunikačních služeb Azure
description: V tomto rychlém startu se dozvíte, jak používat knihovnu pro platformu Azure Communications Services, která umožňuje vložení knihovny pro iOS.
author: palatter
ms.author: palatter
ms.date: 01/25/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 4d28864d41d6540afc87126daf589ed2929f891d
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803957"
---
V tomto rychlém startu se dozvíte, jak připojit týmy na schůzku pomocí knihovny Azure Communications Services pro iOS.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto rychlého startu budete potřebovat následující požadavky:

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Počítač Mac se systémem [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532)spolu s platným certifikátem pro vývojáře nainstalovaným do řetězce klíčů.
- Nasazený prostředek komunikačních služeb. [Vytvořte prostředek služby Communications](../../create-communication-resource.md).
- [Přístupový token uživatele](../../access-tokens.md) pro komunikační službu Azure
- [CocoaPods](https://cocoapods.org/) je nainstalováno pro vaše prostředí sestavení.

## <a name="setting-up"></a>Nastavení

### <a name="creating-the-xcode-project"></a>Vytvoření projektu Xcode

V Xcode vytvořte nový projekt iOS a vyberte šablonu **aplikace** . Budeme používat scénáře UIKit. V průběhu tohoto rychlého startu nebudete vytvářet testy. Můžete si klidně zrušit kontrolu **včetně testů**.

:::image type="content" source="../media/ios/xcode-new-project-template-select.png" alt-text="Snímek obrazovky znázorňující výběr šablony nového projektu v rámci Xcode.":::

Pojmenujte projekt `TeamsEmbedGettingStarted` .

:::image type="content" source="../media/ios/xcode-new-project-details.png" alt-text="Snímek obrazovky zobrazující podrobnosti o novém projektu v rámci Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Instalace balíčku a závislostí pomocí CocoaPods

1. Vytvořte souboru podfile pro vaši aplikaci:

```
platform :ios, '12.0'
use_frameworks!

target 'TeamsEmbedGettingStarted' do
    pod 'AzureCommunication', '~> 1.0.0-beta.8'
end

azure_libs = [
'AzureCommunication',
'AzureCore']

post_install do |installer|
    installer.pods_project.targets.each do |target|
    if azure_libs.include?(target.name)
        puts "Adding BUILD_LIBRARY_FOR_DISTRIBUTION to #{target.name}"
        target.build_configurations.each do |config|
        xcconfig_path = config.base_configuration_reference.real_path
        File.open(xcconfig_path, "a") {|file| file.puts "BUILD_LIBRARY_FOR_DISTRIBUTION = YES"}
        end
    end
    end
end
```

2. Spusťte `pod install`.
3. Otevřete vygenerovanou `.xcworkspace` pomocí Xcode.

### <a name="request-access-to-the-microphone-camera-etc"></a>Požádat o přístup k mikrofonu, fotoaparátu atd.

Pokud chcete získat přístup k hardwaru zařízení, aktualizujte seznam vlastností informací o vaší aplikaci. Nastavte přidruženou hodnotu na `string` , která bude obsažena v dialogovém okně, které systém používá k vyžádání přístupu od uživatele.

Klikněte pravým tlačítkem myši na `Info.plist` položku stromové struktury projektu a vyberte **Otevřít jako**  >  **zdrojový kód**. Přidejte následující řádky do části nejvyšší úrovně `<dict>` a pak soubor uložte.

```xml
<key>NSBluetoothAlwaysUsageDescription</key>
<string></string>
<key>NSBluetoothPeripheralUsageDescription</key>
<string></string>
<key>NSCameraUsageDescription</key>
<string></string>
<key>NSContactsUsageDescription</key>
<string></string>
<key>NSMicrophoneUsageDescription</key>
<string></string>
```

### <a name="add-the-teams-embed-framework"></a>Přidat rozhraní pro vložení týmů

1. Stáhněte si rozhraní.
2. Vytvořte `Frameworks` složku v kořenu projektu. Například `\TeamsEmbedGettingStarted\Frameworks\`
3. Zkopírujte stažená `TeamsAppSDK.framework` `MeetingUIClient.framework` rozhraní a do této složky.
4. Přidejte `TeamsAppSDK.framework` a `MeetingUIClient.framework` do cíle projektu na kartě Obecné. pomocí nástroje `Add Other`  ->  `Add Files...` přejděte do souborů rozhraní a přidejte je.

:::image type="content" source="../media/ios/xcode-add-frameworks.png" alt-text="Snímek obrazovky znázorňující přidaná rozhraní v Xcode":::

5. Pokud ještě není, přidejte `$(PROJECT_DIR)/Frameworks` na `Framework Search Paths` kartu nastavení cílového sestavení projektu. Chcete-li najít nastavení, změňte filtr z `basic` na `all` , můžete také použít panel hledání na pravé straně.

:::image type="content" source="../media/ios/xcode-add-framework-search-path.png" alt-text="Snímek obrazovky znázorňující cestu pro hledání rozhraní v Xcode":::

### <a name="turn-off-bitcode"></a>Vypnout Bitcode

Nastavte `Enable Bitcode` možnost na `No` nastavení sestavení projektu. Chcete-li najít nastavení, změňte filtr z `basic` na `all` , můžete také použít panel hledání na pravé straně.

:::image type="content" source="../media/ios/xcode-bitcode-option.png" alt-text="Snímek obrazovky znázorňující možnost BitCode v Xcode":::

### <a name="add-framework-signing-script"></a>Přidat podpisový skript rozhraní

Vyberte cíl aplikace a zvolte `Build Phases` kartu.  Pak klikněte na, a potom na `+` `New Run Script Phase` . Zajistěte, aby k této nové fázi došlo po `Embed Frameworks` fázích.



:::image type="content" source="../media/ios/xcode-build-script.png" alt-text="Snímek obrazovky znázorňující přidání skriptu sestavení do Xcode":::

```bash
#!/bin/sh
if [ -d "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks ]; then
    pushd "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks
    for EACH in *.framework; do
        echo "-- signing ${EACH}"
        /usr/bin/codesign --force --deep --sign "${EXPANDED_CODE_SIGN_IDENTITY}" --entitlements "${TARGET_TEMP_DIR}/${PRODUCT_NAME}.app.xcent" --timestamp=none $EACH
        echo "-- moving ${EACH}"
        mv -nv ${EACH} ../../
    done
    rm -rf "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks
    popd
    echo "BUILD DIR ${TARGET_BUILD_DIR}"
fi
```

### <a name="turn-on-voice-over-ip-background-mode"></a>Zapnout režim hlas přes IP adresu v režimu na pozadí.

Vyberte cíl aplikace a klikněte na kartu Možnosti.

Zapněte `Background Modes` kliknutím na v `+ Capabilities` horní části a výběrem `Background Modes` .

Zaškrtněte políčko pro `Voice over IP` .

:::image type="content" source="../media/ios/xcode-background-voip.png" alt-text="Snímek obrazovky s povoleným VOIP v Xcode":::

### <a name="add-a-window-reference-to-appdelegate"></a>Přidat odkaz na okno na AppDelegate

Otevřete soubor **AppDelegate. SWIFT** projektu a přidejte odkaz na Window.

```swift
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?
```

### <a name="add-a-button-to-the-viewcontroller"></a>Přidání tlačítka do soubor viewcontroller

Vytvořte tlačítko ve `viewDidLoad` zpětném volání v **soubor viewcontroller. SWIFT**.

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    
    let button = UIButton(frame: CGRect(x: 100, y: 100, width: 200, height: 50))
    button.backgroundColor = .black
    button.setTitle("Join Meeting", for: .normal)
    button.addTarget(self, action: #selector(joinMeetingTapped), for: .touchUpInside)
    
    button.translatesAutoresizingMaskIntoConstraints = false
    self.view.addSubview(button)
    button.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    button.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
```

Vytvoří zásuvku pro tlačítko v **soubor viewcontroller. SWIFT**.

```swift
@IBAction func joinMeetingTapped(_ sender: UIButton) {

}
```

### <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

Otevřete soubor **soubor viewcontroller. SWIFT** projektu a přidejte `import` do horní části souboru deklaraci, která importuje `AzureCommunication library` a `MeetingUIClient` . 

```swift
import UIKit
import AzureCommunication
import MeetingUIClient
```

Nahrazením implementace `ViewController` třídy jednoduchým tlačítkem umožníte uživateli připojit se ke schůzce. Obchodní logika budeme připojovat k tlačítku v tomto rychlém startu.

```swift
class ViewController: UIViewController {

    private var meetingUIClient: MeetingUIClient?
    
    override func viewDidLoad() {
        super.viewDidLoad()

        // Initialize meetingClient
    }

    @IBAction func joinMeetingTapped(_ sender: UIButton) {
        joinMeeting()
    }
    
    private func joinMeeting() {
        // Add join meeting logic
    }
}
```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce knihovny Azure Communications Services pro vložení:

| Název                                  | Popis                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| MeetingUIClient | MeetingUIClient je hlavní vstupní bod do knihovny pro vkládání týmů. |
| MeetingUIClientDelegate | MeetingUIClientDelegate slouží k přijímání událostí, jako jsou například změny ve stavu volání. |
| MeetingJoinOptions | MeetingJoinOptions se používají pro konfigurovatelné možnosti, jako je zobrazované jméno. | 
| CallState | CallState slouží k vytváření sestav o změnách stavu volání. Možnosti jsou následující: připojení, waitingInLobby, připojení a ukončení. |

## <a name="create-and-authenticate-the-client"></a>Vytvoření a ověření klienta

Inicializujte `MeetingUIClient` instanci pomocí přístupového tokenu uživatele, který nám umožní připojit se ke schůzce. Do `viewDidLoad` zpětného volání v **soubor viewcontroller. SWIFT** přidejte následující kód:

```swift
do {
    let communicationTokenRefreshOptions = CommunicationTokenRefreshOptions(initialToken: "<USER_ACCESS_TOKEN>", refreshProactively: true, tokenRefresher: fetchTokenAsync(completionHandler:))
    let credential = try CommunicationTokenCredential(with: communicationTokenRefreshOptions)
    meetingUIClient = MeetingUIClient(with: credential)
}
catch {
    print("Failed to create communication token credential")
}
```

Nahraďte `<USER_ACCESS_TOKEN>` platným tokenem přístupu uživatele pro váš prostředek. Pokud ještě nemáte k dispozici token, přečtěte si dokumentaci k [tokenu uživatele](../../access-tokens.md) .

### <a name="setup-token-refreshing"></a>Aktualizace tokenu instalace

Vytvořte metodu `fetchTokenAsync`. Pak přidejte `fetchToken` logiku pro získání tokenu uživatele.

```swift
private func fetchTokenAsync(completionHandler: @escaping TokenRefreshOnCompletion) {
    func getTokenFromServer(completionHandler: @escaping (String) -> Void) {
        completionHandler("<USER_ACCESS_TOKEN>")
    }
    getTokenFromServer { newToken in
        completionHandler(newToken, nil)
    }
}
```

Nahraďte `<USER_ACCESS_TOKEN>` platným tokenem přístupu uživatele pro váš prostředek.

## <a name="join-a-meeting"></a>Připojit se ke schůzce

`joinMeeting`Metoda je nastavena jako akce, která bude provedena při klepnutí na tlačítko *Schůzka JOIN* . Aktualizujte implementaci a připojte se ke schůzce s těmito `MeetingUIClient` kroky:

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingJoinOptions(displayName: "John Smith")
        
    meetingUIClient?.join(meetingUrl: "<MEETING_URL>", meetingJoinOptions: meetingJoinOptions, completionHandler: { (error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
    })
}
```

Nahraďte `<MEETING URL>` odkazem na schůzku týmů.

### <a name="get-a-teams-meeting-link"></a>Získat odkaz na schůzku týmu

Propojení týmů lze načíst pomocí rozhraní Graph API. Tato část je podrobně popsána v [dokumentaci ke grafům](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true).
Komunikační služby, které volá sadu SDK, přijímají úplný tým odkaz na schůzku. Tento odkaz se vrátí jako součást `onlineMeeting` prostředku, který je přístupný v rámci [ `joinWebUrl` vlastnosti](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) . můžete také získat požadované informace o schůzce z adresy URL **připojení ke schůzce** v rámci pozvánky na schůzku.

## <a name="run-the-code"></a>Spuštění kódu

Můžete sestavit a spustit aplikaci v simulátoru iOS tak, že vyberete možnost  >  **spuštění** produktu nebo pomocí klávesové zkratky (&#8984;-R).

:::image type="content" source="../media/ios/quick-start-join-meeting.png" alt-text="Konečný vzhled a chování aplikace rychlý Start":::

:::image type="content" source="../media/ios/quick-start-meeting-joined.png" alt-text="Konečný vzhled a chování po spojení schůzky":::

> [!NOTE]
> Při prvním připojení ke schůzce vás systém vyzve k zadání přístupu k mikrofonu. V produkční aplikaci byste měli použít `AVAudioSession` rozhraní API ke [kontrole stavu oprávnění](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) a řádné aktualizaci chování aplikace, když není udělené oprávnění.

## <a name="add-localization-support-based-on-your-app"></a>Přidání podpory lokalizace na základě vaší aplikace

Sada Microsoft Teams SDK podporuje více než 100 řetězců a prostředků. Sada rozhraní obsahuje základní a anglické jazyky. Zbytek je obsažen v souboru, který je `Localizations.zip` součástí balíčku.

#### <a name="add-localizations-to-the-sdk-based-on-what-your-app-supports"></a>Přidání lokalizací do sady SDK na základě toho, co vaše aplikace podporuje

1. Určete, jaký druh lokalizací vaše aplikace podporuje z projektu app Xcode Project > info > seznam lokalizací.
2. Rozbalit Localizations.zip, který je součástí balíčku
3. Zkopírujte lokalizační složky ze složky unzip na základě toho, co vaše aplikace podporuje, do kořenového adresáře rozhraní TeamsAppSDK. Framework.

## <a name="preparation-for-app-store-upload"></a>Příprava nahrávání do App Storu

V případě archivace z rozhraní odeberte architektury i386 a x86_64.

Přidejte `i386` architektury a `x86_64` pro odebrání skriptu do fáze sestavení před fází kodesign architektury rozhraní, pokud chcete aplikaci archivovat.

V Navigátoru projektu vyberte svůj projekt. V podokně editoru přejděte na fáze sestavení → klikněte na + Sign → vytvořit novou fázi spuštění skriptu.

```bash
echo "Target architectures: $ARCHS"
APP_PATH="${TARGET_BUILD_DIR}/${WRAPPER_NAME}"
find "$APP_PATH" -name '*.framework' -type d | while read -r FRAMEWORK
do
FRAMEWORK_EXECUTABLE_NAME=$(defaults read "$FRAMEWORK/Info.plist" CFBundleExecutable)
FRAMEWORK_EXECUTABLE_PATH="$FRAMEWORK/$FRAMEWORK_EXECUTABLE_NAME"
echo "Executable is $FRAMEWORK_EXECUTABLE_PATH"
echo $(lipo -info "$FRAMEWORK_EXECUTABLE_PATH")
FRAMEWORK_TMP_PATH="$FRAMEWORK_EXECUTABLE_PATH-tmp"
# remove simulator's archs if location is not simulator's directory
case "${TARGET_BUILD_DIR}" in
*"iphonesimulator")
    echo "No need to remove archs"
    ;;
*)
    if $(lipo "$FRAMEWORK_EXECUTABLE_PATH" -verify_arch "i386") ; then
    lipo -output "$FRAMEWORK_TMP_PATH" -remove "i386" "$FRAMEWORK_EXECUTABLE_PATH"
    echo "i386 architecture removed"
    rm "$FRAMEWORK_EXECUTABLE_PATH"
    mv "$FRAMEWORK_TMP_PATH" "$FRAMEWORK_EXECUTABLE_PATH"
    fi
    if $(lipo "$FRAMEWORK_EXECUTABLE_PATH" -verify_arch "x86_64") ; then
    lipo -output "$FRAMEWORK_TMP_PATH" -remove "x86_64" "$FRAMEWORK_EXECUTABLE_PATH"
    echo "x86_64 architecture removed"
    rm "$FRAMEWORK_EXECUTABLE_PATH"
    mv "$FRAMEWORK_TMP_PATH" "$FRAMEWORK_EXECUTABLE_PATH"
    fi
    ;;
esac
echo "Completed for executable $FRAMEWORK_EXECUTABLE_PATH"
echo $(lipo -info "$FRAMEWORK_EXECUTABLE_PATH")
done
```

## <a name="sample-code"></a>Příklad kódu

Ukázkovou aplikaci si můžete stáhnout z [GitHubu](https://github.com/Azure-Samples/teams-embed-ios-getting-started) .
