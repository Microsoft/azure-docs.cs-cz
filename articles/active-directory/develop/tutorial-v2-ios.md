---
title: 'Kurz: Vytvoření aplikace pro iOS nebo macOS, která používá Microsoft Identity Platform pro ověřování | Azure'
titleSuffix: Microsoft identity platform
description: V tomto kurzu vytvoříte aplikaci pro iOS nebo macOS, která používá Microsoft Identity Platform k přihlašování uživatelů a získání přístupového tokenu pro volání rozhraní API Microsoft Graph jménem.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 09/18/2020
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: c9e4997ad08f2dd1d96dd442f80ad4203abf6261
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015882"
---
# <a name="tutorial-sign-in-users-and-call-microsoft-graph-from-an-ios-or-macos-app"></a>Kurz: přihlášení uživatelů a volání Microsoft Graph z aplikace pro iOS nebo macOS

V tomto kurzu vytvoříte aplikaci pro iOS nebo macOS, která se integruje s platformou Microsoft identity k podepisování uživatelů a získání přístupového tokenu pro volání rozhraní API Microsoft Graph.

Po dokončení průvodce bude aplikace přijímat přihlašovacíky osobních účtů Microsoft (včetně outlook.com, live.com a dalších) a pracovních nebo školních účtů z jakékoli společnosti nebo organizace, která používá Azure Active Directory. Tento kurz se týká aplikací pro iOS i macOS. Některé kroky se mezi těmito dvěma platformami liší.

V tomto kurzu:

> [!div class="checklist"]
> * Vytvoření projektu aplikace pro iOS nebo macOS v *Xcode*
> * Registrace aplikace v Azure Portal
> * Přidat kód pro podporu přihlášení a odhlášení uživatele
> * Přidat kód pro volání rozhraní Microsoft Graph API
> * Otestování aplikace

## <a name="prerequisites"></a>Požadavky

