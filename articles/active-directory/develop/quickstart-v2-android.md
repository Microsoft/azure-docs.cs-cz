---
title: Microsoft Identity Platform Android – rychlý Start | Azure
description: Přečtěte si, jak můžou aplikace pro Android volat rozhraní API, které vyžaduje přístupové tokeny pomocí koncového bodu Microsoft Identity Platform.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2019
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d4dba67bcf38049939ac40437c3668ee988235
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268503"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Rychlý start: Přihlaste se uživatelům a zavolejte rozhraní API pro Microsoft Graph z aplikace pro Android.

Tento rychlý start obsahuje vzorek kódu, který demonstruje, jak může aplikace pro Android přihlásit uživatele v rámci osobního nebo pracovního a školního účtu, získat přístupový token a volat rozhraní Microsoft Graph API.

![Ukazuje, jak ukázková aplikace vygenerovaná tímto rychlým startem funguje.](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Požadavky**
> * Android Studio 
> * Vyžaduje se Android 16 +. 


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrace a stažení aplikace pro rychlý start
> Aplikaci pro rychlý start můžete spustit dvěma způsoby:
> * Express [Možnost 1: Zaregistrujte a automaticky nakonfigurujte svoji aplikaci a Stáhněte si ukázku kódu.](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * Zásah [Možnost 2: Registrace a ruční konfigurace vaší aplikace a ukázky kódu](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Možnost 1: Zaregistrujte a automaticky nakonfigurujte svoji aplikaci a Stáhněte si ukázku kódu.
> #### <a name="step-1-register-your-application"></a>Krok 1: Zaregistrujte svoji aplikaci.
> K registraci aplikace
> 1. Přejít na nové podokno [Azure Portal-registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AndroidQuickstartPage/sourceType/docs) .
> 1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
> 1. Postupujte podle pokynů ke stažení a automatické konfiguraci nové aplikace jedním kliknutím.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Možnost 2: Registrace a ruční konfigurace vaší aplikace a ukázky kódu
>
> #### <a name="step-1-register-your-application"></a>Krok 1: Zaregistrujte svoji aplikaci.
> Pokud chcete zaregistrovat aplikaci a ručně přidat informace o registraci aplikace ke svému řešení, postupujte následovně:
>
> 1. Přejděte na stránku [Registrace aplikací](https://aka.ms/MobileAppReg) Microsoft Identity Platform for Developers.
> 1. Vyberte **Nová registrace**.
> 1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
>      - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `AndroidQuickstart`.
>      - Na této stránce můžete přeskočit další konfigurace. 
>      - `Register` Stiskněte tlačítko.
> 1. Klikněte na nová aplikace > Přejít na `Authentication`. `Add Platform`  >   >  `Android`    
>      - Zadejte název balíčku z projektu Android Studio. 
>      - Vygeneruje hodnotu hash podpisu. Pokyny najdete na portálu.
> 1. Vyberte `Configure` a uložte JSON ***Configuration MSAL*** pro pozdější verzi. 

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Krok 1: Konfigurovat aplikaci
> Aby ukázka kódu pro tento rychlý Start fungovala, je nutné přidat identifikátor URI pro přesměrování, který je kompatibilní s zprostředkovatelem ověřování. 
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Udělat změnu za mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-android/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-the-project"></a>Krok 2: Stáhnout projekt

* [Stažení ukázky kódu](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Krok 3: Konfigurace projektu

> [!div renderon="docs"]
> Pokud jste vybrali možnost 1 výše, můžete tento postup přeskočit. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Extrahujte a otevřete projekt v nástroji Android Studio.
> 1. V **aplikaci** > **Src** > **hlavní**primární zdroj RAW otevřete auth_config. JSON. >  > 
> 1. Upravte **auth_config. JSON** a nahraďte ho řetězcem json z Azure Portal. Pokud místo toho chcete změny provést ručně:
>    ```javascript
>    {
>       "client_id" : "Enter_the_Application_Id_Here",
>       "authorization_user_agent" : "DEFAULT",
>       "redirect_uri" : "Enter_the_Redirect_Uri_Here",
>       "authorities" : [
>          {
>             "type": "AAD",
>             "audience": {
>                "type": "Enter_the_Audience_Info_Here",
>                "tenant_id": "Enter_the_Tenant_Info_Here"
>             }
>          }
>       ]
>    }
>    ```
> 
> 1. V manifestech **aplikace** > otevřete **soubor souboru AndroidManifest. XML**.
> 1. Do uzlu **manifest\application** vložte následující aktivitu: 
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
>            <data android:scheme="msauth"
>                android:host="Enter_the_Package_Name"
>                android:path="/Enter_the_Signature_Hash" />
>        </intent-filter>
>    </activity>
>    ```
> 1. Spusťte aplikaci. 

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Tento rychlý Start podporuje Enter_the_Supported_Account_Info_Here.

> [!div renderon="docs"]
> 1. Extrahujte a otevřete projekt v nástroji Android Studio.
> 1. V rámci **App** > **res** > **raw**otevřete **auth_config. JSON**.
> 1. Upravte **auth_config. JSON** a nahraďte ho řetězcem json z Azure Portal. Pokud místo toho chcete tyto změny provést ručně:
>    ```javascript
>    "client_id" : "ENTER_YOUR_APPLICATION_ID",
>    "redirect_uri": "ENTER_YOUR_REDIRECT_URI", 
>     ```
> 1. V manifestech **aplikace** > otevřete **soubor souboru AndroidManifest. XML**.
> 1. Do uzlu **manifest\application** přidejte následující aktivitu. Tento fragment kódu zaregistruje **BrowserTabActivity**, aby operační systém mohl obnovit aplikaci po dokončení ověřování:
>    ```xml
>    <!--Intent filter to catch Microsoft's callback after Sign In-->
>    <activity
>        android:name="com.microsoft.identity.client.BrowserTabActivity">
>        <intent-filter>
>            <action android:name="android.intent.action.VIEW" />
>            <category android:name="android.intent.category.DEFAULT" />
>            <category android:name="android.intent.category.BROWSABLE" />
>            <data android:scheme="msauth"
>                android:host="Enter_the_Package_Name"
>                android:path="/Enter_the_Decoded_Signature_Hash" />
>        </intent-filter>
>    </activity>
>    ```
> 1. Hodnoty `Enter_the_Package_Name` a`Enter_the_Signature_Hash` nahraďte hodnotami, které jste zaregistrovali v Azure Portal. 
> 1. Spusťte aplikaci. 

## <a name="more-information"></a>Další informace

Pročtěte si následující oddíly, které obsahují další informace o tomto rychlém startu.

### <a name="getting-msal"></a>Získání MSAL

MSAL ([com. Microsoft. identity. Client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) je knihovna používaná k přihlašování uživatelů a žádosti o tokeny používané pro přístup k rozhraní API chráněnému platformou Microsoft identity. Můžete použít Gradle 3.0 + k jeho instalaci přidáním následujícího do **skriptu** > Gradle**Build. Gradle (Module: App)** v části **závislosti**:

```gradle  
implementation 'com.android.volley:volley:1.1.1'
implementation 'com.microsoft.identity.client:msal:0.3.+'
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
> |`R.raw.auth_config` | Tento soubor obsahuje konfigurace vaší aplikace, včetně ID vaší aplikace/klienta, cílové skupiny přihlášení, identifikátoru URI přesměrování a několika dalších možností přizpůsobení. |

### <a name="requesting-tokens"></a>Žádosti o tokeny

Knihovna MSAL používá k získání tokenů dvě metody: `acquireToken` a `acquireTokenSilentAsync`.

#### <a name="acquiretoken-getting-a-token-interactively"></a>acquireToken: Interaktivní získání tokenu

Některé situace vyžadují, aby uživatelé mohli pracovat s platformou Microsoft identity. V těchto případech může být koncový uživatel muset vybrat svůj účet, zadat jejich přihlašovací údaje nebo vyjádřit souhlas s oprávněními, které vaše aplikace požaduje. Například 

* Při prvním přihlášení uživatele k aplikaci
* Pokud uživatel resetuje heslo, bude muset zadat své přihlašovací údaje. 
* Pokud je souhlas odvolán 
* Pokud vaše aplikace explicitně vyžaduje souhlas. 
* Když vaše aplikace požaduje při prvním pokusu o přístup k prostředku
* Pokud jsou vyžadovány MFA nebo jiné zásady podmíněného přístupu

```java
sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
```

> |Kde:||
> |---------|---------|
> | `SCOPES` | Obsahuje požadované obory (to znamená `{ "user.read" }` pro Microsoft Graph nebo `{ "<Application ID URL>/scope" }` pro vlastní webová rozhraní API (např. `api://<Application ID>/access_as_user`) |
> | `getAuthInteractiveCallback` | Zpětné volání, když se ovládací prvek předá zpět do aplikace po ověření |

#### <a name="acquiretokensilent-getting-a-user-token-silently"></a>acquireTokenSilent: Získání tokenu uživatele bez upozornění

Aplikace by neměly vyžadovat, aby se uživatelé přihlásili pokaždé, když požadují token. Pokud se uživatel už přihlásil, tato metoda umožňuje aplikacím vyžádat tokeny v tichém režimu.

```java
    sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
        @Override
        public void onAccountsLoaded(final List<IAccount> accounts) {

            if (!accounts.isEmpty()) {
                sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
            } else {
                /* No accounts */
            }
        }
    });
```

> |Kde:||
> |---------|---------|
> | `SCOPES` | Obsahuje požadované obory (to znamená `{ "user.read" }` pro Microsoft Graph nebo `{ "<Application ID URL>/scope" }` pro vlastní webová rozhraní API (např. `api://<Application ID>/access_as_user`) |
> | `getAccounts(...)` | Obsahuje účet, ke kterému se pokoušíte získat tokeny pro tichou instalaci. |
> | `getAuthSilentCallback()` | Zpětné volání, když se ovládací prvek předá zpět do aplikace po ověření |

## <a name="next-steps"></a>Další kroky

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Postup vytvoření aplikace použité v tomto rychlém startu

Vyzkoušejte kurz pro Android, kde najdete podrobný návod k vytváření aplikací a nových funkcí, včetně úplného popisu tohoto rychlého startu.

> [!div class="nextstepaction"]
> [Kurz volání Graph API pro Android](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>Wiki knihovny MSAL pro Android

Přečtěte si další informace o knihovně MSAL pro Android:

> [!div class="nextstepaction"]
> [Wiki knihovny MSAL pro Android](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Pomůžeme nám vylepšit platformu Microsoft identity. Řekněte nám, co si myslíte, díky krátkému průzkumu dvou dotazů.

> [!div class="nextstepaction"]
> [Microsoft Identity Platform Survey](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)