---
title: Microsoft identity platforma Android rychlý start | Azure
description: Zjistěte, jak aplikace pro Android můžou volat rozhraní API, které vyžaduje přístupové tokeny podle koncového bodu platformy identit microsoftu.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/15/2019
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:Android
ms.openlocfilehash: 9afb5b7602b220c25d919f8fe0773d5cfa143d89
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991190"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Rychlý start: Přihlášení uživatelů a volání rozhraní Microsoft Graph API z aplikace pro Android

Tento rychlý start používá ukázku kódu k předvedení, jak se aplikace pro Android může přihlásit k osobním, pracovním nebo školním účtům pomocí platformy identit Microsoftu a pak získat přístupový token a zavolat rozhraní Microsoft Graph API. (Viz [jak ukázka funguje](#how-the-sample-works) pro ilustraci.)

Aplikace musí být reprezentovány objektem aplikace ve službě Azure Active Directory, aby platforma identit Microsoftu mohla vaší aplikaci poskytovat tokeny.

> [!div renderon="docs"]
> Pro pohodlí ukázka kódu je `redirect_uri` dodáván s `AndroidManifest.xml` výchozí předkonfigurované v souboru, takže není nutné nejprve zaregistrovat vlastní objekt aplikace. A `redirect_uri` je částečně založen na podpisovém klíči vaší aplikace. Ukázkový projekt je předkonfigurován s podpisovým `redirect_uri` klíčem tak, aby zapředpokladu, že bude fungovat. Další informace o registraci objektu aplikace a jeho integraci s aplikací najdete v tématu [Přihlášení uživatelů a volání Microsoft Graphu z](tutorial-v2-android.md) kurzu aplikace pro Android.


> [!NOTE]
> **Požadavky**
> * Android Studio 
> * Android 16+

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1: Konfigurace aplikace na webu Azure Portal 
>  Pro ukázku kódu pro tento rychlý start do práce, je třeba přidat přesměrování URI kompatibilní s zprostředkovatele ověřování.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Proveďte tyto změny pro mě]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Už nakonfigurované](media/quickstart-v2-android/green-check.png) Vaše aplikace je nakonfigurovaná s těmito atributy.
>
> ### <a name="step-2-download-the-project"></a>Krok 2: Stažení projektu 
> [!div class="sxs-lookup" renderon="portal"]
> Spusťte projekt pomocí Sady Android.
> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [Stáhnout ukázku kódu](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip)
>
> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Krok 3: Aplikace je nakonfigurovaná a připravená ke spuštění
> Nakonfigurovali jsme váš projekt s hodnotami vlastností vaší aplikace a je připravený ke spuštění. 
> Ukázková aplikace se spustí na obrazovce **Režim jednoho účtu.** Výchozí **obor, user.read**, je k dispozici ve výchozím nastavení, který se používá při čtení vlastních dat profilu během volání rozhraní API aplikace Microsoft Graph. Adresa URL pro volání rozhraní MICROSOFT Graph API je k dispozici ve výchozím nastavení. Můžete změnit oba tyto, pokud si budete přát.
>
> ![Ukázková aplikace MSAL zobrazující využití jednoho a více účtů](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Pomocí nabídky aplikace můžete přepínat mezi režimy jednoho a více účtů.
>
> V režimu jednoho účtu se přihlaste pomocí pracovního nebo domácího účtu:
>
> 1. Vyberte **Získat data grafu interaktivně,** abyste uživatele vyzvali k zadání pověření. V dolní části obrazovky se zobrazí výstup z volání rozhraní Microsoft Graph API.
> 2. Po přihlášení vyberte Možnost **Získat data grafu tiše** a volání rozhraní Microsoft Graph API bez další výzvy uživatele k získání pověření. V dolní části obrazovky se zobrazí výstup z volání rozhraní Microsoft Graph API.
>
> V režimu více účtů můžete opakovat stejné kroky.  Kromě toho můžete odebrat účet přihlášení, který také odebere tokeny uložené v mezipaměti pro tento účet.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> ## <a name="step-1-get-the-sample-app"></a>Krok 1: Získání ukázkové aplikace
>
> [Stáhněte si kód](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip).
>
> ## <a name="step-2-run-the-sample-app"></a>Krok 2: Spuštění ukázkové aplikace
>
> Vyberte emulátor nebo fyzické zařízení z rozbalovací ho řešení **dostupných zařízení** Android Studio a spusťte aplikaci.
>
> Ukázková aplikace se spustí na obrazovce **Režim jednoho účtu.** Výchozí **obor, user.read**, je k dispozici ve výchozím nastavení, který se používá při čtení vlastních dat profilu během volání rozhraní API aplikace Microsoft Graph. Adresa URL pro volání rozhraní MICROSOFT Graph API je k dispozici ve výchozím nastavení. Můžete změnit oba tyto, pokud si budete přát.
>
> ![Ukázková aplikace MSAL zobrazující využití jednoho a více účtů](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Pomocí nabídky aplikace můžete přepínat mezi režimy jednoho a více účtů.
>
> V režimu jednoho účtu se přihlaste pomocí pracovního nebo domácího účtu:
>
> 1. Vyberte **Získat data grafu interaktivně,** abyste uživatele vyzvali k zadání pověření. V dolní části obrazovky se zobrazí výstup z volání rozhraní Microsoft Graph API.
> 2. Po přihlášení vyberte Možnost **Získat data grafu tiše** a volání rozhraní Microsoft Graph API bez další výzvy uživatele k získání pověření. V dolní části obrazovky se zobrazí výstup z volání rozhraní Microsoft Graph API.
>
> V režimu více účtů můžete opakovat stejné kroky.  Kromě toho můžete odebrat účet přihlášení, který také odebere tokeny uložené v mezipaměti pro tento účet.

## <a name="how-the-sample-works"></a>Jak ukázka funguje
![Snímek obrazovky ukázkové aplikace](media/quickstart-v2-android/android-intro.svg)


Kód je uspořádán do fragmentů, které ukazují, jak psát jeden a více účtů MSAL aplikace. Soubory kódu jsou uspořádány takto:

| File  | Demonstruje  |
|---------|---------|
| Hlavní aktivita | Spravuje ui |
| MSGraphRequestWrapper  | Volá rozhraní Microsoft Graph API pomocí tokenu poskytovaného službou MSAL |
| MultipleAccountModeFragment  | Inicializuje aplikaci s více účtůmi, načte uživatelský účet a získá token pro volání rozhraní Microsoft Graph API. |
| SingleAccountModeFragment | Inicializuje aplikaci s jedním účtem, načte uživatelský účet a získá token pro volání rozhraní Microsoft Graph API |
| res/auth_config_multiple_account.json  | Konfigurační soubor s více účtůmi |
| res/auth_config_single_account.json  | Konfigurační soubor jednoho účtu |
| Gradle Skripty/build.grade (Modul: app) | Sem jsou přidány závislosti knihovny MSAL. |

Nyní se podíváme na tyto soubory podrobněji a volat kód specifický pro MSAL v každém z nich.

### <a name="adding-msal-to-the-app"></a>Přidání MSAL do aplikace

MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) je knihovna používaná k přihlášení uživatelů a vyžádání tokenů používaných pro přístup k rozhraní API chráněnému platformou identit microsoftu. Gradle 3.0+ nainstaluje knihovnu, když přidáte následující do **Gradle Scripts** > **build.gradle (Modul: app)** v části **Závislosti**:

```gradle  
implementation 'com.microsoft.identity.client:msal:1.+'
```

Můžete to vidět v ukázkovém projektu v build.gradle (Modul: aplikace):

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:1.+'
    ...
}
```

To instruuje Gradle ke stažení a sestavení MSAL z maven central.

### <a name="msal-imports"></a>Dovozy MSAL

Importy, které jsou relevantní pro knihovnu MSAL jsou `com.microsoft.identity.client.*`.  Například uvidíte, `import com.microsoft.identity.client.PublicClientApplication;` který obor názvů pro `PublicClientApplication` třídu, který představuje vaši veřejnou klientskou aplikaci.

### <a name="singleaccountmodefragmentjava"></a>SingleAccountModeFragment.java

Tento soubor ukazuje, jak vytvořit jeden účet MSAL aplikace a volání rozhraní MICROSOFT Graph API.

Aplikace s jedním účtem používá pouze jeden uživatel.  Můžete mít například pouze jeden účet, pomocí kterého se přihlásíte do mapovací aplikace.

#### <a name="single-account-msal-initialization"></a>Inicializace MSAL s jedním účtem

V `auth_config_single_account.json` `onCreateView()`aplikaci je `PublicClientApplication` vytvořen jeden účet pomocí konfiguračních informací uložených v souboru. `auth_config_single_account.json`  Tímto způsobem inicializujete knihovnu MSAL pro použití v aplikaci MSAL s jedním účtem:

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

V `SingleAccountModeFragment.java`, kód pro přihlášení uživatele `initializeUI()`je `signInButton` v , v obslužné rutině click.

Volání `signIn()` před pokusem o získání tokenů. `signIn()`chová se, `acquireToken()` jako by se nazývá, což má za následek interaktivní výzvu pro uživatele k přihlášení.

Přihlášení uživatele je asynchronní operace. Je předáno zpětné volání, které volá rozhraní Microsoft Graph API a aktualizuje uživatelské rozhraní po přihlášení uživatele:

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>Odhlášení uživatele

V `SingleAccountModeFragment.java`, kód pro odhlášení `initializeUI()`uživatele je `signOutButton` v , v obslužné rutině click.  Odhlášení uživatele je asynchronní operace. Podepsání uživatele také vymaže mezipaměti tokenů pro tento účet. Zpětné volání je vytvořeno pro aktualizaci uživatelského rozhraní po odoznámení uživatelského účtu:

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

#### <a name="get-a-token-interactively-or-silently"></a>Získejte token interaktivně nebo tiše

Chcete-li uživateli zobrazit co nejmenší počet výzev, obvykle získáte token tiše. Pokud se pak zobrazí chyba, pokuste se získat token interaktivně. Při prvním volání `signIn()`aplikace funguje efektivně jako volání `acquireToken()`na , který vyzve uživatele k zadání přihlašovacích údajů.

Některé situace, kdy může být uživatel vyzván k výběru svého účtu, zadání jeho přihlašovacích údajů nebo souhlasu s oprávněními, která vaše aplikace požadovala, jsou:

* Při prvním přihlášení uživatele k aplikaci
* Pokud uživatel resetuje své heslo, bude muset zadat své přihlašovací údaje.
* Je-li souhlas odvolán
* Pokud vaše aplikace výslovně vyžaduje souhlas
* Když vaše aplikace poprvé požaduje přístup k prostředku
* Pokud jsou vyžadovány vícefaktorové povolení nebo jiné zásady podmíněného přístupu

Kód pro získání tokenu interaktivně, to znamená s uživatelským `SingleAccountModeFragment.java`rozhraním, které bude zahrnovat uživatele, je v , v `initializeUI()`, v `callGraphApiInteractiveButton` obslužné rutině kliknutí:

```java
/**
 * If acquireTokenSilent() returns an error that requires an interaction (MsalUiRequiredException),
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your Single Sign-On refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 **/
mSingleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Pokud se uživatel již `acquireTokenSilentAsync()` přihlásil, umožňuje aplikacím požadovat tokeny tiše, jak je znázorněno v aplikaci `initializeUI()` `callGraphApiSilentButton` , v obslužné rutině kliknutí:

```java
/**
 * Once you've signed the user in,
 * you can perform acquireTokenSilent to obtain resources without interrupting the user.
 **/
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>Načtení účtu

Kód pro načtení účtu `SingleAccountModeFragment.java` `loadAccount()`je v aplikaci .  Načítání uživatelského účtu je asynchronní operace, takže zpětná volání ke zpracování při načtení účtu, změny nebo dojde k chybě je předán a MSAL.  Následující kód také `onAccountChanged()`zpracovává , ke kterému dochází při odebrání účtu, uživatel změní na jiný účet a tak dále.

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

#### <a name="call-microsoft-graph"></a>Volání na Microsoft Graph

Když je uživatel přihlášen, volání microsoft graphu se provádí `callGraphAPI()` prostřednictvím `SingleAccountModeFragment.java`požadavku HTTP, který je definován v aplikaci . Tato funkce je obálka, která zjednodušuje ukázku tím, že `authenticationResult` provádí některé úkoly, jako je například získání přístupového tokenu z a balení volání MSGraphRequestWrapper a zobrazení výsledků volání.

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

### <a name="auth_config_single_accountjson"></a>auth_config_single_account.json

Toto je konfigurační soubor pro aplikaci MSAL, která používá jeden účet.

Vysvětlení těchto polí naleznete [v tématu Understand the Android MSAL configuration file.](msal-configuration.md)

Všimněte si `"account_mode" : "SINGLE"`přítomnosti aplikace , která nakonfiguruje tuto aplikaci tak, aby používala jeden účet.

`"client_id"`je předkonfigurován tak, aby používal registraci objektu aplikace, kterou společnost Microsoft udržuje.
`"redirect_uri"`je předkonfigurován pro použití podpisového klíče dodaný s ukázkou kódu.

```json
{
  "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
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

### <a name="multipleaccountmodefragmentjava"></a>MultipleAccountModeFragment.java

Tento soubor ukazuje, jak vytvořit více účtů MSAL aplikace a volání rozhraní MICROSOFT Graph API.

Příkladem aplikace s více účty je e-mailová aplikace, která umožňuje pracovat s více uživatelskými účty, jako je pracovní účet a osobní účet.

#### <a name="multiple-account-msal-initialization"></a>Vícenásobná inicializace MSAL účtu

V `MultipleAccountModeFragment.java` souboru `onCreateView()`je v aplikaci`IMultipleAccountPublicClientApplication`v aplikaci více účtů vytvořen `auth_config_multiple_account.json file`objekt aplikace ( ) pomocí informací o konfiguraci uložených v aplikaci :

```java
// Creates a PublicClientApplication object with res/raw/auth_config_multiple_account.json
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_multiple_account,
        new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(IMultipleAccountPublicClientApplication application) {
                mMultipleAccountApp = application;
                loadAccounts();
            }

            @Override
            public void onError(MsalException exception) {
                ...
            }
        });
```

Vytvořený `MultipleAccountPublicClientApplication` objekt je uložen v proměnné člena třídy tak, aby jej lze použít k interakci s knihovnou MSAL získat tokeny a načíst a odebrat uživatelský účet.

#### <a name="load-an-account"></a>Načtení účtu

Více aplikací účtu `getAccounts()` obvykle volání vybrat účet, který chcete použít pro operace MSAL. Kód pro načtení účtu `MultipleAccountModeFragment.java` je v `loadAccounts()`souboru v aplikaci .  Načítání uživatelského účtu je asynchronní operace. Takže zpětné volání zpracovává situace, kdy je načten účet, změny nebo dojde k chybě.

```java
/**
 * Load currently signed-in accounts, if there's any.
 **/
private void loadAccounts() {
    if (mMultipleAccountApp == null) {
        return;
    }

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

#### <a name="get-a-token-interactively-or-silently"></a>Získejte token interaktivně nebo tiše

Některé situace, kdy může být uživatel vyzván k výběru svého účtu, zadání jeho přihlašovacích údajů nebo souhlasu s oprávněními, která vaše aplikace požadovala, jsou:

* Při prvním přihlášení uživatele k aplikaci
* Pokud uživatel resetuje své heslo, bude muset zadat své přihlašovací údaje. 
* Je-li souhlas odvolán 
* Pokud vaše aplikace výslovně vyžaduje souhlas 
* Když vaše aplikace poprvé požaduje přístup k prostředku
* Pokud jsou vyžadovány vícefaktorové povolení nebo jiné zásady podmíněného přístupu

Více aplikací účtu by obvykle mělo získat tokeny interaktivně, to znamená s `acquireToken()`uživatelským rozhraním, které zahrnuje uživatele, s voláním .  Kód pro interaktivní získání tokenu `MultipleAccountModeFragment.java` je `initializeUI()`v souboru v , v obslužné rutině `callGraphApiInteractiveButton` kliknutí:

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
 **/
mMultipleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Aplikace by neměly vyžadovat, aby se uživatel přihlašuje pokaždé, když požádá o token. Pokud se uživatel již `acquireTokenSilentAsync()` přihlásil, umožňuje aplikacím požadovat tokeny bez `MultipleAccountModeFragment.java` zobrazení výzvy uživateli, jak je znázorněno v souboru, v`initializeUI()` obslužné rutině `callGraphApiSilentButton` kliknutí:

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

#### <a name="remove-an-account"></a>Odebrání účtu

Kód pro odebrání účtu a všechny tokeny uložené v mezipaměti pro účet je v souboru `MultipleAccountModeFragment.java` v `initializeUI()` obslužné rutině pro tlačítko odebrat účet. Před odebráním účtu potřebujete objekt účtu, který získáte z metod `getAccounts()` MSAL, jako je a `acquireToken()`. Vzhledem k tomu, že odebrání účtu `onRemoved` je asynchronní operace, zpětné volání je dodáván o aktualizaci ui.

```java
/**
 * Removes the selected account and cached tokens from this app (or device, if the device is in shared mode).
 **/
mMultipleAccountApp.removeAccount(accountList.get(accountListSpinner.getSelectedItemPosition()),
        new IMultipleAccountPublicClientApplication.RemoveAccountCallback() {
            @Override
            public void onRemoved() {
                ...
                /* Reload account asynchronously to get the up-to-date list. */
                loadAccounts();
            }

            @Override
            public void onError(@NonNull MsalException exception) {
                displayError(exception);
            }
        });
```

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account.json

Toto je konfigurační soubor pro aplikaci MSAL, která používá více účtů.

Vysvětlení různých polí naleznete [v tématu Understand the Android MSAL configuration file.](msal-configuration.md)

Na rozdíl od konfiguračního souboru `"account_mode" : "MULTIPLE"` [auth_config_single_account.json](#auth_config_single_accountjson) má tento konfigurační soubor místo toho, `"account_mode" : "SINGLE"` protože se jedná o aplikaci s více účtůmi.

`"client_id"`je předkonfigurován tak, aby používal registraci objektu aplikace, kterou společnost Microsoft udržuje.
`"redirect_uri"`je předkonfigurován pro použití podpisového klíče dodaný s ukázkou kódu.

```json
{
  "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
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

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Zjistěte, jak vytvořit aplikaci použitou v tomto rychlém startu

Vyzkoušejte [přihlašovací uživatele a zavolejte Microsoft Graph z](tutorial-v2-android.md) kurzu aplikace pro Android pro podrobný návod pro vytváření aplikace pro Android, která získá přístupový token a používá ho k volání rozhraní Microsoft Graph API.

> [!div class="nextstepaction"]
> [Kurz volání Graph API pro Android](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>Wiki knihovny MSAL pro Android

Přečtěte si další informace o knihovně MSAL pro Android:

> [!div class="nextstepaction"]
> [Wiki knihovny MSAL pro Android](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