- [Xcode 11. x +](https://developer.apple.com/xcode/)

## <a name="how-tutorial-app-works"></a>Jak funguje aplikace kurz

![Ukazuje, jak ukázková aplikace vygenerovaná tímto kurzem funguje](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

Aplikace v tomto kurzu se může přihlásit k uživatelům a získat data od Microsoft Graph jménem. Tato data budou k dispozici prostřednictvím chráněného rozhraní API (Microsoft Graph API v tomto případě), které vyžaduje autorizaci a jsou chráněny platformou Microsoft identity.

A konkrétně:

* Vaše aplikace se přihlásí k uživateli přes prohlížeč nebo Microsoft Authenticator.
* Koncový uživatel bude akceptovat oprávnění, která vaše aplikace požadovala.
* Vaše aplikace se vydá přístupový token pro rozhraní Microsoft Graph API.
* Přístupový token bude součástí požadavku HTTP webovému rozhraní API.
* Zpracujte odpověď Microsoft Graph.

Tato ukázka implementuje ověřování pomocí knihovny Microsoft Authentication Library (MSAL). MSAL bude automaticky obnovovat tokeny, poskytovat jednotné přihlašování (SSO) mezi ostatními aplikacemi na zařízení a spravovat účty.

Pokud chcete stáhnout dokončenou verzi aplikace, kterou jste vytvořili v tomto kurzu, můžete najít obě verze na GitHubu:

- [Ukázka kódu pro iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/) (GitHub)
- [Ukázka kódu MacOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/) (GitHub)

## <a name="create-a-new-project"></a>Vytvoření nového projektu

1. Otevřete Xcode a vyberte **vytvořit nový projekt Xcode**.
2. V případě aplikací pro iOS vyberte možnost  >  **aplikace s jedním zobrazením** pro iOS a vyberte **Další**.
3. V případě aplikací MacOS vyberte **MacOS**  >  **aplikace pro kakao** a vyberte **Další**.
4. Zadejte název produktu.
5. Nastavte **jazyk** na **SWIFT** a vyberte **Další**.
6. Vyberte složku pro vytvoření aplikace a vyberte **vytvořit**.

## <a name="register-your-application"></a>Registrace aplikace

1. Přihlaste se <a href="https://portal.azure.com/" target="_blank">k <span class="docon docon-navigate-external x-hidden-focus"></span> Azure Portal</a>.
1. Máte-li přístup k více klientům, použijte filtr **adresář + odběr** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
1. Vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Registrace aplikací**  >  **Nová registrace**.
1. Zadejte **název** vaší aplikace. Uživatel vaší aplikace může tento název zobrazit a později ho můžete změnit.
1. V části **podporované typy účtů** vyberte **účty v jakémkoli adresáři organizace (libovolný Azure AD Directory – víceklientské klienty) a osobní účty Microsoft (např. Skype, Xbox)** .
1. Vyberte **Zaregistrovat**.
1. V části **Spravovat** vyberte **ověřování**  >  **Přidat platformu**  >  **iOS/MacOS**.
1. Zadejte ID sady projektu. Pokud jste kód stáhli, je to `com.microsoft.identitysample.MSALiOS` . Pokud vytváříte vlastní projekt, vyberte projekt v Xcode a otevřete kartu **Obecné** . Identifikátor sady prostředků se zobrazí v oddílu **Identita** .
1. Vyberte **Konfigurovat** a uložte **konfiguraci MSAL** , která se zobrazí na stránce **Konfigurace MSAL** , abyste ji mohli zadat při pozdější konfiguraci aplikace. 
1. Vyberte **Hotovo**.

## <a name="add-msal"></a>Přidat MSAL

Vyberte jeden z následujících způsobů, jak nainstalovat knihovnu MSAL do vaší aplikace:

### <a name="cocoapods"></a>CocoaPods

1. Pokud používáte [CocoaPods](https://cocoapods.org/), nainstalujte `MSAL` nejprve vytvořením prázdného souboru `podfile` ve stejné složce jako `.xcodeproj` soubor projektu. Přidejte následující `podfile` :

   ```
   use_frameworks!

   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. Nahraďte `<your-target-here>` názvem vašeho projektu.
3. V okně terminálu přejděte do složky, která obsahuje `podfile` vámi vytvořenou a spuštěnou `pod install` pro instalaci knihovny MSAL.
4. Zavřete Xcode a otevřete a `<your project name>.xcworkspace` znovu načtěte projekt v Xcode.

### <a name="carthage"></a>Carthage

Pokud používáte [Carthage](https://github.com/Carthage/Carthage), nainstalujte `MSAL` ho přidáním do `Cartfile` :

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

V okně terminálu ve stejném adresáři jako aktualizovaný `Cartfile` Spusťte následující příkaz, aby Carthage aktualizoval závislosti ve vašem projektu.

iOS:

```bash
carthage update --platform iOS
```

MacOS

```bash
carthage update --platform macOS
```

### <a name="manually"></a>Ručně

Můžete také použít dílčí modul Git nebo si zaregistrovat nejnovější verzi pro použití jako rozhraní ve vaší aplikaci.

## <a name="add-your-app-registration"></a>Přidání registrace aplikace

V dalším kroku přidáme vaši registraci vaší aplikace do vašeho kódu.

Nejprve přidejte následující příkaz Import na začátek a `ViewController.swift` také `AppDelegate.swift` `SceneDelegate.swift` soubory nebo:

```swift
import MSAL
```

Pak přidejte následující kód `ViewController.swift` před `viewDidLoad()` :

```swift
// Update the below to your client ID you received in the portal. The below is for running the demo only
let kClientID = "Your_Application_Id_Here"
let kGraphEndpoint = "https://graph.microsoft.com/" // the Microsoft Graph endpoint
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization's Azure AD tenant to sign in

let kScopes: [String] = ["user.read"] // request permission to read the profile of the signed-in user

var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParameters : MSALWebviewParameters?
var currentAccount: MSALAccount?
```

Jedinou hodnotou, kterou je třeba upravit výše, je hodnota přiřazená k `kClientID` ID vaší [aplikace](./developer-glossary.md#application-id-client-id). Tato hodnota je součástí dat konfigurace MSAL, která jste uložili během kroku na začátku tohoto kurzu, abyste aplikaci zaregistrovali v Azure Portal.

## <a name="configure-xcode-project-settings"></a>Konfigurace nastavení projektu Xcode

Přidejte novou skupinu řetězce klíčů k **funkcím podepisování projektu &**. Skupina řetězce klíčů by měla být `com.microsoft.adalcache` v systému iOS a `com.microsoft.identity.universalstorage` v MacOS.

![Uživatelské rozhraní Xcode zobrazující, jak by se měla nastavit skupina klíčů klíčů](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro-keychainShare.png)

## <a name="for-ios-only-configure-url-schemes"></a>Jenom pro iOS, konfigurace schémat URL

V tomto kroku se zaregistrujete `CFBundleURLSchemes` tak, aby se uživatel mohl po přihlášení přesměrovat zpátky do aplikace. Díky `LSApplicationQueriesSchemes` tomu také umožňuje, aby vaše aplikace mohla využívat Microsoft Authenticator.

V Xcode otevřete `Info.plist` jako soubor zdrojového kódu a do oddílu přidejte následující `<dict>` . Nahraďte `[BUNDLE_ID]` hodnotou, kterou jste použili v Azure Portal. Pokud jste kód stáhli, je identifikátor sady prostředků `com.microsoft.identitysample.MSALiOS` . Pokud vytváříte vlastní projekt, vyberte projekt v Xcode a otevřete kartu **Obecné** . Identifikátor sady prostředků se zobrazí v oddílu **Identita** .

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.[BUNDLE_ID]</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauthv2</string>
    <string>msauthv3</string>
</array>
```

## <a name="for-macos-only-configure-app-sandbox"></a>Pro macOS, konfigurace izolovaného prostoru aplikace

1. Přejít na nastavení projektu Xcode > **Možnosti**  >  **aplikačního prostoru aplikace** TAB
2. Zaškrtněte políčko **odchozí připojení (klient)** .

## <a name="create-your-apps-ui"></a>Vytvoření uživatelského rozhraní vaší aplikace

Nyní vytvořte uživatelské rozhraní, které obsahuje tlačítko pro volání rozhraní Microsoft Graph API, jiné pro odhlášení a textové zobrazení pro zobrazení výstupu přidáním následujícího kódu do `ViewController` třídy:

### <a name="ios-ui"></a>uživatelské rozhraní iOS

```swift
var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!
var usernameLabel: UILabel!

func initUI() {

    usernameLabel = UILabel()
    usernameLabel.translatesAutoresizingMaskIntoConstraints = false
    usernameLabel.text = ""
    usernameLabel.textColor = .darkGray
    usernameLabel.textAlignment = .right

    self.view.addSubview(usernameLabel)

    usernameLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
    usernameLabel.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -10.0).isActive = true
    usernameLabel.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
    usernameLabel.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    // Add call Graph button
    callGraphButton  = UIButton()
    callGraphButton.translatesAutoresizingMaskIntoConstraints = false
    callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
    callGraphButton.setTitleColor(.blue, for: .normal)
    callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
    self.view.addSubview(callGraphButton)

    callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 120.0).isActive = true
    callGraphButton.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
    callGraphButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    // Add sign out button
    signOutButton = UIButton()
    signOutButton.translatesAutoresizingMaskIntoConstraints = false
    signOutButton.setTitle("Sign Out", for: .normal)
    signOutButton.setTitleColor(.blue, for: .normal)
    signOutButton.setTitleColor(.gray, for: .disabled)
    signOutButton.addTarget(self, action: #selector(signOut(_:)), for: .touchUpInside)
    self.view.addSubview(signOutButton)

    signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
    signOutButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
    signOutButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    let deviceModeButton = UIButton()
    deviceModeButton.translatesAutoresizingMaskIntoConstraints = false
    deviceModeButton.setTitle("Get device info", for: .normal);
    deviceModeButton.setTitleColor(.blue, for: .normal);
    deviceModeButton.addTarget(self, action: #selector(getDeviceMode(_:)), for: .touchUpInside)
    self.view.addSubview(deviceModeButton)

    deviceModeButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    deviceModeButton.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
    deviceModeButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
    deviceModeButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    // Add logging textfield
    loggingText = UITextView()
    loggingText.isUserInteractionEnabled = false
    loggingText.translatesAutoresizingMaskIntoConstraints = false

    self.view.addSubview(loggingText)

    loggingText.topAnchor.constraint(equalTo: deviceModeButton.bottomAnchor, constant: 10.0).isActive = true
    loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
    loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
    loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
}

func platformViewDidLoadSetup() {

    NotificationCenter.default.addObserver(self,
                        selector: #selector(appCameToForeGround(notification:)),
                        name: UIApplication.willEnterForegroundNotification,
                        object: nil)

}

@objc func appCameToForeGround(notification: Notification) {
    self.loadCurrentAccount()
}

```

### <a name="macos-ui"></a>uživatelské rozhraní macOS

```swift

var callGraphButton: NSButton!
var loggingText: NSTextView!
var signOutButton: NSButton!

var usernameLabel: NSTextField!

func initUI() {

    usernameLabel = NSTextField()
    usernameLabel.translatesAutoresizingMaskIntoConstraints = false
    usernameLabel.stringValue = ""
    usernameLabel.isEditable = false
    usernameLabel.isBezeled = false
    self.view.addSubview(usernameLabel)

    usernameLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 30.0).isActive = true
    usernameLabel.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -10.0).isActive = true

    // Add call Graph button
    callGraphButton  = NSButton()
    callGraphButton.translatesAutoresizingMaskIntoConstraints = false
    callGraphButton.title = "Call Microsoft Graph API"
    callGraphButton.target = self
    callGraphButton.action = #selector(callGraphAPI(_:))
    callGraphButton.bezelStyle = .rounded
    self.view.addSubview(callGraphButton)

    callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
    callGraphButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true

    // Add sign out button
    signOutButton = NSButton()
    signOutButton.translatesAutoresizingMaskIntoConstraints = false
    signOutButton.title = "Sign Out"
    signOutButton.target = self
    signOutButton.action = #selector(signOut(_:))
    signOutButton.bezelStyle = .texturedRounded
    self.view.addSubview(signOutButton)

    signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
    signOutButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
    signOutButton.isEnabled = false

    // Add logging textfield
    loggingText = NSTextView()
    loggingText.translatesAutoresizingMaskIntoConstraints = false

    self.view.addSubview(loggingText)

    loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
    loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
    loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
    loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: -10.0).isActive = true
    loggingText.widthAnchor.constraint(equalToConstant: 500.0).isActive = true
    loggingText.heightAnchor.constraint(equalToConstant: 300.0).isActive = true
}

func platformViewDidLoadSetup() {}

```

Dále v rámci `ViewController` třídy nahraďte `viewDidLoad()` metodu následujícím způsobem:

```swift
    override func viewDidLoad() {

        super.viewDidLoad()

        initUI()

        do {
            try self.initMSAL()
        } catch let error {
            self.updateLogging(text: "Unable to create Application Context \(error)")
        }

        self.loadCurrentAccount()
        self.platformViewDidLoadSetup()
    }
```

## <a name="use-msal"></a>Použití MSAL

### <a name="initialize-msal"></a>Inicializovat MSAL

`initMSAL`Do třídy přidejte následující metodu `ViewController` :

```swift
    func initMSAL() throws {

        guard let authorityURL = URL(string: kAuthority) else {
            self.updateLogging(text: "Unable to create authority URL")
            return
        }

        let authority = try MSALAADAuthority(url: authorityURL)

        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
        self.initWebViewParams()
    }
```

Do třídy přidejte následující `initMSAL` metodu `ViewController` .

### <a name="ios-code"></a>kód pro iOS:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters(authPresentationViewController: self)
    }
```

### <a name="macos-code"></a>macOS kód:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters()
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>Pouze pro iOS, zpracování zpětného volání přihlášení

Otevřete soubor `AppDelegate.swift`. Pro zpracování zpětného volání po přihlášení přidejte `MSALPublicClientApplication.handleMSALResponse` do `appDelegate` třídy takto:

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**Pokud používáte Xcode 11**, měli byste místo toho umístit MSAL zpětné volání do `SceneDelegate.swift` .
Pokud podporujete UISceneDelegate i UIApplicationDelegate pro zajištění kompatibility se staršími systémy iOS, je nutné MSAL zpětné volání umístit do obou souborů.

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {

        guard let urlContext = URLContexts.first else {
            return
        }

        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication

        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

#### <a name="acquire-tokens"></a>Získat tokeny

Nyní můžeme implementovat logiku zpracování uživatelského rozhraní aplikace a interaktivně získat tokeny prostřednictvím MSAL.

MSAL zpřístupňuje dvě primární metody získání tokenů: `acquireTokenSilently()` a `acquireTokenInteractively()` :

- `acquireTokenSilently()` pokusí se přihlásit uživatele a získat tokeny bez zásahu uživatele, pokud je přítomen účet. `acquireTokenSilently()` vyžaduje poskytnutí platného `MSALAccount` , který lze načíst pomocí některého z rozhraní API výčtu MSAL účtu. Tato ukázka používá `applicationContext.getCurrentAccount(with: msalParameters, completionBlock: {})` k načtení aktuálního účtu.

- `acquireTokenInteractively()` Při pokusu o přihlášení uživatele vždy zobrazuje uživatelské rozhraní. K zajištění interaktivního prostředí jednotného přihlašování může používat soubory cookie relace v prohlížeči nebo účtu v rámci služby Microsoft Authenticator.

Do třídy přidejte následující kód `ViewController` :

```swift
    func getGraphEndpoint() -> String {
        return kGraphEndpoint.hasSuffix("/") ? (kGraphEndpoint + "v1.0/me/") : (kGraphEndpoint + "/v1.0/me/");
    }

    @objc func callGraphAPI(_ sender: AnyObject) {

        self.loadCurrentAccount { (account) in

            guard let currentAccount = account else {

                // We check to see if we have a current logged in account.
                // If we don't, then we need to sign someone in.
                self.acquireTokenInteractively()
                return
            }

            self.acquireTokenSilently(currentAccount)
        }
    }

    typealias AccountCompletion = (MSALAccount?) -> Void

    func loadCurrentAccount(completion: AccountCompletion? = nil) {

        guard let applicationContext = self.applicationContext else { return }

        let msalParameters = MSALParameters()
        msalParameters.completionBlockQueue = DispatchQueue.main

        applicationContext.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

            if let error = error {
                self.updateLogging(text: "Couldn't query current account with error: \(error)")
                return
            }

            if let currentAccount = currentAccount {

                self.updateLogging(text: "Found a signed in account \(String(describing: currentAccount.username)). Updating data for that account...")

                self.updateCurrentAccount(account: currentAccount)

                if let completion = completion {
                    completion(self.currentAccount)
                }

                return
            }

            self.updateLogging(text: "Account signed out. Updating UX")
            self.accessToken = ""
            self.updateCurrentAccount(account: nil)

            if let completion = completion {
                completion(nil)
            }
        })
    }
