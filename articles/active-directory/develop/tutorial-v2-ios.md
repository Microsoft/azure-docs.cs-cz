---
title: Začínáme s Iosem – Microsoft identity platform | Azure
description: Jak aplikace pro iOS (Swift) může volat rozhraní API, která vyžaduje přístupové tokeny pomocí platforma identit Microsoft
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 780da9359aaf645abc9b685fa9d90bbea9199759
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962160"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-app"></a>Přihlašování uživatelů a volání Microsoft Graphu v aplikaci pro iOS

V tomto kurzu se dozvíte, jak vytvářet aplikace pro iOS a integrace do Microsoft identity platform. Konkrétně tato aplikace se přihlásit uživatele získání přístupového tokenu pro volání rozhraní Microsoft Graph API a vytvořte žádost na základní rozhraní Microsoft Graph API.  

Po dokončení průvodce bude vaše aplikace akceptovat přihlášení osobní účty Microsoft (včetně outlook.com, live.com a další) a pracovní nebo školní účty z jakéhokoli společnosti nebo organizace, která používá Azure Active Directory.

## <a name="how-this-guide-works"></a>Jak funguje tento průvodce

![Ukazuje, jak ukázková aplikace vygenerované v tomto kurzu funguje](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

Aplikace v této ukázce se přihlásit uživatele a jejich jménem získat data.  Tato data budou mít přístup přes chráněný API (Microsoft Graph API v tomto případě), který vyžaduje ověření a také chráněn platforma identit Microsoft.

A konkrétně:

* Vaše aplikace se přihlásit uživatele buď prostřednictvím prohlížeče nebo si aplikaci Microsoft Authenticator.
* Koncový uživatel přijme oprávnění, která vyžaduje vaše aplikace. 
* Vaše aplikace budou vydány lístky přístupového tokenu pro rozhraní Microsoft Graph API.
* Požadavek HTTP do webového rozhraní API zahrne přístupový token.
* Zpracování odpovědi Microsoft Graphu.

Tato ukázka používá Microsoft Authentication library (MSAL) k implementaci ověřeného Knihovna MSAL bude automaticky obnovit tokeny, poskytovat jednotné přihlašování mezi jinými aplikacemi na zařízení a spravovat účty.

## <a name="prerequisites"></a>Požadavky

- Verze XCode 10.x se vyžaduje pro ukázku, která se vytvoří v této příručce. Si můžete stáhnout z XCode [iTunes webu](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "adresa URL pro XCode stahování").
- Microsoft Authentication Library ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Můžete použít Správce závislostí nebo přidat ručně. Oddíl níže s [informace](#add-msal). 

## <a name="set-up-your-project"></a>Nastavení projektu

V tomto kurzu se vytvoří nový projekt. Pokud chcete stáhnout dokončený kurzu místo toho [stáhnout kód](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip).

### <a name="create-a-new-project"></a>Vytvořit nový projekt

1. Xcode otevřete a vyberte **vytvořte nový projekt Xcode**.
2. Vyberte **iOS > jediné zobrazení aplikace** a vyberte **Další**.
3. Zadejte název produktu a vybrat **Další**.
4. Vyberte složku pro vytvoření aplikace a klikněte na tlačítko *vytvořit*.

## <a name="register-your-application"></a>Zaregistrujte svoji aplikaci. 

Můžete zaregistrovat aplikaci v některém ze dvou způsobů, jak je popsáno v následujících dvou částech.

### <a name="register-your-app"></a>Zaregistrovat aplikaci

1. Přejděte [webu Azure portal](https://aka.ms/MobileAppReg) > vyberte `New registration`. 
2. Zadejte **název** pro vaši aplikaci > `Register`. **Není nastavena identifikátor URI pro přesměrování v této fázi**. 
3. V `Manage` části, přejděte na `Authentication` > `Add a platform` > `iOS`
    - Zadejte ID sady prostředků projektu Pokud jste stáhli kód, je to `com.microsoft.identitysample.MSALiOS`.
4. Spuštění `Configure` a uložit `MSAL Configuration` na později. 

## <a name="add-msal"></a>Přidat MSAL

### <a name="get-msal"></a>Získat MSAL

#### <a name="cocoapods"></a>CocoaPods

Můžete použít [CocoaPods](https://cocoapods.org/) instalace `MSAL` tak, že přidáte ho do vašeho `Podfile` podle cílové:

```
use_frameworks!

target '<your-target-here>' do
   pod 'MSAL', '~> 0.4.0'
end
```

#### <a name="carthage"></a>Carthage

Můžete použít [Carthage](https://github.com/Carthage/Carthage) instalace `MSAL` tak, že přidáte ho do vašeho `Cartfile`: 

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

#### <a name="manually"></a>Ručně

Můžete také použít Git dílčího modulu nebo podívejte se na nejnovější verzi a použít jako framework ve vaší aplikaci.

### <a name="add-your-app-registration"></a>Přidat registraci vaší aplikace

Registrace aplikace v dalším kroku přidejte do kódu. Přidat vaše ***klienta nebo ID aplikace*** k `ViewController.swift`:

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
let kAuthority = "https://login.microsoftonline.com/common"
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
```

### <a name="configure-url-schemes"></a>Schémata adres URL konfigurace

Zaregistrujte `CFBundleURLSchemes` umožňující zpětné volání, takže uživatel může přesměrován do aplikace po přihlášení.

`LSApplicationQueriesSchemes` Umožňuje pomocí aplikace Microsoft Authenticator používat, pokud je k dispozici. 

Chcete-li to provést, otevřete `Info.plist` jako zdroj kódu a přidejte následující, přičemž nahraďte ***BUNDLE_ID*** s vámi provedené konfiguraci na webu Azure Portal.

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

### <a name="import-msal"></a>Import MSAL

Rámec importu MSAL `ViewController.swift` a `AppDelegate.swift` soubory:

```swift
import MSAL
```

## <a name="create-your-apps-ui"></a>Vytvořit uživatelské rozhraní vaší aplikace

Pro účely tohoto kurzu je potřeba vytvořit:

- Tlačítko volání rozhraní Graph API
- Odhlásit se tlačítko
- Textview protokolování

V `ViewController.swift`, definovat vlastnosti a `initUI()` následujícím způsobem:

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

V dalším kroku přidejte do `viewDidLoad()` z ViewController.swift:

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

Nejprve je potřeba vytvořit `MSALPublicClientApplication` s instancí `MSALPublicClientConfiguration` pro vaši aplikaci:

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

### <a name="handle-the-callback"></a>Zpracování zpětného volání

Chcete-li po přihlášení zpracování zpětného volání, přidejte `MSALPublicClientApplication.handleMSALResponse` v `appDelegate`:

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

#### <a name="acquire-tokens"></a>Získat tokeny

Teď můžeme implementovat uživatelského rozhraní aplikace logiky zpracování a jak získat tokeny interaktivně prostřednictvím MSAL. 

Knihovna MSAL poskytuje dva primární metody pro získávání tokenů: `acquireTokenSilently` a `acquireTokenInteractively`.  

`acquireTokenSilently()` pokusí se přihlásit uživatele a získat tokeny bez nutnosti zásahu uživatele, pokud se účet nachází.

`acquireTokenInteractively` vždy zobrazit uživatelské rozhraní, při pokusu o přihlášení uživatele a získat tokeny; může však použít soubory cookie relací v prohlížeči nebo účet v aplikaci Microsoft authenticator poskytnout interaktivní Jednotným přihlašováním. 

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

K získání tokenu poprvé, budete muset vytvořit `MSALInteractiveTokenParameters` a volat `acquireToken`.

1. Vytvoření `MSALInteractiveTokenParameters` s obory.
2. Volání `acquireToken` s parametry, které vytvořili.
3. Odpovídajícím způsobem chybu zpracujte. Další podrobnosti najdete [zpracování chyb iOS průvodce](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Error-Handling).
4. Zpracování případu, úspěšné. 

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

Bezobslužná získat aktualizovaný token, je potřeba vytvořit `MSALSilentTokenParameters` a volat `acquireTokenSilent`:

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

Jakmile budete mít token prostřednictvím `self.accessToken`, vaše aplikace může využívat tuto hodnotu v hlavičce protokolu HTTP, aby požadavek na oprávnění pro Microsoft Graph:

| Klíč hlavičky    | value                 |
| ------------- | --------------------- |
| Autorizace | Nosiče < přístupový token > |

Přidejte následující text do `ViewController.swift`:

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

Další informace o [Microsoft Graph API](https://graph.microsoft.com)

### <a name="use-msal-for-sign-out"></a>Použití MSAL pro odhlášení

Dále, až budete přidáváme podporu pro odhlašování do vaší aplikace. 

Je důležité si uvědomit, odhlašování pomocí MSAL odebere všechny známé informace o uživateli z této aplikace, ale uživatel bude mít stále aktivní relace na svém zařízení. Pokud se uživatel pokusí přihlásit znovu, může se zobrazit interakce, ale možná muset znovu zadat své přihlašovací údaje z důvodu se aktivní relace zařízení. 

Chcete-li přidat odhlašování, zkopírujte následující metodu do vaší `ViewController.swift`:

```swift 
    @objc func signOut(_ sender: UIButton) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
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

Pokud chcete povolit ukládání tokenu do mezipaměti, přejděte na nastavení projektu Xcode > `Capabilities tab`  >  `Enable Keychain Sharing` > klikněte na tlačítko `Plus` > Enter **com.microsoft.adalcache**.

### <a name="add-helper-methods"></a>Přidají metody helper

Přidejte tyto pomocné metody pro dokončení vzorku:

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

Tato aplikace je vytvořená pro scénář jeden účet. Knihovna MSAL podporuje i scénáře více účtů, ale vyžaduje další úkony z aplikací. Je potřeba vytvořit uživatelské rozhraní umožňující uživateli na výběru účtu, který chce použít pro každou akci, která vyžaduje tokeny. Aplikace můžete alternativně implementovat heuristiku k výběru účtu, který se má použít prostřednictvím `getAllAccounts(...)` metody.

## <a name="test-your-app"></a>Testování aplikace

### <a name="run-locally"></a>Spuštění v místním prostředí

Pokud jste postupovali podle výše uvedený kód, zkuste k vytvoření a nasazení aplikace do testovacího zařízení nebo emulátoru. Byste měli být schopni se přihlásit a získat tokeny pro službu Azure AD nebo osobní účty Microsoft! Po přihlášení uživatele, tato aplikace se zobrazí data vrácená z Microsoft Graphu `/me` koncového bodu. 

Pokud máte nějaké problémy, neváhejte otevřete problém v tomto dokumentu nebo knihovna MSAL a dejte nám vědět. 

### <a name="consent-to-your-app"></a>Souhlas s vaší aplikace

Prvním libovolný uživatel přihlásí do vaší aplikace, uživatelé budou vyzváni k pomocí Microsoft identity souhlas oprávnění požadovaná.  Zatímco většina uživatelů jsou schopny vyjadřuje, zakázali několik tenantů Azure AD souhlasu uživatele - vyžadující souhlas jménem všech uživatelů správce.  Pro podporu tohoto scénáře, je nutné zaregistrovat obory vaší aplikace na webu Azure Portal.

## <a name="help-and-support"></a>Nápověda a podpora

Měli jakékoli potíže, v tomto kurzu nebo s platformou identity Microsoft? Zobrazit [Nápověda a podpora](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)

