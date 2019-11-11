---
title: Začínáme s iOS a macOS – Microsoft Identity Platform | Azure
description: Jak aplikace iOS a macOS (SWIFT) můžou volat rozhraní API, které vyžaduje přístupové tokeny pomocí platformy Microsoft Identity Platform.
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/30/2019
ms.author: jmprieur
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cfb61f417597abe52910b012ce3fb79ba48ce97
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2019
ms.locfileid: "73902838"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-or-macos-app"></a>Přihlaste se uživatelům a zavolejte Microsoft Graph z aplikace pro iOS nebo macOS.

V tomto kurzu se dozvíte, jak integrovat aplikaci pro iOS nebo macOS s platformou Microsoft identity. Aplikace se přihlásí k uživateli, získá přístupový token pro volání rozhraní Microsoft Graph API a vytvoří požadavek na rozhraní Microsoft Graph API.  

Po dokončení průvodce bude aplikace přijímat přihlašovacíky osobních účtů Microsoft (včetně outlook.com, live.com a dalších) a pracovních nebo školních účtů z jakékoli společnosti nebo organizace, která používá Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Jak tento kurz funguje

![Ukazuje, jak ukázková aplikace vygenerovaná tímto kurzem funguje](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

Aplikace v tomto kurzu se bude přihlašovat uživatelům a získat data jménem.  Tato data budou k dispozici prostřednictvím chráněného rozhraní API (Microsoft Graph API v tomto případě), které vyžaduje autorizaci a jsou chráněny platformou Microsoft identity.

A to konkrétně:

* Vaše aplikace se přihlásí k uživateli přes prohlížeč nebo Microsoft Authenticator.
* Koncový uživatel bude akceptovat oprávnění, která vaše aplikace požadovala.
* Vaše aplikace se vydá přístupový token pro rozhraní Microsoft Graph API.
* Přístupový token bude součástí požadavku HTTP webovému rozhraní API.
* Zpracujte odpověď Microsoft Graph.

Tato ukázka implementuje ověřování pomocí knihovny Microsoft Authentication Library (MSAL). MSAL bude automaticky obnovovat tokeny, poskytovat jednotné přihlašování (SSO) mezi ostatními aplikacemi na zařízení a spravovat účty.

Tento kurz se týká aplikací pro iOS i macOS. Všimněte si, že některé kroky se mezi těmito dvěma platformami liší. 

## <a name="prerequisites"></a>Požadavky

- K sestavení aplikace v této příručce se vyžaduje XCode verze 10. x nebo vyšší. XCode si můžete stáhnout z [webu iTunes](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "Adresa URL pro stažení XCode").
- Knihovna Microsoft Authentication Library ([MSAL. Framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Můžete použít Správce závislostí nebo přidat knihovnu ručně. Níže uvedené pokyny vám ukážou, jak.

V tomto kurzu se vytvoří nový projekt. Pokud chcete stáhnout dokončený kurz místo toho, Stáhněte si kód:
- [Ukázkový kód iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [vzorový kód macOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

## <a name="create-a-new-project"></a>Vytvoření nového projektu

1. Otevřete Xcode a vyberte **vytvořit nový projekt Xcode**.
2. V případě aplikací pro iOS vyberte aplikace pro **ios** > s **jedním zobrazením** a vyberte **Další**.
3. V případě aplikací macOS vyberte **macOS** > **aplikace pro kakao** a vyberte **Další**.
4. Zadejte název produktu.
5. Nastavte **jazyk** na **SWIFT** a vyberte **Další**.
6. Vyberte složku pro vytvoření aplikace a klikněte na **vytvořit**.

## <a name="register-your-application"></a>Registrace vaší aplikace

1. Přejděte na web [Azure Portal](https://aka.ms/MobileAppReg).
2. Otevřete okno [Registrace aplikací](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) a klikněte na **+ Nová registrace**.
3. Zadejte **název** vaší aplikace a pak bez nastavení identifikátoru URI přesměrování klikněte na **zaregistrovat**.
4. V části **Spravovat** v podokně, které se zobrazí, vyberte **ověřování**.

5. Kliknutím na **vyzkoušet nové prostředí** v horní části obrazovky otevřete nové prostředí pro registraci aplikací a potom klikněte na **+ Nová registrace** >  **+ Přidat platformu** > **iOS**.
    - Zadejte ID sady projektu. Pokud jste kód stáhli, je to `com.microsoft.identitysample.MSALiOS`. Pokud vytváříte vlastní projekt, vyberte projekt v Xcode a otevřete kartu **Obecné** . Identifikátor sady prostředků se zobrazí v oddílu **Identita** .
    - Všimněte si, že pro macOS byste měli také používat prostředí iOS. 
6. Klikněte na `Configure` a uložte **konfiguraci MSAL** , která se zobrazí na stránce **Konfigurace iOS** , abyste ji mohli zadat při pozdější konfiguraci aplikace.  Klikněte na **Done** (Hotovo).

## <a name="add-msal"></a>Přidat MSAL

Vyberte jeden z následujících způsobů, jak nainstalovat knihovnu MSAL do vaší aplikace:

### <a name="cocoapods"></a>CocoaPods

1. Pokud používáte [CocoaPods](https://cocoapods.org/), nainstalujte `MSAL` tak, že nejprve vytvoříte prázdný soubor s názvem `podfile` ve stejné složce jako soubor `.xcodeproj` projektu. Přidejte následující `podfile`:

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. Nahraďte `<your-target-here>` názvem projektu.
3. V okně terminálu přejděte do složky obsahující `podfile`, který jste vytvořili a spustili `pod install` a nainstalujte knihovnu MSAL.
4. Zavřete Xcode a otevřete `<your project name>.xcworkspace` pro opětovné načtení projektu v Xcode.

### <a name="carthage"></a>Carthage

Pokud používáte [Carthage](https://github.com/Carthage/Carthage), nainstalujte `MSAL` přidáním do `Cartfile`:

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

V okně terminálu ve stejném adresáři jako aktualizované `Cartfile`spusťte následující příkaz, který Carthage aktualizuje závislosti ve vašem projektu.

iOS

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

Nejprve přidejte následující příkaz Import na začátek `ViewController.swift` a `AppDelegate.swift` souborů:

```swift
import MSAL
```

Poté do `ViewController.swift` před `viewDidLoad()`přidejte následující kód:

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/" // the Microsoft Graph endpoint
let kScopes: [String] = ["https://graph.microsoft.com/user.read"] // request permission to read the profile of the signed-in user
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization’s Azure AD tenant to sign in
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParamaters : MSALWebviewParameters?
```

Jedinou hodnotou, kterou je třeba upravit výše, je hodnota přiřazená `kClientID`jako [ID aplikace](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-id-client-id). Tato hodnota je součástí dat konfigurace MSAL, která jste uložili během kroku na začátku tohoto kurzu, abyste aplikaci zaregistrovali v Azure Portal.

## <a name="for-ios-only-configure-url-schemes"></a>Jenom pro iOS, konfigurace schémat URL

V tomto kroku zaregistrujete `CFBundleURLSchemes`, aby se uživatel mohl po přihlášení znovu přesměrovat zpátky do aplikace. `LSApplicationQueriesSchemes` také umožňuje, aby aplikace používala Microsoft Authenticator.

V Xcode otevřete `Info.plist` jako soubor zdrojového kódu a do `<dict>` oddílu přidejte následující. Nahraďte `[BUNDLE_ID]` hodnotou, kterou jste použili v Azure Portal který, pokud jste kód stáhli `com.microsoft.identitysample.MSALiOS`. Pokud vytváříte vlastní projekt, vyberte projekt v Xcode a otevřete kartu **Obecné** . Identifikátor sady prostředků se zobrazí v oddílu **Identita** .

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

1. Přejít na nastavení projektu Xcode > **karta možnosti** > **izolovaný prostor aplikace**
2. Zaškrtněte políčko **odchozí připojení (klient)** . 

## <a name="create-your-apps-ui"></a>Vytvoření uživatelského rozhraní vaší aplikace

Nyní vytvořte uživatelské rozhraní, které obsahuje tlačítko pro volání rozhraní Microsoft Graph API, jiné pro odhlášení a textové zobrazení, aby se zobrazil nějaký výstup přidáním následujícího kódu do `ViewController`třídy:

### <a name="ios-ui"></a>uživatelské rozhraní iOS

```swift
var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = UIButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
        callGraphButton.setTitleColor(.blue, for: .normal)
        callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
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
        signOutButton.isEnabled = false
        
        // Add logging textfield
        loggingText = UITextView()
        loggingText.isUserInteractionEnabled = false
        loggingText.translatesAutoresizingMaskIntoConstraints = false
        
        self.view.addSubview(loggingText)
        
        loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
        loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
        loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: 10.0).isActive = true
        loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
    }
```

### <a name="macos-ui"></a>uživatelské rozhraní macOS

```swift

var callGraphButton: NSButton!
var loggingText: NSTextView!
var signOutButton: NSButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = NSButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.title = "Call Microsoft Graph API"
        callGraphButton.target = self
        callGraphButton.action = #selector(callGraphAPI(_:))
        callGraphButton.bezelStyle = .rounded
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 30.0).isActive = true
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
```

Dále v rámci třídy `ViewController` nahraďte `viewDidLoad()` metodu následujícím způsobem:

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        initUI()
        do {
            try self.initMSAL()
        } catch let error {
            self.updateLogging(text: "Unable to create Application Context \(error)")
        }
    }
```

## <a name="use-msal"></a>Použití MSAL

### <a name="initialize-msal"></a>Inicializovat MSAL

Do `ViewController` třídy přidejte následující metodu `initMSAL`:

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

Přidejte následující po `initMSAL` metodě do `ViewController` třídy.

### <a name="ios-code"></a>kód pro iOS:

```swift
func initWebViewParams() {
        self.webViewParamaters = MSALWebviewParameters(parentViewController: self)
    }
```

### <a name="macos-code"></a>macOS kód:

```swift
func initWebViewParams() {
        self.webViewParamaters = MSALWebviewParameters()
        self.webViewParamaters?.webviewType = .wkWebView
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>Pouze pro iOS, zpracování zpětného volání přihlášení

Otevřete soubor `AppDelegate.swift`. Pro zpracování zpětného volání po přihlášení přidejte `MSALPublicClientApplication.handleMSALResponse` do `appDelegate` třídy podobný tomuto:

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**Pokud používáte Xcode 11**, měli byste místo toho umístit MSAL zpětné volání do `SceneDelegate.swift`.
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

MSAL zpřístupňuje dvě primární metody získání tokenů: `acquireTokenSilently()` a `acquireTokenInteractively()`: 

- `acquireTokenSilently()` se pokusí přihlásit uživatele a získat tokeny bez zásahu uživatele, pokud je přítomen účet.

- `acquireTokenInteractively()` vždy zobrazuje uživatelské rozhraní při pokusu o přihlášení uživatele. K zajištění interaktivního prostředí jednotného přihlašování může používat soubory cookie relace v prohlížeči nebo účtu v rámci služby Microsoft Authenticator.

Do `ViewController` třídy přidejte následující kód:

```swift
    @objc func callGraphAPI(_ sender: AnyObject) {
        
        guard let currentAccount = self.currentAccount() else {
            // We check to see if we have a current logged in account.
            // If we don't, then we need to sign someone in.
            acquireTokenInteractively()
            return
        }
        
        acquireTokenSilently(currentAccount)
    }

    func currentAccount() -> MSALAccount? {
        
        guard let applicationContext = self.applicationContext else { return nil }
        
        // We retrieve our current account by getting the first account from cache
        // In multi-account applications, account should be retrieved by home account identifier or username instead
        
        do {
            let cachedAccounts = try applicationContext.allAccounts()
            if !cachedAccounts.isEmpty {
                return cachedAccounts.first
            }
        } catch let error as NSError {
            self.updateLogging(text: "Didn't find any accounts in cache: \(error)")
        }
        
        return nil
    }
```

#### <a name="get-a-token-interactively"></a>Interaktivní získání tokenu

Následující kód získá token poprvé, a to vytvořením objektu `MSALInteractiveTokenParameters` a voláním `acquireToken`. Dále přidáte kód, který:

1. Vytvoří `MSALInteractiveTokenParameters` s obory.
2. Volá `acquireToken()` s vytvořenými parametry.
3. Zpracovává chyby. Další podrobnosti najdete v tématu [Průvodce zpracováním chyb MSAL pro iOS a MacOS](msal-handling-exceptions.md).
4. Zpracuje úspěšný případ.

Do třídy `ViewController` přidejte následující kód.

```swift
func acquireTokenInteractively() {
        
    guard let applicationContext = self.applicationContext else { return }
    guard let webViewParameters = self.webViewParamaters else { return }
        
    // #1
    let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: webViewParameters)
        
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
        self.updateSignOutButton(enabled: true)
        self.getContentWithToken()
    }
}    
```


#### <a name="get-a-token-silently"></a>Bezobslužné získání tokenu

Chcete-li získat aktualizovaný token tiše, přidejte následující kód do třídy `ViewController`. Vytvoří objekt `MSALSilentTokenParameters` a volá `acquireTokenSilent()`:

```swift
    
    func acquireTokenSilently(_ account : MSALAccount!) {
        guard let applicationContext = self.applicationContext else { return }
        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
        
        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in    
            if let error = error {
                let nsError = error as NSError
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
| Autorizace | Přístupový \<přístup-token > |

Do `ViewController` třídy přidejte následující kód:

```swift
    func getContentWithToken() {        
        // Specify the Graph API endpoint
        let url = URL(string: kGraphURI)
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
> Odhlášení pomocí MSAL odebere všechny známé informace o uživateli z aplikace, ale uživatel bude na svém zařízení mít stále aktivní relaci. Pokud se uživatel pokusí přihlásit znovu, může se jim zobrazit přihlašovací uživatelské rozhraní, ale nemusí znovu zadávat svoje přihlašovací údaje, protože relace zařízení je stále aktivní.

Chcete-li přidat možnost odhlášení, přidejte následující kód do třídy `ViewController`. Tato metoda cyklicky projde všechny účty a odebere je:

```swift 
@objc func signOut(_ sender: AnyObject) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
            try applicationContext.remove(account)
            self.updateLogging(text: "")
            self.updateSignOutButton(enabled: false)
            self.accessToken = ""
            
        } catch let error as NSError {
            
            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }
```

### <a name="enable-token-caching"></a>Povolit ukládání tokenů do mezipaměti

Ve výchozím nastavení MSAL ukládá do mezipaměti tokeny vaší aplikace v řetězci klíčů pro iOS nebo macOS. 

Postup povolení ukládání tokenů do mezipaměti:
1. Ujistěte se, že je aplikace správně podepsaná.
2. Přejít na nastavení projektu Xcode > **kartu možnosti** > **Povolení sdílení řetězce klíčů**
3. Klikněte na **+** a zadejte následující položku **skupin klíčů** : 3. a pro iOS zadejte `com.microsoft.adalcache` 3. b pro MacOS ENTER `com.microsoft.identity.universalstorage`

### <a name="add-helper-methods"></a>Přidat pomocné metody
Dokončete ukázku přidáním následujících pomocných metod do třídy `ViewController`.

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
```

### <a name="macos-ui"></a>uživatelské rozhraní macOS:

```swift
func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.string = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.string = text
            }
        }
    }
```



### <a name="multi-account-applications"></a>Aplikace s více účty

Tato aplikace je vytvořená pro scénář s jedním účtem. MSAL podporuje také scénáře s více účty, ale vyžaduje další práci z aplikací. Budete muset vytvořit uživatelské rozhraní, které uživatelům pomůže vybrat, který účet chtějí použít pro každou akci, která vyžaduje tokeny. Alternativně může vaše aplikace implementovat heuristiku pro výběr účtu, který se má použít prostřednictvím metody `getAccounts()`.

## <a name="test-your-app"></a>Testování aplikace

### <a name="run-locally"></a>Spuštění v místním prostředí

Sestavte a nasaďte aplikaci do testovacího zařízení nebo simulátoru. Měli byste být schopni se přihlásit a získat tokeny pro účty Azure AD nebo osobní účty Microsoft.

Když se uživatel poprvé přihlásí do vaší aplikace, zobrazí se mu výzva Microsoftu k vyjádření souhlasu s požadovanými oprávněními.  I když se většina uživatelů může zasílat, někteří klienti Azure AD mají zakázaný souhlas s uživatelem, což vyžaduje, aby správci měli souhlas jménem všech uživatelů. Pro podporu tohoto scénáře Zaregistrujte obory aplikace v Azure Portal.

Po přihlášení aplikace zobrazí data vrácená z Microsoft Graph koncového bodu `/me`.

## <a name="get-help"></a>Podpora

Pokud máte potíže s tímto kurzem nebo s platformou Microsoft identity, přejděte na [pomoc a podpora](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) .

Pomůžeme nám vylepšit platformu Microsoft identity. Řekněte nám, co si myslíte, díky krátkému průzkumu dvou dotazů.

> [!div class="nextstepaction"]
> [Microsoft Identity Platform Survey](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