```

#### <a name="get-a-token-interactively"></a>Interaktivní získání tokenu

Následující fragment kódu získá token poprvé, a to vytvořením `MSALInteractiveTokenParameters` objektu a voláním `acquireToken` . Dále přidáte kód, který:

1. Vytvoří `MSALInteractiveTokenParameters` s rozsahy.
2. Volání `acquireToken()` s vytvořenými parametry.
3. Zpracovává chyby. Další podrobnosti najdete v tématu [Průvodce zpracováním chyb MSAL pro iOS a MacOS](msal-error-handling-ios.md).
4. Zpracuje úspěšný případ.

Do třídy `ViewController` přidejte následující kód.

```swift
func acquireTokenInteractively() {

    guard let applicationContext = self.applicationContext else { return }
    guard let webViewParameters = self.webViewParameters else { return }

    // #1
    let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: webViewParameters)
    parameters.promptType = .selectAccount

    // #2
    applicationContext.acquireToken(with: parameters) { (result, error) in

        // #3
        if let error = error {

            self.updateLogging(text: "Could not acquire token: \(error)")
            return
        }

        guard let result = result else {

            self.updateLogging(text: "Could not acquire token: No result returned")
            return
        }

        // #4
        self.accessToken = result.accessToken
        self.updateLogging(text: "Access token is \(self.accessToken)")
        self.updateCurrentAccount(account: result.account)
        self.getContentWithToken()
    }
}
```

`promptType`Vlastnost `MSALInteractiveTokenParameters` konfiguruje chování výzvy ověřování a vyjádření souhlasu. Podporovány jsou následující hodnoty:

- `.promptIfNecessary` (výchozí) – uživateli se zobrazí výzva pouze v případě potřeby. Možnosti jednotného přihlašování se určují podle přítomnosti souborů cookie ve webviewu a typu účtu. Pokud se přihlásí více uživatelů, zobrazí se možnosti výběru účtu. *Toto je výchozí chování*.
- `.selectAccount` – Pokud není zadán žádný uživatel, zobrazí se v ověřovacím WebView seznam aktuálně přihlášených účtů, ze kterého může uživatel vybírat.
- `.login` – Vyžaduje, aby se uživatel ověřil ve webviewu. Pokud zadáte tuto hodnotu, může být v okamžiku přihlášení pouze jeden účet.
- `.consent` – Vyžaduje souhlas uživatele s aktuální sadou oborů pro daný požadavek.

#### <a name="get-a-token-silently"></a>Bezobslužné získání tokenu

Chcete-li získat aktualizovaný token tiše, přidejte do třídy následující kód `ViewController` . Vytvoří `MSALSilentTokenParameters` objekt a volání `acquireTokenSilent()` :

```swift

    func acquireTokenSilently(_ account : MSALAccount!) {

        guard let applicationContext = self.applicationContext else { return }

        /**

         Acquire a token for an existing account silently

         - forScopes:           Permissions you want included in the access token received
         in the result in the completionBlock. Not all scopes are
         guaranteed to be included in the access token returned.
         - account:             An account object that we retrieved from the application object before that the
         authentication flow will be locked down to.
         - completionBlock:     The completion block that will be called when the authentication
         flow completes, or encounters an error.
         */

        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)

        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in

            if let error = error {

                let nsError = error as NSError

                // interactionRequired means we need to ask the user to sign-in. This usually happens
                // when the user's Refresh Token is expired or if the user has changed their password
                // among other possible reasons.

                if (nsError.domain == MSALErrorDomain) {

                    if (nsError.code == MSALError.interactionRequired.rawValue) {

                        DispatchQueue.main.async {
                            self.acquireTokenInteractively()
                        }
                        return
                    }
                }

                self.updateLogging(text: "Could not acquire token silently: \(error)")
                return
            }

            guard let result = result else {

                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }

            self.accessToken = result.accessToken
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```

### <a name="call-the-microsoft-graph-api"></a>Volání rozhraní API pro Microsoft Graph

Jakmile máte token, může ji aplikace v hlavičce HTTP použít k provedení autorizované žádosti na Microsoft Graph:

| klíč záhlaví    | hodnota                 |
| ------------- | --------------------- |
| Autorizace | Nosný \<access-token> |

Do třídy přidejte následující kód `ViewController` :

```swift
    func getContentWithToken() {

        // Specify the Graph API endpoint
        let graphURI = getGraphEndpoint()
        let url = URL(string: graphURI)
        var request = URLRequest(url: url!)

        // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")

        URLSession.shared.dataTask(with: request) { data, response, error in

            if let error = error {
                self.updateLogging(text: "Couldn't get graph result: \(error)")
                return
            }

            guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {

                self.updateLogging(text: "Couldn't deserialize result JSON")
                return
            }

            self.updateLogging(text: "Result from Graph: \(result))")

            }.resume()
    }
