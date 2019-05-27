---
title: Rychlý start Microsoft identity platform iOS | Azure
description: Zjistěte, jak přihlásit uživatele a dotazů Microsoft Graphu v aplikaci iOS.
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2019
ms.author: brandwe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3802d8f92913e416cc6a80f899179fde80cec30
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962587"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-app"></a>Rychlý start: Přihlašování uživatelů a volání rozhraní Microsoft Graph API z aplikace pro iOS

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Tento rychlý start obsahuje vzorový kód, který předvádí, jak může nativní aplikace pro iOS přihlašovat uživatele pomocí osobního nebo pracovního a školního účtu, získat přístupový token a volat rozhraní Microsoft Graph API.

![Ukazuje, jak ukázková aplikace vygenerované v tomto rychlém startu funguje](media/quickstart-v2-ios/ios-intro.svg)

> [!NOTE]
> **Požadavky**
> * XCode 10 +
> * iOS 10+ 

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrace a stažení aplikace pro rychlý start
> Aplikaci pro rychlý start můžete spustit dvěma způsoby:
> * [Express] [Možnost 1: Registrace a automaticky konfigurovat svoji aplikaci a pak si stáhnout ukázku kódu](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Ruční] [Možnost 2: Registraci a ručně konfiguraci vaší aplikace a ukázku kódu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1: Registrace a automaticky konfigurovat svoji aplikaci a pak si stáhnout ukázku kódu
> #### <a name="step-1-register-your-application"></a>Krok 1: Zaregistrujte svoji aplikaci.
> Registrace vaší aplikace
> 1. Přejděte k novému [portál Azure – registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs) podokně.
> 1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
> 1. Postupujte podle pokynů ke stažení a automatické konfiguraci nové aplikace jedním kliknutím.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2: Registraci a ručně konfiguraci vaší aplikace a ukázku kódu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Zaregistrujte svoji aplikaci.
> Pokud chcete zaregistrovat aplikaci a ručně přidat informace o registraci aplikace ke svému řešení, postupujte následovně:
>
> 1. Přejděte na Microsoft identity platform pro vývojáře [registrace aplikací](https://aka.ms/MobileAppReg) stránky.
> 1. Vyberte **registrace nové**.
> 1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
>      - V **název** části, zadejte název smysluplné aplikace, který bude zobrazen uživatelům aplikace při přihlášení nebo souhlas s vaší aplikace, například `iOSQuickstart`.
>      - Přeskočte další konfigurace na této stránce. 
>      - Klikněte `Register` tlačítko.
> 1. Klikněte na novou aplikaci > přejděte na `Authentication`  >  `Add Platform`  >  `iOS`.    
>      - Zadejte ***identifikátor sady prostředků*** pro vaši aplikaci. 
> 1. Vyberte `Configure` a uložit ***MSAL konfigurace*** podrobnosti pro pozdější. 

> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>Krok 1: Konfigurace aplikace
> Ukázka kódu pro tento rychlý start pro práci budete muset přidat kompatibilní s zprostředkovatele vícefaktorového ověřování identifikátoru URI přesměrování. 
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Udělat změnu za mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-ios/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-your-web-server-or-project"></a>Krok 2: Stažení webového serveru nebo projektu

- [Stáhněte si ukázky kódu](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Krok 3: Konfigurace projektu

> [!div renderon="docs"]
> Pokud jste vybrali možnost 1 výše, můžete přeskočit tyto kroky. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Extrahujte soubor zip a otevřete projekt v XCode.
> 1. Upravte soubor **ViewController.swift** a nahraďte řádek začínající na „let kClientID“ následujícím fragmentem kódu:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_here"
>    let kAuthority = "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
>
>    ```
> 1. Klikněte pravým tlačítkem myši **Info.plist** a vyberte **otevřít jako** > **zdrojový kód**.
> 1. Do kořenového uzlu dict, nahraďte vaše ***Id sady prostředků***:
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
> 
>    ```
> 1. Sestavení a spuštění aplikace! 

> [!div renderon="docs"]
>
> 1. Extrahujte soubor zip a otevřete projekt v XCode.
> 1. Upravit **ViewController.swift** a nahraďte řádek začínající "umožnit kClientID" následujícím fragmentem kódu:
>
>    ```swift
>    let kClientID = "<ENTER_YOUR_APPLICATION/CLIENT_ID>"
> 
>    ```
> 1. Klikněte pravým tlačítkem myši **Info.plist** a vyberte **otevřít jako** > **zdrojový kód**.
> 1. Do kořenového uzlu dict, nahraďte vaše ***Id sady prostředků***:
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.<ENTER_YOUR_BUNDLE_ID></string>
>          </array>
>       </dict>
>    </array>
>
>    ```
> 1. Sestavení a spuštění aplikace! 

## <a name="more-information"></a>Další informace

Další informace o tomto rychlém startu najdete v následujících částech.

### <a name="getting-msal"></a>Získávání MSAL

Knihovna MSAL ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) je knihovna používaná k přihlášení uživatelů a požádat o tokeny pro přístup k rozhraní API chráněné službou Microsoft identity platform. Knihovnu MSAL můžete do své aplikace přidat následujícím způsobem:

```
$ vi Podfile

```
Přidejte do tohoto souboru podfile (s cílem projektu) následující:

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL', '~> 0.4.0'
end

```

### <a name="msal-initialization"></a>Inicializace knihovny MSAL

Odkaz na knihovnu MSAL můžete přidat tak, že přidáte následující kód:

```swift
import MSAL
```

Potom inicializujte knihovnu MSAL pomocí následujícího kódu:

```swift
let authority = try MSALAADAuthority(url: URL(string: kAuthority)!)
            
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)

```

> |Kde: ||
> |---------|---------|
> | `clientId` | ID aplikace z aplikace zaregistrované na webu *portal.azure.com* |
> | `authority` | Microsoft identity platform koncový bod. Ve většině případů to bude *https<span/>://login.microsoftonline.com/common* |
> | `redirectUri` | Identifikátor URI pro přesměrování aplikace. Můžete předat nil použít výchozí hodnotu, nebo vaše vlastní identifikátor URI přesměrování. |

### <a name="additional-app-requirements"></a>Požadavky na další aplikace  

Aplikace musí mít rovněž následující ve vaší `AppDelegate`. Díky tomu MSAL SDK zpracování odpovědi tokenu z aplikace zprostředkovatele vícefaktorového ověřování při provádění ověřování.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
         guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
             return false
         }
         
         return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
     }

```

A konečně, musí vaše aplikace má `LSApplicationQueriesSchemes` položku v vaše ***Info.plist*** spolu s `CFBundleURLTypes`. Ukázka obsahuje to zahrnuté. 

   ```xml 
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauth</string>
      <string>msauthv2</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Přihlašování uživatelů a požádat o tokeny

Knihovna MSAL používá k získání tokenů dvě metody: `acquireToken` a `acquireTokenSilent`.

#### <a name="acquiretoken-getting-a-token-interactively"></a>acquireToken: Při získávání tokenu interaktivně

Některé situace vyžadují uživatelům interakci s platformou identity Microsoft. V těchto případech může být potřeba zvolit svůj účet, zadejte své přihlašovací údaje nebo vaší aplikace oprávnění vyjádřit souhlas koncového uživatele. Například 

* Při prvním přihlášení uživatele k aplikaci
* Pokud uživatel obnoví svoje heslo, bude nutné k zadání přihlašovacích údajů 
* Když vaše aplikace požaduje přístup k prostředku poprvé
* Když se vyžaduje vícefaktorové ověřování nebo jiných zásad podmíněného přístupu

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
applicationContext.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Kde:||
> |---------|---------|
> | `scopes` | Obsahuje obory žádá (to znamená `[ "user.read" ]` pro Microsoft Graph nebo `[ "<Application ID URL>/scope" ]` pro vlastní webová rozhraní API (`api://<Application ID>/access_as_user`) |

#### <a name="acquiretokensilent-getting-an-access-token-silently"></a>acquireTokenSilent: Získání přístupového tokenu bez upozornění

Aplikace by neměl vyžaduje, aby uživatelé přihlásit pokaždé, když požádají token. Pokud má uživatel již přihlášení, tato metoda umožňuje aplikacím požádat o tokeny bezobslužně. 

```swift
let parameters = MSALSilentTokenParameters(scopes: kScopes, account: applicationContext.allAccounts().first)
applicationContext.acquireTokenSilent(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Kde: ||
> |---------|---------|
> | `scopes` | Obsahuje obory žádá (to znamená `[ "user.read" ]` pro Microsoft Graph nebo `[ "<Application ID URL>/scope" ]` pro vlastní webová rozhraní API (`api://<Application ID>/access_as_user`) |
> | `account` | Pro žádá token účtu. V tomto rychlém startu je jeden účet aplikace, pokud chcete sestavovat aplikace s více účtu budete muset definovat logiku k identifikaci účtu, který se má použít pro žádosti o tokeny `applicationContext.account(forHomeAccountId: self.homeAccountId)` |

## <a name="next-steps"></a>Další postup

Vyzkoušejte si kurz iOS pro úplný podrobný návod na vytváření aplikací, včetně úplné vysvětlení tohoto rychlého startu.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Postup vytvoření aplikace použité v tomto rychlém startu

> [!div class="nextstepaction"]
> [Kurz volání rozhraní Graph API pro iOS](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]