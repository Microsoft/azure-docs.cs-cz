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
ms.date: 04/26/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45252cc4d45e96c2bde4a4600630ea578a8d3009
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64946728"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Rychlý start: Přihlašování uživatelů a volání rozhraní Microsoft Graph API z aplikace pro Android

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Tento rychlý start obsahuje vzorek kódu, který demonstruje, jak může aplikace pro Android přihlásit uživatele v rámci osobního nebo pracovního a školního účtu, získat přístupový token a volat rozhraní Microsoft Graph API.

![Ukazuje, jak ukázková aplikace vygenerované v tomto rychlém startu funguje](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Požadavky**
> * Android Studio 
> * Android 16 + je povinný 


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
> 1. Přejděte na Microsoft identity platform pro vývojáře [registrace aplikací](https://aka.ms/MobileAppReg) stránky.
> 1. Vyberte **registrace nové**.
> 1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
>      - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `AndroidQuickstart`.
>      - Na této stránce můžete přeskočit další konfigurace. 
>      - Klikněte `Register` tlačítko.
> 1. Klikněte na novou aplikaci > přejděte na `Authentication`  >  `Add Platform`  >  `Android`.    
>      - Zadejte název balíčku z vašeho projektu Android studio. 
>      - Generování hodnoty Hash podpisu. Odkazovat na portálu a pokyny.
> 1. Vyberte `Configure` a uložit ***MSAL konfigurace*** JSON pro pozdější. 

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Krok 1: Konfigurace aplikace
> Ukázka kódu pro tento rychlý start pro práci budete muset přidat kompatibilní s zprostředkovatele vícefaktorového ověřování identifikátoru URI přesměrování. 
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Udělat změnu za mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-android/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.

#### <a name="step-2-download-the-project"></a>Krok 2: Stáhněte si projekt

* [Stáhněte si ukázky kódu](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Krok 3: Konfigurace projektu

> [!div renderon="docs"]
> Pokud jste vybrali možnost 1 výše, můžete přeskočit tyto kroky. 

> [!div renderon="portal" class="sxs-lookup"]
> 1. Extrahujte a otevřete projekt v nástroji Android Studio.
> 1. Uvnitř **aplikace** > **src** > **hlavní** > **res**  >   **Nezpracovaná**, otevřete **auth_config.json**.
> 1. Upravit **auth_config.json** a nahraďte ji metodou ve formátu JSON na webu Azure Portal. Pokud chcete místo toho ručně provést změny:
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
> 1. Inside **app** > **manifests**, open  **AndroidManifest.xml**.
> 1. Paste the following activity to the **manifest\application** node: 
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
> > 1. Spusťte aplikaci. 

> [!div renderon="docs"]
> 1. Extrahujte a otevřete projekt v nástroji Android Studio.
> 1. Uvnitř **aplikace** > **res** > **nezpracovaná**, otevřete **auth_config.json**.
> 1. Upravit **auth_config.json** a nahraďte ji metodou ve formátu JSON na webu Azure Portal. Pokud místo toho chcete tyto změny provést ručně:
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
>            <data android:scheme="msauth"
>                android:host="Enter_the_Package_Name"
>                android:path="/Enter_the_Decoded_Signature_Hash" />
>        </intent-filter>
>    </activity>
>    ```
> 1. Nahraďte `Enter_the_Package_Name` a `Enter_the_Signature_Hash` hodnotami, které jste zaregistrovali na webu Azure Portal. 
> 1. Spusťte aplikaci. 

## <a name="more-information"></a>Další informace

Pročtěte si následující oddíly, které obsahují další informace o tomto rychlém startu.

### <a name="getting-msal"></a>Získávání MSAL

Knihovna MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) je knihovna používaná k přihlášení uživatelů a požádat o tokeny pro přístup k rozhraní API chráněné službou Microsoft identity platform. Můžete použít Gradle 3.0 nainstalovat přidáním následujícího kódu v **skriptů Gradle** > **build.gradle (modul: aplikace)** pod **závislosti**:

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
> |`R.raw.auth_config` | Tento soubor obsahuje konfigurací pro vaši aplikaci, včetně vaše ID klienta aplikace/přihlášení cílovou skupinu, identifikátor URI pro přesměrování a řady dalších možností přizpůsobení. |

### <a name="requesting-tokens"></a>Žádosti o tokeny

Knihovna MSAL používá k získání tokenů dvě metody: `acquireToken` a `acquireTokenSilentAsync`.

#### <a name="acquiretoken-getting-a-token-interactively"></a>acquireToken: Při získávání tokenu interaktivně

Některé situace vyžadují uživatelům interakci s platformou identity Microsoft. V těchto případech se koncový uživatel může být nutné zvolit svůj účet, zadejte své přihlašovací údaje nebo oprávnění, která vaše aplikace vyžaduje vyjádřit souhlas. Například: 

* Při prvním přihlášení uživatele k aplikaci
* Pokud uživatel obnoví svoje heslo, bude nutné k zadání přihlašovacích údajů 
* Pokud odvolání souhlasu 
* Pokud vaše aplikace vyžaduje výslovně souhlas. 
* Když vaše aplikace požaduje přístup k prostředku poprvé
* Když se vyžaduje vícefaktorové ověřování nebo jiných zásad podmíněného přístupu

```java
sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
```

> |Kde:||
> |---------|---------|
> | `SCOPES` | Obsahuje požadované obory (to znamená `{ "user.read" }` pro Microsoft Graph nebo `{ "<Application ID URL>/scope" }` pro vlastní webová rozhraní API (např. `api://<Application ID>/access_as_user`) |
> | `getAuthInteractiveCallback` | Zpětné volání, když se ovládací prvek předá zpět do aplikace po ověření |

#### <a name="acquiretokensilent-getting-a-user-token-silently"></a>acquireTokenSilent: Získání tokenu uživatele bez upozornění

Aplikace by neměl vyžaduje, aby uživatelé přihlásit pokaždé, když požádají token. Pokud má uživatel již přihlášení, tato metoda umožňuje aplikacím požádat o tokeny bezobslužně.

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
> | `getAccounts(...)` | Obsahuje účet, který se snažíte získat tokeny pro bezobslužné |
> | `getAuthSilentCallback()` | Zpětné volání, když se ovládací prvek předá zpět do aplikace po ověření |

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