```

Další informace o rozhraní API pro Microsoft Graph najdete v tématu [Microsoft Graph API](https://graph.microsoft.com) .

### <a name="use-msal-for-sign-out"></a>Použití MSAL pro odhlášení

Dále přidejte podporu pro odhlášení.

> [!Important]
> Odhlášení pomocí MSAL odebere všechny známé informace o uživateli z aplikace a odebere aktivní relaci na jejich zařízení, pokud to povoluje konfigurace zařízení. Volitelně můžete uživatele z prohlížeče taky podepsat.

Chcete-li přidat možnost odhlášení, přidejte do třídy následující kód `ViewController` .

```swift
@objc func signOut(_ sender: AnyObject) {

        guard let applicationContext = self.applicationContext else { return }

        guard let account = self.currentAccount else { return }

        do {

            /**
             Removes all tokens from the cache for this application for the provided account

             - account:    The account to remove from the cache
             */

            let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParameters!)
            signoutParameters.signoutFromBrowser = false // set this to true if you also want to signout from browser or webview

            applicationContext.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

                if let error = error {
                    self.updateLogging(text: "Couldn't sign out account with error: \(error)")
                    return
                }

                self.updateLogging(text: "Sign out completed successfully")
                self.accessToken = ""
                self.updateCurrentAccount(account: nil)
            })

        }
    }
