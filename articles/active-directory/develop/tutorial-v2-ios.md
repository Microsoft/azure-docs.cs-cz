---
title: MSAL pro iOS & macOS tutorial - Microsoft identity platforma | Azure
description: Zjistěte, jak můžou aplikace pro iOS a macOS (Swift) volat rozhraní API, které vyžaduje přístupové tokeny pomocí platformy microsoftu pro identity
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/30/2019
ms.author: jmprieur
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 3c88ad2d147fb7e681c266bc5f0e7eb2cbf470ce
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546180"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-or-macos-app"></a>Přihlášení uživatelů a volání microsoft graphu z aplikace pro iOS nebo macOS

V tomto kurzu se dozvíte, jak integrovat aplikaci pro iOS nebo macOS s platformou identit Microsoftu. Aplikace se přihlásí uživatele, získá přístupový token pro volání rozhraní Microsoft Graph API a požádá o rozhraní Microsoft Graph API.  

Po dokončení příručky bude vaše aplikace přijímat přihlášení k osobním účtům Microsoft (včetně outlook.com, live.com a dalších) a pracovních nebo školních účtů od jakékoli společnosti nebo organizace, která používá Azure Active Directory.

>[!NOTE]
> Pokud s platformou microsoftu začínáte, doporučujeme začít s [přihlášením uživatelů a volat rozhraní Microsoft Graph API z aplikace pro iOS nebo macOS](quickstart-v2-ios.md).

## <a name="how-this-tutorial-works"></a>Jak tento výukový program funguje

