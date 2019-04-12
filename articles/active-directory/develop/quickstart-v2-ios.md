---
title: Rychlý start Microsoft identity platform iOS | Azure
description: Zjistěte, jak přihlašovat uživatele a dotazovat Microsoft Graph v nativní aplikaci pro iOS.
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6340e0f349d66ecf6baaca481722396a6d786c5
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496125"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-native-app"></a>Rychlý start: Přihlašování uživatelů a volání rozhraní Microsoft Graph API z nativních aplikací pro iOS

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Tento rychlý start obsahuje vzorový kód, který předvádí, jak může nativní aplikace pro iOS přihlašovat uživatele pomocí osobního nebo pracovního a školního účtu, získat přístupový token a volat rozhraní Microsoft Graph API.

![Ukazuje, jak ukázková aplikace vygenerované v tomto rychlém startu funguje](media/quickstart-v2-ios/ios-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download"></a>Registrace a stažení
> ### <a name="register-and-configure-your-application-and-code-sample"></a>Zaregistrujte si a nakonfigurujte svoji aplikaci a vzorový kód
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace vaší aplikace
> Postup při registraci aplikace a přidání informací o registraci aplikace k řešení:
> 1. Abyste mohli zaregistrovat aplikaci, přejděte na [portál pro registraci aplikací Microsoft](https://apps.dev.microsoft.com/portal/register-app).
> 1. Do pole **Application Name** (Název aplikace) zadejte název vaší aplikace.
> 1. Zkontrolujte, že není zaškrtnuté políčko **Guided Setup** (Instalační program s asistencí) a vyberte **Create** (Vytvořit).
> 1. Vyberte **Add Platform** (Přidat platformu), **Native Application** (Nativní aplikace) a pak **Save** (Uložit).

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Krok 1: Konfigurace aplikace
> Pro ukázkový kód pro tento rychlý start pro práci, budete muset přidat adresu URL odpovědi jako `msal<AppId>://auth` (kde msal\<AppId > je tato aplikace Id).
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Provedení této změny pro mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurovali](media/quickstart-v2-ios/green-check.png) vaše aplikace je nakonfigurovaná pomocí tohoto atributu

#### <a name="step-2-download-your-web-server-or-project"></a>Krok 2: Stažení webového serveru nebo projektu

- [Stáhněte si projekt XCode](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Krok 3: Konfigurace projektu

1. Extrahujte soubor zip a otevřete projekt v XCode.
1. Upravte soubor **ViewController.swift** a nahraďte řádek začínající na „let kClientID“ následujícím fragmentem kódu:

    > [!div renderon="portal" class="sxs-lookup"]
    > ```swift
    > let kClientID = "Enter_the_Application_Id_here"
    > ```

    > [!div renderon="docs"]
    > ```swift
    > let kClientID = "<ENTER_THE_APPLICATION_ID_HERE>"
    > ```   
1. Stiskněte Ctrl a kliknutím na soubor **Info.plist** otevřete místní nabídku. Pak vyberte **Open As** > **Source Code** (Otevřít jako > Zdrojový kód).
1. Do kořenového uzlu adresáře přidejte následující kód:

    > [!div renderon="portal" class="sxs-lookup"]
    > ```xml
    > <key>CFBundleURLTypes</key>
    > <array>
    >     <dict>
    >         <key>CFBundleTypeRole</key>
    >         <string>Editor</string>
    >         <key>CFBundleURLName</key>
    >         <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
    >         <key>CFBundleURLSchemes</key>
    >         <array>
    >             <string>msalEnter_the_Application_Id_here</string>
    >         </array>
    >     </dict>
    > </array>
    > ```

    > [!div renderon="docs"]
    > ```xml
    > <key>CFBundleURLTypes</key>
    > <array>
    >     <dict>
    >         <key>CFBundleTypeRole</key>
    >         <string>Editor</string>
    >         <key>CFBundleURLName</key>
    >         <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
    >         <key>CFBundleURLSchemes</key>
    >         <array>
    >             <string>msal<ENTER_THE_APPLICATION_ID_HERE></string>
    >         </array>
    >     </dict>
    > </array>
    > ```
    
> [!div renderon="docs"]
> <span>5.</span> Místo `<ENTER_THE_APPLICATION_ID_HERE>` zadejte *ID vaší aplikace*. Pokud potřebuje vyhledat *ID aplikace*, přejděte na stránku *Overview* (Přehled).

## <a name="more-information"></a>Další informace

Další informace o tomto rychlém startu najdete v následujících částech.

### <a name="msal"></a>MSAL

MSAL ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) je knihovna, která slouží k přihlašování uživatelů a vyžádání tokenů pro přístup k rozhraní API chráněného službou Microsoft Azure Active Directory. Knihovnu MSAL můžete do své aplikace přidat následujícím způsobem:

```
$ vi Podfile
```
Do tohoto souboru podfile přidejte následující:

```
 target 'QuickStart' do
   use_frameworks!
 pod 'MSAL'
 end
```

### <a name="msal-initialization"></a>Inicializace knihovny MSAL

Odkaz na knihovnu MSAL můžete přidat tak, že přidáte následující kód:

```swift
import MSAL
```

Potom inicializujte knihovnu MSAL pomocí následujícího kódu:

```swift
let authority = MSALAuthority(url: URL(string: kAuthority)!)
self.applicationContext = try MSALPublicClientApplication(clientId: kClientID, authority: authority)
```

> |Kde: ||
> |---------|---------|
> | `clientId` | ID aplikace z aplikace zaregistrované na webu *portal.azure.com* |
> | `authority` | Microsoft identity platform koncový bod. Ve většině případů to bude *https<span/>://login.microsoftonline.com/common* |

### <a name="requesting-tokens"></a>Žádosti o tokeny

Knihovna MSAL používá k získání tokenů dvě metody: `acquireToken` a `acquireTokenSilent`.

#### <a name="getting-an-access-token-interactively"></a>Interaktivní získání přístupového tokenu

Některé situace vyžadují vynucení uživatelům interakci s Microsoft identity platformy endpoint, který způsobí přepnutí kontextu, prohlížeč systému buď ověření přihlašovacích údajů od uživatelů nebo ho o souhlas. Možné příklady:

* Při prvním přihlášení uživatele k aplikaci
* Když je potřeba, aby uživatelé znovu zadali svoje přihlašovací údaje, protože vypršela platnost hesla
* Když vaše aplikace žádá o přístup k prostředku, ke kterému musí dát uživatel souhlas
* Když je nutné dvoufaktorové ověřování

```swift
applicationContext.acquireToken(forScopes: self.kScopes) { (result, error) in /* Add your handling logic */}
```

> |Kde:||
> |---------|---------|
> | `forScopes` | Obsahuje požadované obory (to znamená `[ "user.read" ]` pro Microsoft Graph nebo `[ "<Application ID URL>/scope" ]` pro vlastní webová rozhraní API (např. `api://<Application ID>/access_as_user`)). |

#### <a name="getting-an-access-token-silently"></a>Získání přístupového tokenu bez upozornění

Nechcete vyžadovat, aby uživatel ověřoval přihlašovací údaje pokaždé, když potřebuje přístup k prostředku. Ve většině případů budete chtít tokeny pořizovat a obnovovat bez nutnosti zásahu uživatele. Po počáteční metodě `acquireToken` můžete použít metodu `acquireTokenSilent` a získat tokeny pro přístup k chráněným prostředkům:

```swift
applicationContext.acquireTokenSilent(forScopes: self.kScopes, account: applicationContext.allAccounts().first) { (result, error) in /* Add your handling logic */}
```

> |Kde: ||
> |---------|---------|
> | `forScopes` | Obsahuje požadované obory (to znamená `[ "user.read" ]` pro Microsoft Graph nebo `[ "<Application ID URL>/scope" ]` pro vlastní webová rozhraní API (např. `api://<Application ID>/access_as_user`)). |
> | `account` | Účet, který o token žádá (knihovna podporuje více účtů v jedné aplikaci). V případě tohoto rychlého startu tato hodnota odkazuje na první účet v mezipaměti (`applicationContext.allAccounts().first`). |

## <a name="next-steps"></a>Další postup

Vyzkoušejte kurz pro iOS, kde najdete podrobný návod k vytváření aplikací a nových funkcí, včetně úplného popisu tohoto rychlého startu.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Postup vytvoření aplikace použité v tomto rychlém startu

> [!div class="nextstepaction"]
> [Kurz iOS volání rozhraní Graph API](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