```

### <a name="enable-token-caching"></a>Povolit ukládání tokenů do mezipaměti

Ve výchozím nastavení MSAL ukládá do mezipaměti tokeny vaší aplikace v řetězci klíčů pro iOS nebo macOS.

Postup povolení ukládání tokenů do mezipaměti:

1. Ujistěte se, že je aplikace správně podepsaná.
1. Přejít na nastavení projektu Xcode **karta možnosti**>  >  **Povolit sdílení řetězce klíčů**
1. Vyberte **+** a zadejte jednu z následujících **skupin řetězce klíčů**:
    - iOS `com.microsoft.adalcache`
    - MacOS `com.microsoft.identity.universalstorage`

### <a name="add-helper-methods"></a>Přidat pomocné metody
Dokončete ukázku přidáním následujících pomocných metod do `ViewController` třídy.

### <a name="ios-ui"></a>uživatelské rozhraní iOS:

``` swift

    func updateLogging(text : String) {

        if Thread.isMainThread {
            self.loggingText.text = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.text = text
            }
        }
    }

    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }

    func updateAccountLabel() {

        guard let currentAccount = self.currentAccount else {
            self.usernameLabel.text = "Signed out"
            return
        }

        self.usernameLabel.text = currentAccount.username
    }

    func updateCurrentAccount(account: MSALAccount?) {
        self.currentAccount = account
        self.updateAccountLabel()
        self.updateSignOutButton(enabled: account != nil)
    }
