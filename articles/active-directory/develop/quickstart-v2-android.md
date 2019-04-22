---
title: Rychlý start Microsoft identity platform Androidu | Azure
description: Zjistěte, jak s Androidem aplikace může volat rozhraní API, které vyžadují přístupové tokeny ve Microsoft identity platform koncový bod.
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
ms.date: 04/11/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1f174229da565627c0e5791f53031b338880cb3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59495307"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Rychlý start: Přihlašování uživatelů a volání rozhraní Microsoft Graph API z aplikace pro Android

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Tento rychlý start obsahuje vzorek kódu, který demonstruje, jak může aplikace pro Android přihlásit uživatele v rámci osobního nebo pracovního a školního účtu, získat přístupový token a volat rozhraní Microsoft Graph API.

![Ukazuje, jak ukázková aplikace vygenerované v tomto rychlém startu funguje](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Požadavky**
> * Android Studio 3+
> * Android 21 + je povinný 


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrace a stažení aplikace pro rychlý start
> Aplikaci pro rychlý start můžete spustit dvěma způsoby:
> * [Express] [Možnost 1: Registrace a automaticky konfigurovat svoji aplikaci a pak si stáhnout ukázku kódu](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Ruční] [Možnost 2: Registraci a ručně konfiguraci vaší aplikace a ukázku kódu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1: Registrace a automaticky konfigurovat svoji aplikaci a pak si stáhnout ukázku kódu
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace vaší aplikace
> Registrace vaší aplikace
> 1. Přejděte k novému [portál Azure – registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AndroidQuickstartPage/sourceType/docs) podokně.
> 1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
> 1. Postupujte podle pokynů ke stažení a automatické konfiguraci nové aplikace jedním kliknutím.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2: Registraci a ručně konfiguraci vaší aplikace a ukázku kódu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Registrace vaší aplikace
> Pokud chcete zaregistrovat aplikaci a ručně přidat informace o registraci aplikace ke svému řešení, postupujte následovně:
>
> 1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
> 1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
> 1. Přejděte na Microsoft identity platform pro vývojáře [registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) stránky.
> 1. Vyberte **registrace nové**.
> 1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
>      - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `Android-Quickstart`.
>      - Klikněte `Register` tlačítko.
> 1. Přejděte na `Authentication`  >  `Redirect URIs`  >  `Suggested Redirect URIs for public clients`a vyberte identifikátor URI přesměrování formátu **msal {AppId} :/ / auth**. Uložte změny.


> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Krok 1: Konfigurace aplikace
> Aby vzorek kódu pro tento rychlý start fungoval, budete muset přidat adresu URL odpovědi jako **msal{AppId}://auth** (kde {AppId} je ID vaší aplikace).
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Udělat změnu za mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-android/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-the-project"></a>Krok 2: Stáhněte si projekt

* [Stáhnout projekt pro Android Studio](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Krok 3: Konfigurace projektu

> [!div renderon="docs"]
> Pokud jste vybrali možnost 1 výše, můžete přeskočit tyto kroky. Otevřete projekt v nástroji Android Studio a spusťte aplikaci. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Extrahujte a otevřete projekt v nástroji Android Studio.
> 1. Uvnitř **aplikace** > **res** > **nezpracovaná**, otevřete **auth_config.json**.
> 1. Upravit **auth_config.json** a nahraďte `client_id` a `tenant_id`:
>    ```javascript
>    "client_id" : "Enter_the_Application_Id_Here",
>    "type": "Enter_the_Audience_Info_Here",
>    "tenant_id" : "Enter_the_Tenant_Info_Here"
>    ```
> 1. Uvnitř **aplikace** > **manifesty**, otevřete **AndroidManifest.xml**.
> 1. Do uzlu **manifest\application** přidejte následující aktivitu. Tento kód umožňuje společnosti Microsoft pro zpětné volání do vaší aplikace:   
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
> 
>            <!--Add in your scheme/host from registered redirect URI-->
>            <!--By default, the scheme should be similar to 'msal[appId]' -->
>            <data android:scheme="msalEnter_The_Application_Id_Here"
>                android:host="auth" />
>        </intent-filter>
>    </activity>
>    ```

> [!div renderon="docs"]
> 1. Extrahujte a otevřete projekt v nástroji Android Studio.
> 1. Uvnitř **aplikace** > **res** > **nezpracovaná**, otevřete **auth_config.json**.
> 1. Upravit **auth_config.json** a nahraďte `client_id` a `redirect_uri`:
>    ```javascript
>    "client_id" : "ENTER_YOUR_APPLICATION_ID",
>    "redirect_uri": "ENTER_YOUR_REDIRECT_URI", 
>     ```
> 1. Uvnitř **aplikace** > **manifesty**, otevřete **AndroidManifest.xml**.
> 1. Do uzlu **manifest\application** přidejte následující aktivitu. Tento fragment kódu zaregistruje **BrowserTabActivity**, aby operační systém mohl obnovit aplikaci po dokončení ověřování:
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
> 
>            <!--Add in your scheme/host from registered redirect URI-->
>            <!--By default, the scheme should be similar to 'msal[appId]' -->
>            <data android:scheme="msal<ENTER_YOUR_APPLICATION_ID>"
>                android:host="auth" />
>        </intent-filter>
>    </activity>
>    ```
> 1. Místo `<ENTER_THE_APPLICATION_ID_HERE>` zadejte *ID vaší aplikace*. Pokud potřebuje vyhledat *ID aplikace*, přejděte na stránku *Overview* (Přehled).

## <a name="more-information"></a>Další informace

Pročtěte si následující oddíly, které obsahují další informace o tomto rychlém startu.

### <a name="msal"></a>MSAL

Knihovna MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) je knihovna používaná k přihlášení uživatelů a požádat o tokeny pro přístup k rozhraní API chráněné službou Microsoft identity platform. Nainstalovat ji můžete pomocí nástroje Gradle tak, že přidáte následující řetězec v části **Gradle Scripts** (Skripty Gradle)  > **build.gradle (Module: app)** v části **Dependencies** (Závislosti):

```gradle  
implementation 'com.android.volley:volley:1.1.1'
implementation 'com.microsoft.identity.client:msal:0.2.+'
```

### <a name="msal-initialization"></a>Inicializace knihovny MSAL

Odkaz na knihovnu MSAL můžete přidat tak, že přidáte následující kód:

```java
import com.microsoft.identity.client.*;
```

Potom inicializujte knihovnu MSAL pomocí následujícího kódu:

```java
    sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        R.raw.auth_config);
```

> |Kde: ||
> |---------|---------|
> |`R.raw.auth_config` | Tento soubor obsahuje konfigurací pro vaši aplikaci, včetně ID aplikace/klienta, přihlášení cílové skupiny a řady dalších možností přizpůsobení. |

### <a name="requesting-tokens"></a>Žádosti o tokeny

Knihovna MSAL používá k získání tokenů dvě metody: `acquireToken` a `acquireTokenSilentAsync`.

#### <a name="getting-a-user-token-interactively"></a>Interaktivní získání tokenu uživatele

Některé situace vyžadují vynucení uživatelům interakci s Microsoft identity platform koncový bod, jaké výsledky v kontextu přepnout prohlížeč systému se buď ověřit přihlašovací údaje uživatele nebo pro vyjádření souhlasu. Možné příklady:

* Při prvním přihlášení uživatele k aplikaci
* Když je potřeba, aby uživatelé znovu zadali svoje přihlašovací údaje, protože vypršela platnost hesla
* Když vaše aplikace žádá o přístup k prostředku, ke kterému musí dát uživatel souhlas
* Když je nutné dvoufaktorové ověřování

```java
sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
```

> |Kde:||
> |---------|---------|
> | `SCOPES` | Obsahuje požadované obory (to znamená `{ "user.read" }` pro Microsoft Graph nebo `{ "<Application ID URL>/scope" }` pro vlastní webová rozhraní API (např. `api://<Application ID>/access_as_user`) |
> | `getAuthInteractiveCallback` | Zpětné volání, když se ovládací prvek předá zpět do aplikace po ověření |

#### <a name="getting-a-user-token-silently"></a>Získání tokenu uživatele bez upozornění

Nechcete vyžadovat, aby uživatel ověřoval přihlašovací údaje pokaždé, když potřebuje přístup k prostředku. Ve většině případů budete chtít tokeny pořizovat a obnovovat bez nutnosti zásahu uživatele. Po počáteční metodě `acquireToken` můžete použít metodu `AcquireTokenSilentAsync` a získat tokeny pro přístup k chráněným prostředkům:

```java
List<IAccount> accounts = sampleApp.getAccounts();
if (sample.size() == 1) {
    sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
} else {
    // No or multiple accounts
}
```

> |Kde:||
> |---------|---------|
> | `SCOPES` | Obsahuje požadované obory (to znamená `{ "user.read" }` pro Microsoft Graph nebo `{ "<Application ID URL>/scope" }` pro vlastní webová rozhraní API (např. `api://<Application ID>/access_as_user`) |
> | `accounts.get(0)` | Obsahuje účet, který se snažíte získat tokeny pro bezobslužné |
> | `getAuthInteractiveCallback` | Zpětné volání, když se ovládací prvek předá zpět do aplikace po ověření |

## <a name="next-steps"></a>Další postup

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Postup vytvoření aplikace použité v tomto rychlém startu

Vyzkoušejte kurz pro Android, kde najdete podrobný návod k vytváření aplikací a nových funkcí, včetně úplného popisu tohoto rychlého startu.

> [!div class="nextstepaction"]
> [Kurz volání Graph API pro Android](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>Wiki knihovny MSAL pro Android

Přečtěte si další informace o knihovně MSAL pro Android:

> [!div class="nextstepaction"]
> [Wiki knihovny MSAL pro Android](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
