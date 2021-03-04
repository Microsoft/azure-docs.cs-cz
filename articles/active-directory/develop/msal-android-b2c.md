---
title: Azure AD B2C (MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o konkrétních otázkách při použití Azure AD B2C s knihovnou Microsoft Authentication Library pro Android (MSAL. Svém
services: active-directory
author: iambmelt
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 9/18/2019
ms.author: brianmel
ms.reviewer: rapong
ms.custom: aaddev
ms.openlocfilehash: 1a9b9481d0b4086505bbfd3c2cd654ce228d1ae2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101688871"
---
# <a name="use-msal-for-android-with-b2c"></a>Použití MSAL pro Android s B2C

Knihovna Microsoft Authentication Library (MSAL) umožňuje vývojářům aplikací ověřovat uživatele pomocí sociálních a místních identit pomocí [Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/index.yml). Azure AD B2C je služba správy identit. Použijte ho k přizpůsobení a řízení, jak se zákazníci při používání vašich aplikací přihlásí, přihlásí a spravují své profily.

## <a name="choosing-a-compatible-authorization_user_agent"></a>Výběr kompatibilního authorization_user_agent
Systém správy identit B2C podporuje ověřování s několika poskytovateli účtů sociálních sítí, jako jsou Google, Facebook, Twitter a Amazon. Pokud máte v úmyslu podporovat takové typy účtů v aplikaci, doporučuje se nakonfigurovat MSAL veřejné klientské aplikace tak, aby při určování manifestu používaly buď hodnotu nebo, a to `DEFAULT` `BROWSER` [`authorization_user_agent`](msal-configuration.md#authorization_user_agent) kvůli omezením, které zakazují použití ověřování založeného na WebView u některých externích zprostředkovatelů identity.

## <a name="configure-known-authorities-and-redirect-uri"></a>Konfigurace známých autorit a identifikátor URI pro přesměrování

V MSAL pro Android jsou zásady B2C (uživatelské cesty) nakonfigurované jako samostatné autority.

Daná aplikace B2C má dvě zásady:
- Registrace a přihlášení
    * Znamená `B2C_1_SISOPolicy`
- Upravit profil
    * Znamená `B2C_1_EditProfile`

Konfigurační soubor aplikace by byl deklarován dvakrát `authorities` . Jednu pro každou zásadu. `type`Vlastnost každé autority je `B2C` .

>Poznámka: `account_mode` musí být nastavená na **násobek** pro aplikace B2C. Další informace o [veřejných klientských aplikacích s více účty](./single-multi-account.md#multiple-account-public-client-application)najdete v dokumentaci.

### `app/src/main/res/raw/msal_config.json`

```json
{
  "client_id": "<your_client_id_here>",
  "redirect_uri": "<your_redirect_uri_here>",
  "account_mode" : "MULTIPLE",
  "authorization_user_agent" : "DEFAULT",
  "authorities": [
    {
      "type": "B2C",
      "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/",
      "default": true
    },
    {
      "type": "B2C",
      "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_EditProfile/"
    }
  ]
}
```

`redirect_uri`Musí být registrována v konfiguraci aplikace a také v nástroji `AndroidManifest.xml` pro podporu přesměrování během [toku udělení autorizačního kódu](../../active-directory-b2c/authorization-code-flow.md).

## <a name="initialize-ipublicclientapplication"></a>Inicializovat IPublicClientApplication

`IPublicClientApplication` je vytvořena metodou továrny, která umožňuje asynchronní analýzu konfigurace aplikace.

```java
PublicClientApplication.createMultipleAccountPublicClientApplication(
    context, // Your application Context
    R.raw.msal_config, // Id of app JSON config
    new IPublicClientApplication.ApplicationCreatedListener() {
        @Override
        public void onCreated(IMultipleAccountPublicClientApplication pca) {
            // Application has been initialized.
        }

        @Override
        public void onError(MsalException exception) {
            // Application could not be created.
            // Check Exception message for details.
        }
    }
);
```

## <a name="interactively-acquire-a-token"></a>Interaktivně získat token

Chcete-li získat token interaktivně pomocí MSAL, sestavte `AcquireTokenParameters` instanci a poskytněte ji `acquireToken` metodě. Níže uvedený požadavek na token používá `default` autoritu.

```java
IMultipleAccountPublicClientApplication pca = ...; // Initialization not shown

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccessful, inspect the exception
        }

        @Override
        public void onCancel() {
            // The user cancelled the flow
        }
    }).build();

pca.acquireToken(parameters);
```

## <a name="silently-renew-a-token"></a>Bezobslužné obnovení tokenu

Chcete-li získat token tiše pomocí MSAL, sestavte `AcquireTokenSilentParameters` instanci a poskytněte ji `acquireTokenSilentAsync` metodě. Na rozdíl od `acquireToken` metody je `authority` nutné zadat pro tichou získání tokenu.

```java
IMultipleAccountPublicClientApplication pca = ...; // Initialization not shown
AcquireTokenSilentParameters parameters = new AcquireTokenSilentParameters.Builder()
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .forAccount(account)
    // Select a configured authority (policy), mandatory for silent auth requests
    .fromAuthority("https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/")
    .callback(new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccessful, inspect the exception
        }
    })
    .build();

pca.acquireTokenSilentAsync(parameters);
```

## <a name="specify-a-policy"></a>Zadat zásadu

Vzhledem k tomu, že zásady v B2C jsou reprezentovány jako samostatné autority, volání jiné jiné než výchozí je dosaženo zadáním `fromAuthority` klauzule při sestavování `acquireToken` nebo `acquireTokenSilent` parametrech.  Například:

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(...) // provide callback here
    .fromAuthority("<url_of_policy_defined_in_configuration_json>")
    .build();
```

## <a name="handle-password-change-policies"></a>Zpracování zásad změny hesel

Tok uživatele registrace nebo přihlašování k místnímu účtu zobrazuje **zapomenuté heslo?** . Kliknutím na tento odkaz se automaticky neaktivuje tok uživatele resetování hesla.

Místo toho se kód chyby `AADB2C90118` vrátí do vaší aplikace. Vaše aplikace by měla zpracovat tento kód chyby spuštěním konkrétního toku uživatele, který resetuje heslo.

Pokud chcete zachytit kód chyby resetování hesla, můžete použít tuto implementaci v rámci `AuthenticationCallback` :

```java
new AuthenticationCallback() {

    @Override
    public void onSuccess(IAuthenticationResult authenticationResult) {
        // ..
    }

    @Override
    public void onError(MsalException exception) {
        final String B2C_PASSWORD_CHANGE = "AADB2C90118";

        if (exception.getMessage().contains(B2C_PASSWORD_CHANGE)) {
            // invoke password reset flow
        }
    }

    @Override
    public void onCancel() {
        // ..
    }
}
```

## <a name="use-iauthenticationresult"></a>Použití IAuthenticationResult

Výsledkem úspěchu získání tokenu je `IAuthenticationResult` objekt. Obsahuje přístupový token, deklarace identity uživatelů a metadata.

### <a name="get-the-access-token-and-related-properties"></a>Získání přístupového tokenu a souvisejících vlastností

```java
// Get the raw bearer token
String accessToken = authenticationResult.getAccessToken();

// Get the scopes included in the access token
String[] accessTokenScopes = authenticationResult.getScope();

// Gets the access token's expiry
Date expiry = authenticationResult.getExpiresOn();

// Get the tenant for which this access token was issued
String tenantId = authenticationResult.getTenantId();
```

### <a name="get-the-authorized-account"></a>Získat autorizovaný účet

```java
// Get the account from the result
IAccount account = authenticationResult.getAccount();

// Get the id of this account - note for B2C, the policy name is a part of the id
String id = account.getId();

// Get the IdToken Claims
//
// For more information about B2C token claims, see reference documentation
// https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens
Map<String, ?> claims = account.getClaims();

// Get the 'preferred_username' claim through a convenience function
String username = account.getUsername();

// Get the tenant id (tid) claim through a convenience function
String tenantId = account.getTenantId();
```

### <a name="idtoken-claims"></a>IdToken deklarace identity

Deklarace identity vrácené v IdToken jsou vyplněné pomocí služby tokenů zabezpečení (STS), nikoli pomocí MSAL. V závislosti na použitém zprostředkovateli identity (IdP) mohou chybět některé deklarace identity. Některé zprostředkovatelů identity aktuálně neposkytují `preferred_username` deklaraci identity. Vzhledem k tomu, že je tato deklarace identity používána MSAL pro ukládání do mezipaměti, `MISSING FROM THE TOKEN RESPONSE` se na svém místě používá zástupný symbol. Další informace o deklaracích B2C IdToken najdete v tématu [Přehled tokenů v Azure Active Directory B2C](../../active-directory-b2c/tokens-overview.md#claims).

## <a name="managing-accounts-and-policies"></a>Správa účtů a zásad

B2C považuje každou zásadu za samostatnou autoritu. Přístupové tokeny, aktualizační tokeny a tokeny ID vrácené z jednotlivých zásad proto nejsou zaměnitelné. To znamená, že každá zásada vrátí samostatný `IAccount` objekt, jehož tokeny nelze použít k vyvolání jiných zásad.

Každá zásada přidá `IAccount` do mezipaměti pro každého uživatele. Pokud se uživatel přihlásí k aplikaci a vyvolá dvě zásady, budou mít dvě `IAccount` . Pokud chcete tohoto uživatele odebrat z mezipaměti, musíte zavolat `removeAccount()` za každou zásadu.

Když obnovujete tokeny pro zásadu s `acquireTokenSilent` , zadejte stejný `IAccount` , který byl vrácen z předchozích vyvolání zásady do  `AcquireTokenSilentParameters` . Poskytnutí účtu vráceného jinou zásadou způsobí chybu.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o Azure Active Directory B2C (Azure AD B2C) na adrese [Azure Active Directory B2C?](../../active-directory-b2c/overview.md)