```

### <a name="macos-ui"></a>uživatelské rozhraní macOS:

```swift
    func updateLogging(text : String) {

        if Thread.isMainThread {
            self.loggingText.string = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.string = text
            }
        }
    }

    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }

     func updateAccountLabel() {

         guard let currentAccount = self.currentAccount else {
            self.usernameLabel.stringValue = "Signed out"
            return
        }

        self.usernameLabel.stringValue = currentAccount.username ?? ""
        self.usernameLabel.sizeToFit()
     }

     func updateCurrentAccount(account: MSALAccount?) {
        self.currentAccount = account
        self.updateAccountLabel()
        self.updateSignOutButton(enabled: account != nil)
    }
```

### <a name="for-ios-only-get-additional-device-information"></a>Jenom pro iOS, Získejte další informace o zařízení.

Pomocí následujícího kódu si přečtete aktuální konfiguraci zařízení, včetně toho, jestli je zařízení nakonfigurované jako sdílené:

```swift
    @objc func getDeviceMode(_ sender: AnyObject) {

        if #available(iOS 13.0, *) {
            self.applicationContext?.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

                guard let deviceInfo = deviceInformation else {
                    self.updateLogging(text: "Device info not returned. Error: \(String(describing: error))")
                    return
                }

                let isSharedDevice = deviceInfo.deviceMode == .shared
                let modeString = isSharedDevice ? "shared" : "private"
                self.updateLogging(text: "Received device info. Device is in the \(modeString) mode.")
            })
        } else {
            self.updateLogging(text: "Running on older iOS. GetDeviceInformation API is unavailable.")
        }
    }
