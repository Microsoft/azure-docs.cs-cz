---
title: Microsoft Identity Platform Android – rychlý Start | Azure
description: Přečtěte si, jak můžou aplikace pro Android volat rozhraní API, které vyžaduje přístupové tokeny pomocí koncového bodu Microsoft Identity Platform.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2019
ms.author: twhitney
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: e11e47952f70ce0cd212ca93eff1c38f2b3993a8
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71678056"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Rychlý Start: přihlášení uživatelů a volání rozhraní Microsoft Graph API z aplikace pro Android

V tomto rychlém startu se používá ukázka kódu, která předvádí, jak se aplikace pro Android může přihlašovat pomocí osobních, pracovních nebo školních účtů, a pak získá přístupový token a zavolá rozhraní Microsoft Graph API.

![Screenshoft ukázkové aplikace](media/quickstart-v2-android/android-intro.svg)

> [!NOTE]
> **Požadovaný**
> * Android Studio 
> * Vyžaduje se Android 16 +.

## <a name="step-1-get-the-sample-app"></a>Krok 1: získání ukázkové aplikace

[Naklonujte kód](https://github.com/Azure-Samples/ms-identity-android-java.git).

## <a name="step-2-register-your-application"></a>Krok 2: registrace aplikace

Chcete-li zaregistrovat objekt aplikace a přidat registrační informace objektu aplikace do ukázkového projektu, postupujte podle následujících kroků:

1. Přejít na [Azure Portal](https://aka.ms/MobileAppReg).
1. Otevřete okno [Registrace aplikací](https://portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) a klikněte na **+ Nová registrace**.
1. Zadejte **název** registrace aplikace a pak bez nastavení identifikátoru URI přesměrování klikněte na **zaregistrovat**.
1. V části **Spravovat** vyberte **ověřování** >  **+ Přidat platformu** > **Android**. (Pro zobrazení této obrazovky možná budete muset vybrat **vyzkoušet nové prostředí** v horní části okna.)
1. Zadejte **název balíčku**projektu, což je `com.azuresamples.msalandroidapp`.
1. V části **hash podpisu** na stránce **Konfigurace aplikace pro Android** klikněte na **vygenerovat hodnotu hash signatury pro vývoj**a zkopírujte příkaz nástroje klíče, který použijete pro platformu, kterou používáte k vývoji vaší aplikace pro Android.

   > [!Note]
   > Nástroj Tool. exe je nainstalován jako součást sady Java Development Kit (JDK). Je také nutné nainstalovat nástroj OpenSSL pro spuštění příkazu nástroje.  Budete potřebovat nástroj pro cestu a adresář OpenSSL\bin ve vaší cestě.

1. Spusťte příkaz nástroje, který jste zkopírovali z portálu v okně terminálu.
1. Zadejte hodnotu hash vygenerované signatury na portál pod položkou **hodnota hash podpisu**.
1. Klikněte na `Configure` a vytvořte kopii **Konfigurace MSAL**. Tento soubor zkopírujete a vložíte do konfiguračního souboru v dalším kroku. Klikněte na **Hotovo**.

## <a name="step-3-add-your-app-registration"></a>Krok 3: Přidání registrace vaší aplikace

1. Otevřete vzorový projekt v Android Studio.
1. Uvnitř **aplikace** > **res** > **raw**otevřete **auth_config_multiple_account. JSON**.  Vložte obsah konfigurace MSAL. Tím se na portálu přidá ID klienta, ID tenanta a identifikátor redirect_uri. Bude vypadat přibližně takto, ale s hodnotami, které jsou vyplněny pro ID klienta, ID tenanta a identifikátor redirect_uri:

    ```json
    {
      "client_id" : "<your_client_id_here>",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "<your_redirect_uri_here>",
      "account_mode" : "MULTIPLE",
      "broker_redirect_uri_registered": true,
      "authorities" : [
        {
          "type": "AAD",
          "audience": {
            "type": "AzureADandPersonalMicrosoftAccount",
            "tenant_id": "common"
          }
        }
      ]
    }
    ```

1. Otevřete **app** > **res** > **raw**, otevřete **AUTH_CONFIG_SINGLE_ACCOUNT. JSON**a vložte obsah konfigurace MSAL. Bude vypadat jako v souboru **auth_config_multiple_account. JSON** výše.
1. V **aplikaci app** > **manifestes** > **souboru androidmanifest. XML**vyhledejte aktivitu `BrowserTabActivity`. Tato položka umožňuje, aby Microsoft po dokončení ověřování vrátil zpět do vaší aplikace:

    ```xml
    ...
    <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
                <intent-filter>
                    <action android:name="android.intent.action.VIEW" />
    
                    <category android:name="android.intent.category.DEFAULT" />
                    <category android:name="android.intent.category.BROWSABLE" />
    
                    <!--
                        Add in your scheme/host from registered redirect URI
                        note that the leading "/" is required for android:path
                        For Example:
                        <data
                            android:host="com.azuresamples.msalandroidapp"
                            android:path="/1wIqXSqBj7w+h11ZifsnqwgyKrY="
                            android:scheme="msauth" />
                    -->
    
                    <data
                        android:host="YOUR_PACKAGE_NAME - must be registered at https://aka.ms/MobileAppReg"
                        android:path="/YOUR_DECODED_SIGNATURE_HASH - must be registered at https://aka.ms/MobileAppReg"
                        android:scheme="msauth" />
                </intent-filter>
            </activity>
    ```
    
1. Název balíčku nahraďte tím, co jste zaregistrovali v Azure Portal pro hodnotu `android:host=`.  V tomto případě bude: `com.azuresamples.msalandroidapp`.

    > [!IMPORTANT]
    > Hodnota **Android: path** **musí** mít úvodní znak "/", nebo se pod hodnotou zobrazí červená čára a ukázková aplikace se nespustí.
     
1. Nahraďte hodnotu hash klíče, kterou jste získali, spuštěním nástroje Key dříve a zadáním do Azure Portal pro hodnotu `android:path=`. Hodnota hash podpisu by neměla být kódovaná v adrese URL.

## <a name="step-4-run-the-sample-app"></a>Krok 4: spuštění ukázkové aplikace

V rozevíracím seznamu **dostupná zařízení** Android Studio vyberte svůj emulátor nebo zařízení a spusťte aplikaci.

Ukázková aplikace se spustí na obrazovce **režimu jednoho účtu** . Ve výchozím nastavení je k dispozici výchozí obor ( **User. Read**), který se používá při čtení dat profilu během volání rozhraní API Microsoft Graph. Ve výchozím nastavení je k dispozici adresa URL pro volání rozhraní API Microsoft Graph. Oba tyto typy můžete změnit, pokud chcete.

![Ukázková aplikace MSAL ukazující použití jednoho a více účtů](./media/quickstart-v2-android/quickstart-sample-app.png)

Pomocí nabídky aplikace můžete změnit mezi jedním a více režimy účtu.

V režimu jednoho účtu se přihlaste pomocí pracovního nebo domácího účtu:

1. Vyberte možnost **získat data grafu interaktivně** , aby se uživateli zobrazila výzva k zadání přihlašovacích údajů. V dolní části obrazovky uvidíte výstup volání rozhraní Microsoft Graph API.
2. Po přihlášení vyberte možnost **získat data grafu v tichém režimu** , aby bylo možné volat rozhraní API pro Microsoft Graph bez výzvy k zadání přihlašovacích údajů uživatele. V dolní části obrazovky uvidíte výstup volání rozhraní Microsoft Graph API.

V režimu více účtů můžete opakovat stejný postup.  Kromě toho můžete odebrat přihlášený účet, který také odebere tokeny uložené v mezipaměti pro tento účet.

## <a name="how-the-sample-works"></a>Jak ukázka funguje

Kód je uspořádaný na fragmenty, které ukazují, jak napsat jednu a více účtů MSAL aplikaci. Soubory s kódem jsou uspořádány takto:

| Soubor  | Demonstruje  |
|---------|---------|
| MainActivity | Spravuje uživatelské rozhraní. |
| MSGraphRequestWrapper  | Volá rozhraní API Microsoft Graph pomocí tokenu poskytnutého funkcí MSAL. |
| MultipleAccountModeFragment  | Inicializuje aplikaci s více účty, načte uživatelský účet a získá token pro volání rozhraní API Microsoft Graph. |
| SingleAccountModeFragment | Inicializuje aplikaci s jedním účtem, načte uživatelský účet a získá token pro volání rozhraní API Microsoft Graph. |
| res/auth_config_multiple_account. JSON  | Konfigurační soubor s více účty |
| res/auth_config_single_account. JSON  | Konfigurační soubor s jedním účtem |
| Gradle skripty/Build. Grad (Module: App) | Tady se přidají závislosti knihovny MSAL. |

Nyní se podrobněji podíváme na tyto soubory a v každém z nich budete volat kód specifický pro MSAL.

### <a name="add-msal-to-the-app"></a>Přidání MSAL do aplikace

MSAL ([com. Microsoft. identity. Client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) je knihovna používaná k přihlašování uživatelů a žádosti o tokeny používané pro přístup k rozhraní API chráněnému platformou Microsoft identity. Gradle 3.0 + nainstaluje knihovnu, když přidáte následující do **skriptů Gradle** > **Build. Gradle (Module: App)** v části **závislosti**:

```gradle  
implementation 'com.microsoft.identity.client:msal:1.0.0'
```

Můžete to vidět v ukázkovém projektu v sestavení. Gradle (modul: aplikace):

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:1.0.0-RC7'
    ...
}
```

Tím se dá Gradle stáhnout a sestavit MSAL z Maven Central.

### <a name="msal-imports"></a>MSAL importy

Importy, které jsou relevantní pro knihovnu MSAL, jsou `com.microsoft.identity.client.*`.  Například uvidíte `import com.microsoft.identity.client.PublicClientApplication;`, což je obor názvů pro třídu `PublicClientApplication`, která představuje vaši klientskou aplikaci vaší veřejné aplikace.

### <a name="singleaccountmodefragmentjava"></a>SingleAccountModeFragment. Java

Tento soubor ukazuje, jak vytvořit MSAL aplikaci s jedním účtem a zavolat rozhraní API Microsoft Graph.

Aplikace s jedním účtem používá jenom jeden uživatel.  Například můžete mít jenom jeden účet, který se přihlašujete do aplikace mapování pomocí.

#### <a name="single-account-msal-initialization"></a>MSAL inicializace jednoho účtu

V `onCreateView()` se vytvoří jeden účet `PublicClientApplication` s použitím konfiguračních informací uložených v souboru `auth_config_single_account.json`.  Tímto způsobem inicializujete knihovnu MSAL pro použití v aplikaci MSAL s jedním účtem:

```java
...
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createSingleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_single_account,
        new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(ISingleAccountPublicClientApplication application) {
                /**
                    * This test app assumes that the app is only going to support one account.
                    * This requires "account_mode" : "SINGLE" in the config json file.
                    **/
                mSingleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                displayError(exception);
            }
        });
```

#### <a name="sign-in-a-user"></a>Přihlášení uživatele

Kód pro přihlášení uživatele je v `initializeUI()` v obslužné rutině pro kliknutí `signInButton`.

Než se pokusíte získat tokeny, volejte `signIn()`. `signIn()` se chová stejně, jako by se volala `acquireToken()`. Výsledkem je interaktivní výzva pro uživatele, aby se přihlásil.

Přihlášení uživatele je asynchronní operace. Zpětné volání, které volá rozhraní Microsoft Graph API a aktualizuje uživatelské rozhraní po přihlášení uživatele:

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>Odhlásit uživatele

Kód pro odhlášení uživatele je v `initializeUI()` v obslužné rutině Click `signOutButton`.  Podepsání uživatele je asynchronní operace. Podepsáním uživatele se taky vymaže mezipaměť tokenu pro tento účet. Po odhlášení uživatelského účtu se vytvoří zpětné volání, které aktualizuje uživatelské rozhraní:

```java
mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
    @Override
    public void onSignOut() {
        updateUI(null);
        performOperationOnSignOut();
    }

    @Override
    public void onError(@NonNull MsalException exception) {
        displayError(exception);
    }
});
```

#### <a name="get-a-token-interactively-or-silently"></a>Interaktivní nebo tiché získání tokenu

K získání nejmenšího počtu výzev uživateli obvykle obdržíte token v tichém režimu. Pokud dojde k chybě, pokusí se získat interaktivní token. Když aplikace poprvé volá `signIn()`, efektivně funguje jako volání `acquireToken()`, které uživateli vyzve k zadání přihlašovacích údajů.

Některé situace, kdy se uživatel může zobrazit výzva k výběru účtu, zadání přihlašovacích údajů nebo vyjádření souhlasu s oprávněními, které vaše aplikace požaduje:

* Když se uživatel poprvé přihlásí k aplikaci
* Pokud uživatel resetuje heslo, bude muset zadat své přihlašovací údaje.
* Pokud je souhlas odvolán
* Pokud vaše aplikace explicitně vyžaduje souhlas
* Když vaše aplikace požaduje při prvním pokusu o přístup k prostředku
* Pokud jsou vyžadovány MFA nebo jiné zásady podmíněného přístupu

Kód pro interaktivní získání tokenu, který je s uživatelským ROZHRANÍm, které bude zahrnovat uživatele, je v `initializeUI()` v obslužné rutině `callGraphApiInteractiveButton` pro kliknutí:

```java
/**
 * If acquireTokenSilent() returns an error that requires an interaction (MsalUiRequiredException),
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your Single Sign-On refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 */
mSingleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Pokud se uživatel už přihlásil, `acquireTokenSilentAsync()` umožňuje aplikacím, aby vyžádala tokeny v tichém režimu, jak je znázorněno v `initializeUI()`, v obslužné rutině `callGraphApiSilentButton`:

```java
/**
  * Once you've signed the user in,
  * you can perform acquireTokenSilent to obtain resources without interrupting the user.
  */
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>Načtení účtu

Kód pro načtení účtu je v `loadAccount()`.  Načtení účtu uživatele je asynchronní operace, takže zpětná volání, která mají být zpracována, když se účet načte, změní nebo dojde k chybě, se předává do MSAL.  Následující kód také zpracovává `onAccountChanged()`, ke kterým dojde při odebrání účtu, uživatel se změní na jiný účet atd.

```java
private void loadAccount() {
    ...

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }

        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
```

#### <a name="call-microsoft-graph"></a>Microsoft Graph volání

Když je uživatel přihlášený, volání Microsoft Graph se provádí prostřednictvím požadavku HTTP pomocí `callGraphAPI()`. Tato funkce je obálkou, která zjednodušuje vzorek provedením některých úkolů, jako je získání přístupového tokenu z `authenticationResult` a zabalení volání do MSGraphRequestWrapper a zobrazení výsledků volání.

```java
private void callGraphAPI(final IAuthenticationResult authenticationResult) {
    MSGraphRequestWrapper.callGraphAPIUsingVolley(
            getContext(),
            graphResourceTextView.getText().toString(),
            authenticationResult.getAccessToken(),
            new Response.Listener<JSONObject>() {
                @Override
                public void onResponse(JSONObject response) {
                    /* Successfully called graph, process data and send to UI */
                    ...
                }
            },
            new Response.ErrorListener() {
                @Override
                public void onErrorResponse(VolleyError error) {
                    ...
                }
            });
}
```

### <a name="auth_config_single_accountjson"></a>auth_config_single_account. JSON

Toto je konfigurační soubor pro aplikaci MSAL, která používá jediný účet.

Vysvětlení těchto polí najdete v tématu Vysvětlení [konfiguračního souboru pro Android MSAL](msal-configuration.md) .

Poznamenejte si přítomnost `"account_mode" : "SINGLE"`, která nakonfiguruje tuto aplikaci tak, aby používala jediný účet.

```json
{
  "client_id" : "<your_client_id_here>",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "<your_redirect_uri_here>",
  "account_mode" : "SINGLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

### <a name="multipleaccountmodefragmentjava"></a>MultipleAccountModeFragment. Java

Tento soubor ukazuje, jak vytvořit MSAL aplikaci s více účty a volat rozhraní API pro Microsoft Graph. 

Příkladem aplikace s více účty je e-mailová aplikace, která umožňuje pracovat s několika uživatelskými účty, jako je například pracovní účet a osobní účet.

#### <a name="multiple-account-msal-initialization"></a>Vícenásobná inicializace MSAL účtu

V `onCreateView()` se vytvoří objekt aplikace s více účty (`IMultipleAccountPublicClientApplication`) s použitím informací o konfiguraci uložených v `auth_config_multiple_account.json file`:

```java
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_multiple_account,
        new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(IMultipleAccountPublicClientApplication application) {
                mMultipleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                ...
            }
        });
```

Vytvořený objekt `MultipleAccountPublicClientApplication` je uložen v proměnné členu třídy, aby jej bylo možné použít k interakci s knihovnou MSAL k získání tokenů a načtení a odebrání uživatelského účtu.

#### <a name="load-an-account"></a>Načtení účtu

Pro výběr účtu, který se má použít pro operace MSAL, je obvykle volání `GetAccounts()` na více účtů. Kód pro načtení účtu je v `loadAccount()`.  Načtení účtu uživatele je asynchronní operace. Proto zpětné volání zpracovává situace při načtení účtu, změny nebo dojde k chybě.

```java
/**
    * Load the currently signed-in account, if there's any.
    * In the shared device mode, if the user is signed out from the device, the app can also perform the clean-up work in onAccountChanged().
    */
private void loadAccount() {
    ...
    mMultipleAccountApp.getAccounts(new IPublicClientApplication.LoadAccountsCallback() {
        @Override
        public void onTaskCompleted(final List<IAccount> result) {
            // You can use the account data to update your UI or your app database.
            accountList = result;
            updateUI(accountList);
        }

        @Override
        public void onError(MsalException exception) {
            displayError(exception);
        }
    });
}
```

#### <a name="get-a-token-interactively-or-silently"></a>Interaktivní nebo tiché získání tokenu

Některé situace, kdy se uživatel může zobrazit výzva k výběru účtu, zadání přihlašovacích údajů nebo vyjádření souhlasu s oprávněními, které vaše aplikace požaduje:

* Když se uživatel poprvé přihlásí k aplikaci
* Pokud uživatel resetuje heslo, bude muset zadat své přihlašovací údaje. 
* Pokud je souhlas odvolán 
* Pokud vaše aplikace explicitně vyžaduje souhlas 
* Když vaše aplikace požaduje při prvním pokusu o přístup k prostředku
* Pokud jsou vyžadovány MFA nebo jiné zásady podmíněného přístupu

K interaktivnímu získávání tokenů by měly být obvykle vyvolány aplikace s více účty, což je uživatelské rozhraní, které zahrnuje uživatele, se zavoláním `acquireToken()`.  Kód pro interaktivní získání tokenu je v `initializeUI()` v obslužné rutině Click-1 `callGraphApiInteractiveButton`:

```java
/**
 * Acquire token interactively. It will also create an account object for the silent call as a result (to be obtained by getAccount()).
 *
 * If acquireTokenSilent() returns an error that requires an interaction,
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your SSO refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 */
mMultipleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Aplikace by neměly vyžadovat, aby se uživatelé přihlásili pokaždé, když požadují token. Pokud se uživatel už přihlásil, `acquireTokenSilentAsync()` umožňuje aplikacím žádat o tokeny bez zobrazení výzvy uživateli, jak je znázorněno v `initializeUI()` v obslužné rutině Click-2 pro `callGraphApiSilentButton`:

```java
/**
 * Performs acquireToken without interrupting the user.
 *
 * This requires an account object of the account you're obtaining a token for.
 * (can be obtained via getAccount()).
 */
mMultipleAccountApp.acquireTokenSilentAsync(getScopes(),
accountList.get(accountListSpinner.getSelectedItemPosition()),
AUTHORITY,
getAuthSilentCallback());
```

#### <a name="remove-an-account"></a>Odebrat účet

Kód pro odebrání účtu a všechny tokeny uložené v mezipaměti pro účet jsou v `initializeUI()` v obslužné rutině pro tlačítko Odebrat účet. Než budete moct účet odebrat, budete potřebovat objekt účtu, který získáte z MSAL funkcí, jako je `getAccounts()` a `acquireToken()`. Vzhledem k tomu, že odebrání účtu je asynchronní operace, je k aktualizaci uživatelského rozhraní zadáno zpětné volání `onRemoved`.

```java
/**
  * Removes the selected account and cached tokens from this app (or device, if the device is in shared mode).
  */
mMultipleAccountApp.removeAccount(accountList.get(accountListSpinner.getSelectedItemPosition()),
        new IMultipleAccountPublicClientApplication.RemoveAccountCallback() {
            @Override
            public void onRemoved() {
                ...
                /* Reload account asynchronously to get the up-to-date list. */
                loadAccount();
            }

            @Override
            public void onError(@NonNull MsalException exception) {
                displayError(exception);
            }
        });
```

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account. JSON

Toto je konfigurační soubor pro aplikaci MSAL, která používá více účtů.

Vysvětlení těchto polí najdete v tématu Vysvětlení [konfiguračního souboru pro Android MSAL](msal-configuration.md) .

Na rozdíl od konfiguračního souboru [auth_config_single_account. JSON](#auth_config_single_accountjson) má tento konfigurační soubor místo `"account_mode" : "SINGLE"` `"account_mode" : "MULTIPLE"`, protože se jedná o aplikaci s více účty.

```json
{
  "client_id" : "<your_client_id_here>",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "<your_redirect_uri_here>",
  "account_mode" : "MULTIPLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Další kroky

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Naučte se postup vytvoření aplikace používané v tomto rychlém startu.

Vyzkoušejte si kurz k Androidu, kde najdete kompletní podrobný průvodce vytvářením aplikací a nových funkcí, včetně úplného vysvětlení tohoto rychlého startu.

> [!div class="nextstepaction"]
> [Kurz pro volání Graph API Android](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>MSAL pro wiki knihovny pro Android

Přečtěte si další informace o knihovně MSAL pro Android:

> [!div class="nextstepaction"]
> [MSAL pro wiki knihovny pro Android](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Pomůžeme nám vylepšit platformu Microsoft identity. Řekněte nám, co si myslíte, díky krátkému průzkumu dvou dotazů.

> [!div class="nextstepaction"]
> [Microsoft Identity Platform Survey](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
