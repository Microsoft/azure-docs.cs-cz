---
title: Začínáme s Iosem – Microsoft identity platform | Azure
description: Jak aplikace pro iOS (Swift) může volat rozhraní API, která vyžaduje přístupové tokeny pomocí platforma identit Microsoft
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.topic: tutorial
ms.workload: identity
ms.date: 07/15/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 910069ab89cef18794e637b6bfbbc57fb732871c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67872088"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-app"></a>Přihlašování uživatelů a volání Microsoft Graphu v aplikaci pro iOS

V tomto kurzu se dozvíte, jak integrovat aplikace pro iOS s platformou identity Microsoft. Aplikace se přihlásit uživatele, získání přístupového tokenu pro volání rozhraní Microsoft Graph API a vytvořte žádost na rozhraní Microsoft Graph API.  

Po dokončení průvodce bude vaše aplikace akceptovat přihlášení osobní účty Microsoft (včetně outlook.com, live.com a další) a pracovní nebo školní účty z jakéhokoli společnosti nebo organizace, která používá Azure Active Directory.

## <a name="how-this-tutorial-works"></a>Jak funguje v tomto kurzu

![Ukazuje, jak ukázková aplikace vygenerované v tomto kurzu funguje](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

Aplikace v tomto kurzu se přihlásit uživatele a jejich jménem získat data.  Tato data budou mít přístup přes chráněný API (Microsoft Graph API v tomto případě), který vyžaduje ověření a je chráněn platforma identit Microsoft.

A konkrétně:

* Vaše aplikace se přihlásit uživatele buď prostřednictvím prohlížeče nebo si aplikaci Microsoft Authenticator.
* Koncový uživatel přijme oprávnění, která vyžaduje vaše aplikace.
* Vaše aplikace budou vydány lístky přístupového tokenu pro rozhraní Microsoft Graph API.
* Požadavek HTTP do webového rozhraní API zahrne přístupový token.
* Zpracování odpovědi Microsoft Graphu.

Tato ukázka používá Microsoft Authentication library (MSAL) pro implementaci ověřování. Knihovna MSAL bude automaticky obnovit tokeny, poskytovat jednotné přihlašování (SSO) mezi jinými aplikacemi na zařízení a spravovat účty.

## <a name="prerequisites"></a>Požadavky

- Verze XCode 10.x se vyžaduje k sestavení aplikace v této příručce. Si můžete stáhnout z XCode [iTunes webu](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "adresa URL pro XCode stahování").
- Microsoft Authentication Library ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Můžete použít Správce závislostí nebo ručně přidat knihovny. Následující pokyny ukazují, jak.

V tomto kurzu se vytvoří nový projekt. Pokud chcete stáhnout dokončený kurzu místo toho [stáhnout kód](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip).

## <a name="create-a-new-project"></a>Vytvoření nového projektu

1. Xcode otevřete a vyberte **vytvořte nový projekt Xcode**.
2. Vyberte **iOS** > **aplikace s jedním zobrazením** a vyberte **Další**.
3. Zadejte název produktu.
4. Nastavte **jazyk** k **Swift** a vyberte **Další**.
5. Vyberte složku pro vytvoření aplikace a klikněte na tlačítko **vytvořit**.

## <a name="register-your-application"></a>Registrace vaší aplikace

1. Přejděte na web [Azure Portal](https://aka.ms/MobileAppReg).
2. Otevřít [okně registrace aplikace](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) a klikněte na tlačítko **+ registrace nové**.
3. Zadejte **název** pro vaši aplikaci a pak, aniž byste museli nastavovat identifikátor URI přesměrování, klikněte na **zaregistrovat**.
4. V **spravovat** podokně, které se zobrazí, vyberte v části **ověřování**.
5. Klikněte na tlačítko **vyzkoušet si nové prostředí** v horní části obrazovky, otevřete nové prostředí registrace aplikací a pak klikněte na tlačítko **+ registrace nové** >  **+ přidat platformu**  >  **iOS**.
    - Zadejte ID sady prostředků projektu Pokud jste stáhli kód, je to `com.microsoft.identitysample.MSALiOS`. Pokud vytváříte vlastní projekt, vyberte v Xcode a otevřete svůj projekt **Obecné** kartu. Identifikátor sady prostředků se zobrazí v **Identity** oddílu.
6. Klikněte na tlačítko `Configure` a uložit **MSAL konfigurace** , který se zobrazí v **konfigurace pro iOS** stránce mohli zadat, při konfiguraci aplikace později.  Klikněte na **Done** (Hotovo).

## <a name="add-msal"></a>Přidat MSAL

Vyberte jednu z následujících způsobů, jak nainstalovat knihovna MSAL ve vaší aplikaci:

### <a name="cocoapods"></a>CocoaPods

1. Pokud používáte [CocoaPods](https://cocoapods.org/), nainstalujte `MSAL` tak, že nejprve vytvořit prázdný soubor volá `podfile` ve stejné složce jako váš projekt `.xcodeproj` souboru. Přidejte následující text do `podfile`:

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL', '~> 0.4.0'
   end
   ```

2. Nahraďte `<your-target-here>` s názvem projektu.
3. V okně terminálu přejděte do složky, která obsahuje `podfile` vytvořili a spustili `pod install` knihovna MSAL instalace.
4. Zavřete Xcode a otevřete `<your project name>.xcworkspace` k opětovnému načtení projektu v Xcode.

### <a name="carthage"></a>Carthage

Pokud používáte [Carthage](https://github.com/Carthage/Carthage), nainstalujte `MSAL` tak, že přidáte ho do vašeho `Cartfile`:

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

### <a name="manually"></a>Ručně

Můžete také použít Git dílčího modulu nebo podívejte se na nejnovější verzi pro použití jako framework ve vaší aplikaci.

## <a name="add-your-app-registration"></a>Přidat registraci vaší aplikace

V dalším kroku přidáme registrace vaší aplikace do vašeho kódu. 

Nejprve přidejte následující příkaz importu do horní části `ViewController.swift` a `AppDelegate.swift` soubory:

```swift
import MSAL
```

Přidejte následující kód k `ViewController.swift` před `viewDidLoad()`:

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
let kAuthority = "https://login.microsoftonline.com/common"
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
```

Změnit hodnotu přiřazenou `kClientID`jako ID aplikace. Tato hodnota je součástí MSAL konfigurační data, že jste si uložili během kroku na začátku tohoto kurzu a zaregistrovat aplikaci na webu Azure Portal.

## <a name="configure-url-schemes"></a>Schémata adres URL konfigurace

V tomto kroku registrovat `CFBundleURLSchemes` tak, aby uživatel je možné přesměrovat zpět do aplikace po přihlášení. Mimochodem `LSApplicationQueriesSchemes` rovněž umožňuje aplikaci používat Microsoft Authenticator.

V Xcode otevřete `Info.plist` jako zdrojový kód a přidejte následující uvnitř `<dict>` oddílu. Nahraďte `[BUNDLE_ID]` s hodnotou, který jste použili na webu Azure Portal, který, pokud jste stáhli kód, je `com.microsoft.identitysample.MSALiOS`. Pokud vytváříte vlastní projekt, vyberte v Xcode a otevřete svůj projekt **Obecné** kartu. Identifikátor sady prostředků se zobrazí v **Identity** oddílu.

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
    <string>msauth</string>
    <string>msauthv2</string>
</array>
```

## <a name="create-your-apps-ui"></a>Vytvořit uživatelské rozhraní vaší aplikace

Nyní vytvořit uživatelské rozhraní, která obsahuje tlačítko pro volání rozhraní Microsoft Graph API, jiné odhlášení, a text viděli nějaká výstupní přidáním následujícího kódu `ViewController`třídy:

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

Dále také uvnitř `ViewController` třídy, nahraďte `viewDidLoad()` metody:

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        initUI()
        do {
            try self.initMSAL()
        } catch let error {
            self.loggingText.text = "Unable to create Application Context \(error)"
        }
    }
```

## <a name="use-msal"></a>Použití MSAL

### <a name="initialize-msal"></a>Inicializovat MSAL

Přidejte následující `InitMSAL` metodu `ViewController` třídy:

```swift
    func initMSAL() throws {
        
        guard let authorityURL = URL(string: kAuthority) else {
            self.loggingText.text = "Unable to create authority URL"
            return
        }
        
        let authority = try MSALAADAuthority(url: authorityURL)
        
        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
    }
```

### <a name="handle-the-sign-in-callback"></a>Zpracování zpětného volání přihlášení

Otevřete soubor `AppDelegate.swift`. Po přihlášení zpracování zpětného volání, přidejte `MSALPublicClientApplication.handleMSALResponse` k `appDelegate` třídu takto:

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

#### <a name="acquire-tokens"></a>Získat tokeny

Teď můžeme implementovat logiku zpracování uživatelského rozhraní aplikace a získat tokeny interaktivně prostřednictvím MSAL.

Knihovna MSAL poskytuje dva primární metody pro získávání tokenů: `acquireTokenSilently()` a `acquireTokenInteractively()`: 

- `acquireTokenSilently()` pokusí se přihlásit uživatele a získat tokeny bez nutnosti zásahu uživatele, pokud je účet k dispozici.

- `acquireTokenInteractively()` vždy zobrazovat uživatelské rozhraní, při pokusu o přihlášení uživatele. Soubory cookie relací v prohlížeči nebo účet v aplikaci Microsoft authenticator může použít k poskytování interaktivní Jednotným přihlašováním.

Přidejte následující kód, který `ViewController` třídy:

```swift
    @objc func callGraphAPI(_ sender: UIButton) {
        
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

#### <a name="get-a-token-interactively"></a>Získat token interaktivně

Následující kód získá token prvním vytvořením `MSALInteractiveTokenParameters` objektu a volání `acquireToken`. Dále přidáte kód, který:

1. Vytvoří `MSALInteractiveTokenParameters` s obory.
2. Volání `acquireToken()` vytvořený parametrů.
3. Zpracovává chyby. Další podrobnosti najdete [zpracování chyb iOS průvodce](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Error-Handling).
4. Zpracovává případ úspěšné.

Přidejte následující kód, který `ViewController` třídy.

```swift
    func acquireTokenInteractively() {
   
        guard let applicationContext = self.applicationContext else { return }
     // #1    
        let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
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



#### <a name="get-a-token-silently"></a>Získání tokenu tiše

Získat aktualizovaný token tiše, přidejte následující kód, který `ViewController` třídy. Vytvoří `MSALSilentTokenParameters` objektu a volání `acquireTokenSilent()`:

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

### <a name="call-the-microsoft-graph-api"></a>Volání rozhraní Microsoft Graph API 

Jakmile máte token, vaše aplikace slouží v hlavičce protokolu HTTP k vytvoření žádosti o oprávnění Microsoft graphu:

| Klíč hlavičky    | value                 |
| ------------- | --------------------- |
| Authorization | Bearer \<access-token> |

Přidejte následující kód, který `ViewController` třídy:

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

Zobrazit [Microsoft Graph API](https://graph.microsoft.com) získat další informace o rozhraní Microsoft Graph API.

### <a name="use-msal-for-sign-out"></a>Použití MSAL pro odhlášení

Dále přidáte podporu pro odhlášení.

> [!Important]
> Odhlásit se MSAL odstraní všechny známé informace o uživateli z aplikace, ale uživatel bude mít stále aktivní relace na svém zařízení. Pokud se uživatel pokusí přihlásit znovu, může se zobrazit přihlašovací uživatelské rozhraní, ale možná muset znovu zadat své přihlašovací údaje, protože je stále aktivní relace zařízení.

Přidání odhlašování schopností, přidejte následující kód `ViewController` třídy. Tato metoda projde všechny účty a odebere je:

```swift 
    @objc func signOut(_ sender: UIButton) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache */
            
            try applicationContext.remove(account)
            self.loggingText.text = ""
            self.signOutButton.isEnabled = false
            
        } catch let error as NSError {
            
            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }
```

### <a name="enable-token-caching"></a>Povolit ukládání tokenu do mezipaměti

Ve výchozím nastavení MSAL ukládá do mezipaměti tokenů vaší aplikace v iOS řetězce klíčů. 

Pokud chcete povolit ukládání tokenu do mezipaměti:
1. Přejděte na nastavení projektu Xcode > **kartu Možnosti** > **povolení sdílení řetězce klíčů**
2. Klikněte na tlačítko **+** a zadejte `com.microsoft.adalcache` jako **skupiny klíčenky** položka.

### <a name="add-helper-methods"></a>Přidají metody helper

Přidejte následující metody pomocné rutiny, které `ViewController` třídy dokončete ukázku:

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

### <a name="multi-account-applications"></a>Více účet aplikace

Tato aplikace je vytvořená pro scénář jeden účet. Knihovna MSAL také podporuje scénáře více účtů, ale vyžaduje další úkony z aplikací. Je potřeba vytvořit uživatelské rozhraní umožňující uživateli na výběru účtu, který chce použít pro každou akci, která vyžaduje tokeny. Aplikace můžete alternativně implementovat heuristiku k výběru účtu, který se má použít prostřednictvím `getAccounts()` metody.

## <a name="test-your-app"></a>Testování aplikace

### <a name="run-locally"></a>Spuštění v místním prostředí

Sestavení a nasazení aplikace do testovacího zařízení nebo emulátoru. Byste měli být schopni se přihlásit a získat tokeny pro službu Azure AD nebo osobní účty Microsoft.

První uživatel přihlásí do vaší aplikace, uživatelé budou vyzváni k pomocí Microsoft identity souhlas oprávnění požadovaná.  Zatímco většina uživatelů jsou schopny vyjadřuje, zakázali několik tenantů Azure AD souhlas uživatele, který vyžaduje souhlas jménem všech uživatelů správce. Pro podporu tohoto scénáře, zaregistrujte obory vaší aplikace na webu Azure Portal.

Po přihlášení, zobrazí data vrácená z Microsoft Graphu `/me` koncového bodu.

## <a name="get-help"></a>Podpora

Navštivte [Nápověda a podpora](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) Pokud máte potíže s tímto kurzem, nebo s platformou identity Microsoft.