```

### <a name="multi-account-applications"></a>Aplikace s více účty

Tato aplikace je vytvořená pro scénář s jedním účtem. MSAL podporuje také scénáře s více účty, ale vyžaduje další práci z aplikací. Budete muset vytvořit uživatelské rozhraní, které uživatelům pomůže vybrat, který účet chtějí použít pro každou akci, která vyžaduje tokeny. Alternativně může vaše aplikace implementovat heuristiku pro výběr účtu, který se má použít, dotazování na všechny účty z MSAL. Podívejte se například na `accountsFromDeviceForParameters:completionBlock:` [rozhraní API](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplication.html#/c:objc(cs)MSALPublicClientApplication(im)accountsFromDeviceForParameters:completionBlock:)

## <a name="test-your-app"></a>Testování aplikace

Sestavte a nasaďte aplikaci do testovacího zařízení nebo simulátoru. Měli byste být schopni se přihlásit a získat tokeny pro účty Azure AD nebo osobní účty Microsoft.

Když se uživatel poprvé přihlásí do vaší aplikace, zobrazí se mu výzva Microsoftu k vyjádření souhlasu s požadovanými oprávněními. I když se většina uživatelů může zasílat, někteří klienti Azure AD mají zakázaný souhlas s uživatelem, což vyžaduje, aby správci měli souhlas jménem všech uživatelů. Pro podporu tohoto scénáře Zaregistrujte obory aplikace v Azure Portal.

Po přihlášení aplikace zobrazí data vrácená z Microsoft Graphho `/me` koncového bodu.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o vytváření mobilních aplikací, které volají chráněná webová rozhraní API v naší řadě scénářů s více částmi.

> [!div class="nextstepaction"]
> [Scénář: mobilní aplikace, která volá webová rozhraní API](scenario-mobile-overview.md)