![Ukazuje, jak ukázková aplikace vygenerovaná tímto kurzem funguje](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

Aplikace v tomto kurzu se přihlásí uživatele a získat data jejich jménem.  Tato data budou přístupná prostřednictvím chráněného rozhraní API (microsoft graph api v tomto případě), které vyžaduje autorizaci a je chráněno platformou identit microsoftu.

A konkrétně:

* Vaše aplikace se přihlásí uživatele buď prostřednictvím prohlížeče nebo Microsoft Authenticator.
* Koncový uživatel přijme oprávnění, která vaše aplikace požadovala.
* Vaší aplikaci bude vydán přístupový token pro rozhraní Microsoft Graph API.
* Přístupový token bude součástí požadavku HTTP do webového rozhraní API.
* Zpracujte odpověď aplikace Microsoft Graph.

Tato ukázka používá k implementaci ověřování knihovnu Microsoft Authentication (MSAL). MSAL automaticky obnoví tokeny, doručuje jednotné přihlašování (SSO) mezi ostatními aplikacemi v zařízení a spravuje účet(y).

Tento kurz se vztahuje na aplikace pro iOS i macOS. Všimněte si, že některé kroky se liší mezi těmito dvěma platformami. 

## <a name="prerequisites"></a>Požadavky

- XCode verze 11.x nebo vyšší je nutné vytvořit aplikaci v této příručce. XCode si můžete stáhnout z [webových stránek iTunes](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "Adresa URL pro stažení xkódu").
- Knihovna ověřování společnosti Microsoft ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Můžete použít správce závislostí nebo knihovnu přidat ručně. Níže uvedené pokyny vám ukážou, jak na to.

Tento kurz vytvoří nový projekt. Pokud chcete místo toho stáhnout dokončený kurz, stáhněte si kód:
- [ukázkový kód iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [ukázkový kód macOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

## <a name="create-a-new-project"></a>Vytvoření nového projektu

1. Otevřete Xcode a vyberte **Vytvořit nový projekt Xcode**.
2. Pro aplikace pro iOS vyberte**aplikaci pro jedno zobrazení** **iOS** > a vyberte **Další**.
3. Pro aplikace pro macOS vyberte **macOS** > **Cocoa App** a vyberte **Další**.
4. Zadejte název produktu.
5. Nastavte **jazyk** na **Rychlý** a vyberte **Další**.
6. Vyberte složku, ve které chcete aplikaci vytvořit, a klepněte na **vytvořit**.

## <a name="register-your-application"></a>Registrace vaší aplikace

1. Přejděte na web [Azure Portal](https://aka.ms/MobileAppReg).
2. Otevřete okno Registrace aplikací a klikněte na **+Nová registrace**.
3. Zadejte **název** aplikace a pak bez nastavení identifikátoru URI přesměrování.
4. Vyberte **účty v libovolném organizačním adresáři (libovolný adresář Azure AD – víceklientský) a osobní účty Microsoft (např. skype, xbox)** v části **Podporované typy účtů.**
5. Klikněte na **Registrovat.**
6. V části **Spravovat** v podokně, které se zobrazí, vyberte **možnost Ověřování**.

7. Kliknutím na **Vyzkoušet nové prostředí** v horní části obrazovky otevřete nové prostředí registrace aplikace a potom klikněte na **+Nová registrace** > **+ Přidat platformu** > **iOS/macOS**.
    - Zadejte ID balíčku projektu. Pokud jste kód stáhli, `com.microsoft.identitysample.MSALiOS`je to . Pokud vytváříte vlastní projekt, vyberte projekt v Xcode a otevřete kartu **Obecné.** Identifikátor balíčku se zobrazí v části **Identita.**
8. Klikněte `Configure` a uložte **konfiguraci služby MSAL,** která se zobrazí na **konfigurační stránce služby MSAL,** abyste ji mohli zadat při pozdější konfiguraci aplikace.  Klikněte na **Done** (Hotovo).

## <a name="add-msal"></a>Přidat MSAL

Vyberte si jeden z následujících způsobů instalace knihovny MSAL do aplikace:

### <a name="cocoapods"></a>Kakaové pody

1. Pokud používáte [CocoaPods](https://cocoapods.org/), `MSAL` nainstalujte nejprve `podfile` vytvořeníprázdného souboru volaném ve stejné složce jako `.xcodeproj` soubor projektu. Doudá `podfile`následující:

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. Nahraďte `<your-target-here>` název projektu.
3. V okně terminálu přejděte do `podfile` složky, `pod install` která obsahuje vytvořenou a spusťte instalaci knihovny MSAL.
4. Zavřete Xcode `<your project name>.xcworkspace` a otevřete znovu načíst projekt v Xcode.

### <a name="carthage"></a>Kartágo

Pokud používáte [Kastrámaso](https://github.com/Carthage/Carthage), `MSAL` nainstalujte `Cartfile`jej tak, že si ho přidáte do :

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

Z okna terminálu ve stejném adresáři jako aktualizovaný `Cartfile`spusťte následující příkaz, aby Kasthác aktualizoval závislosti v projektu.

iOS:

```bash
carthage update --platform iOS
```

Macos:

```bash
carthage update --platform macOS
```

### <a name="manually"></a>Ručně

Můžete také použít Git Submodule, nebo se podívat na nejnovější verzi použít jako rámec ve vaší aplikaci.

## <a name="add-your-app-registration"></a>Přidání registrace aplikace

Dále přidáme registraci vaší aplikace do vašeho kódu. 

Nejprve přidejte následující příkaz importu `ViewController.swift`do horní `AppDelegate.swift` `SceneDelegate.swift` části aplikace , stejně jako soubory:

```swift
import MSAL
```

Pak přidejte následující `ViewController.swift` kód `viewDidLoad()`před :

```swift
// Update the below to your client ID you received in the portal. The below is for running the demo only
let kClientID = "Your_Application_Id_Here"
let kGraphEndpoint = "https://graph.microsoft.com/" // the Microsoft Graph endpoint
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization’s Azure AD tenant to sign in
    
let kScopes: [String] = ["user.read"] // request permission to read the profile of the signed-in user
    
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParameters : MSALWebviewParameters?
var currentAccount: MSALAccount?
```

Jedinou hodnotou, kterou je třeba `kClientID`upravit výše, je hodnota přiřazená k [ID aplikace](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-id-client-id). Tato hodnota je součástí dat konfigurace MSAL, které jste uložili během kroku na začátku tohoto kurzu k registraci aplikace na webu Azure Portal.

## <a name="for-ios-only-configure-url-schemes"></a>Pouze pro iOS nakonfigurujte schémata adres URL

V tomto kroku se `CFBundleURLSchemes` zaregistrujete, aby uživatel mohl být přesměrován zpět do aplikace po přihlášení. Mimochodem, `LSApplicationQueriesSchemes` také umožňuje vaší aplikaci využívat Microsoft Authenticator.

V Xcode `Info.plist` otevřete jako soubor zdrojového kódu a `<dict>` přidejte následující vnitřek oddílu. Nahraďte `[BUNDLE_ID]` hodnotou, kterou jste použili na webu Azure `com.microsoft.identitysample.MSALiOS`Portal, která, pokud jste stáhli kód, je . Pokud vytváříte vlastní projekt, vyberte projekt v Xcode a otevřete kartu **Obecné.** Identifikátor balíčku se zobrazí v části **Identita.**

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

## <a name="for-macos-only-configure-app-sandbox"></a>Jenom pro macOS konfigurace izolovaného prostoru aplikace

1. Přejděte na kartu Nastavení projektu Xcode > **Možnosti–** > **izolovaného prostoru aplikace**
2. **Zaškrtávací políčko Odchozí připojení (Klient).** 

## <a name="create-your-apps-ui"></a>Vytvoření ui aplikace

Nyní vytvořte uj. `ViewController`

### <a name="ios-ui"></a>UI iOS

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

### <a name="macos-ui"></a>MacOS UI

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

Dále také uvnitř `ViewController` třídy `viewDidLoad()` nahraďte metodu:

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

## <a name="use-msal"></a>Použití funkce MSAL

### <a name="initialize-msal"></a>Inicializovat MSAL

Do třídy přidejte následující `initMSAL` metodu: `ViewController`

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

Přidejte následující `initMSAL` po `ViewController` metodě do třídy.

### <a name="ios-code"></a>kód iOS:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters(parentViewController: self)
    }
```

### <a name="macos-code"></a>kód macOS:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters()
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>Jenom pro iOS, zpracování zpětného volání přihlášení

Otevřete soubor `AppDelegate.swift`. Chcete-li zpracovat zpětné volání `MSALPublicClientApplication.handleMSALResponse` po `appDelegate` přihlášení, přidejte do třídy takto:

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**Pokud používáte Xcode 11**, měli byste místo `SceneDelegate.swift` toho umístit zpětné volání MSAL.
Pokud podporujete uISceneDelegate a UIApplicationDelegate pro kompatibilitu se starším iOS, bude nutné umístit zpětné volání MSAL do obou souborů.

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

#### <a name="acquire-tokens"></a>Získat žetony

Nyní můžeme implementovat logiku zpracování ui aplikace a získat tokeny interaktivně prostřednictvím MSAL.

MSAL zveřejňuje dvě primární metody pro `acquireTokenSilently()` `acquireTokenInteractively()`získání tokenů: a : 

- `acquireTokenSilently()`pokusí se přihlásit uživatele a získat tokeny bez jakékoli interakce uživatele, pokud je k dispozici účet. `acquireTokenSilently()`vyžaduje poskytnutí `MSALAccount` platného, který lze načíst pomocí jednoho z nastavení API výčtu účtu MSAL. Tato ukázka používá `applicationContext.getCurrentAccount(with: msalParameters, completionBlock: {})` k načtení aktuálního účtu. 

- `acquireTokenInteractively()`vždy zobrazuje uživatelské rozhraní při pokusu o přihlášení uživatele. Může používat soubory cookie relace v prohlížeči nebo účtu v ověřovateli microsoftu k poskytování interaktivního prostředí SSO.

Do `ViewController` třídy přidejte následující kód:

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

Níže uvedený kód získá token poprvé `MSALInteractiveTokenParameters` vytvořením `acquireToken`objektu a volání . Dále přidáte kód, který:

1. Vytvoří `MSALInteractiveTokenParameters` s obory.
2. Volání `acquireToken()` s vytvořenými parametry.
3. Zpracovává chyby. Podrobnější informace naleznete v [příručce MSAL pro iOS a macOS pro zpracování chyb](msal-handling-exceptions.md).
4. Zpracovává úspěšný případ.

Přidejte následující kód `ViewController` do třídy.

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


#### <a name="get-a-token-silently"></a>Získejte token tiše

Chcete-li získat aktualizovaný token tiše, přidejte následující kód do `ViewController` třídy. Vytvoří `MSALSilentTokenParameters` objekt a `acquireTokenSilent()`volá :

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

### <a name="call-the-microsoft-graph-api"></a>Volání rozhraní Microsoft Graph API 

Jakmile budete mít token, vaše aplikace ho může použít v hlavičce HTTP k vytvoření autorizovaného požadavku na Microsoft Graph:

| klíč záhlaví    | value                 |
| ------------- | --------------------- |
| Autorizace | > \<přístupových tokenů nosiče |

Do `ViewController` třídy přidejte následující kód:

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

Další informace o rozhraní Microsoft Graph API najdete v [tématu Microsoft Graph API.](https://graph.microsoft.com)

### <a name="use-msal-for-sign-out"></a>Použití msal pro odhlášení

Dále přidejte podporu pro odhlášení.

> [!Important]
> Odhlášení pomocí služby MSAL odebere z aplikace všechny známé informace o uživateli a také odebere aktivní relaci na svém zařízení, pokud je povolena konfigurací zařízení. Můžete také volitelně odhlásit uživatele z prohlížeče.

Chcete-li přidat možnost odhlášení, přidejte do třídy `ViewController` následující kód. 

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

Ve výchozím nastavení msal ukládá tokeny vaší aplikace v řetězci klíčů iOS nebo macOS. 

Povolení ukládání tokenů do mezipaměti:
1. Ujistěte se, že je vaše žádost řádně podepsána
2. Přejděte na kartu > Nastavení projektu Xcode > **možnosti****Povolit sdílení klíčů**
3. Klikněte **+** a zadejte následující položku **Skupiny klíčů:** `com.microsoft.adalcache` 3.a Pro iOS zadejte 3.b Pro macOS zadejte`com.microsoft.identity.universalstorage`

### <a name="add-helper-methods"></a>Přidání pomocné metody
Přidejte následující pomocné `ViewController` metody do třídy k dokončení vzorku.

### <a name="ios-ui"></a>UI iOS:

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

### <a name="macos-ui"></a>UI macOS:

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

### <a name="for-ios-only-get-additional-device-information"></a>Pouze pro iOS získáte další informace o zařízení

Ke čtení aktuální konfigurace zařízení, včetně toho, zda je zařízení nakonfigurováno jako sdílené, použijte následující kód:

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

### <a name="multi-account-applications"></a>Aplikace s více obchodními místy

Tato aplikace je vytvořena pro scénář jednoho účtu. MSAL také podporuje scénáře s více účtůmi, ale vyžaduje určitou další práci z aplikací. Budete muset vytvořit uživatelské tlačítko, které uživatelům pomůže vybrat, který účet chtějí použít pro každou akci, která vyžaduje tokeny. Alternativně vaše aplikace můžete implementovat heuristickou vybrat, který účet použít dotazem na všechny účty z MSAL. Například viz `accountsFromDeviceForParameters:completionBlock:` [ROZHRANÍ API](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplication.html#/c:objc(cs)MSALPublicClientApplication(im)accountsFromDeviceForParameters:completionBlock:)

## <a name="test-your-app"></a>Testování aplikace

### <a name="run-locally"></a>Spuštění v místním prostředí

Vytvořte a nasaďte aplikaci do testovacího zařízení nebo simulátoru. Měli byste být schopni se přihlásit a získat tokeny pro Azure AD nebo osobní účty Microsoft.

Při prvním přihlášení uživatele do vaší aplikace bude uživatel vyzván identitou Microsoftu, aby souhlasil s požadovanými oprávněními.  Zatímco většina uživatelů je schopná souhlasit, někteří klienti Azure AD zakázali uživatelský souhlas, což vyžaduje, aby správci souhlasili jménem všech uživatelů. Chcete-li tento scénář podpořit, zaregistrujte obory aplikace na webu Azure Portal.

Po přihlášení se v aplikaci zobrazí data vrácená z koncového bodu Microsoft Graphu. `/me`

## <a name="get-help"></a>Podpora

Pokud máte potíže s tímto kurzem nebo s platformou identit microsoftu, navštivte [nápovědu a podporu.](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)

Pomozte nám vylepšit platformu identit Microsoftu. Řekněte nám, co si myslíte, že dokončení krátké hod-průzkum u dvou otázek.

> [!div class="nextstepaction"]
> [Průzkum platformy identity Microsoftu](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
