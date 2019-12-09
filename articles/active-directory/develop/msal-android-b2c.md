---
title: Azure AD B2C (MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o konkrétních otázkách při použití Azure AD B2C s knihovnou Microsoft Authentication Library pro Android (MSAL. Svém
services: active-directory
author: brianmel
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 9/18/2019
ms.author: brianmel
ms.reviewer: rapong
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98a31ea2daffba19242e73362af5a44e3a392342
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917110"
---
# <a name="use-msal-for-android-with-b2c"></a>Použití MSAL pro Android s B2C

Knihovna Microsoft Authentication Library (MSAL) umožňuje vývojářům aplikací ověřovat uživatele pomocí sociálních a místních identit pomocí [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). Azure AD B2C je služba správy identit. Použijte ho k přizpůsobení a řízení, jak se zákazníci při používání vašich aplikací přihlásí, přihlásí a spravují své profily.

## <a name="configure-known-authorities-and-redirect-uri"></a>Konfigurace známých autorit a identifikátor URI pro přesměrování

V MSAL pro Android jsou zásady B2C (uživatelské cesty) nakonfigurované jako samostatné autority.

Daná aplikace B2C má dvě zásady:
- Registrace a přihlášení
    * Nazývá se `B2C_1_SISOPolicy`
- Upravit profil
    * Nazývá se `B2C_1_EditProfile`

Konfigurační soubor pro aplikaci deklaruje dvě `authorities`. Jednu pro každou zásadu. Vlastnost `type` jednotlivých autorit je `B2C`.

### `app/src/main/res/raw/msal_config.json`
```json
{
    "client_id": "<your_client_id_here>",
    "redirect_uri": "<your_redirect_uri_here>",
    "authorities": [{
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

`redirect_uri` musí být registrována v konfiguraci aplikace a také v `AndroidManifest.xml` k podpoře přesměrování během [toku udělení autorizačního kódu](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-oauth-code).

## <a name="initialize-ipublicclientapplication"></a>Inicializovat IPublicClientApplication

`IPublicClientApplication` je vytvořen metodou továrního nastavení, aby bylo možné konfiguraci aplikace analyzovat asynchronně.

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

Chcete-li získat token interaktivně pomocí MSAL, sestavte instanci `AcquireTokenParameters` a poskytněte ji do metody `acquireToken`. Níže uvedený požadavek na token používá `default` autorita.

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

K tiché získání tokenu pomocí MSAL vytvořte instanci `AcquireTokenSilentParameters` a poskytněte ji do metody `acquireTokenSilentAsync`. Na rozdíl od metody `acquireToken` musí být `authority` určena k tiché získání tokenu.

```java
IMultilpeAccountPublicClientApplication pca = ...; // Initialization not shown
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
            // Token request was unsuccesful, inspect the exception
        }
    })
    .build();

pca.acquireTokenSilentAsync(parameters);
```

## <a name="specify-a-policy"></a>Zadat zásadu

Vzhledem k tomu, že zásady v B2C jsou reprezentovány jako samostatné autority, je při sestavování `acquireToken` nebo `acquireTokenSilent` parametrů vyvoláno jiné jiné než výchozí zásady, a to zadáním klauzule `fromAuthority`.  Například:

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

Tok uživatele registrace nebo přihlašování k místnímu účtu zobrazuje**zapomenuté heslo?** odkaz. Kliknutím na tento odkaz se automaticky neaktivuje tok uživatele resetování hesla.

Místo toho se do vaší aplikace vrátí kód chyby `AADB2C90118`. Vaše aplikace by měla zpracovat tento kód chyby spuštěním konkrétního toku uživatele, který resetuje heslo.

Pokud chcete zachytit kód chyby resetování hesla, můžete v `AuthenticationCallback`použít tuto implementaci:

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

Výsledkem úspěchu získání tokenu je objekt `IAuthenticationResult`. Obsahuje přístupový token, deklarace identity uživatelů a metadata.

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

Deklarace identity vrácené v IdToken jsou vyplněné pomocí služby tokenů zabezpečení (STS), nikoli pomocí MSAL. V závislosti na použitém zprostředkovateli identity (IdP) mohou chybět některé deklarace identity. Některé zprostředkovatelů identity aktuálně neposkytují deklaraci `preferred_username`. Vzhledem k tomu, že je tato deklarace identity používána MSAL pro ukládání do mezipaměti, se na svém místě používá zástupná hodnota `MISSING FROM THE TOKEN RESPONSE`. Další informace o deklaracích B2C IdToken najdete v tématu [Přehled tokenů v Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#claims).

## <a name="managing-accounts-and-policies"></a>Správa účtů a zásad

B2C považuje každou zásadu za samostatnou autoritu. Přístupové tokeny, aktualizační tokeny a tokeny ID vrácené z jednotlivých zásad proto nejsou zaměnitelné. To znamená, že každá zásada vrátí samostatný objekt `IAccount`, jehož tokeny nelze použít k vyvolání jiných zásad.

Každá zásada přidá `IAccount` do mezipaměti pro každého uživatele. Pokud se uživatel přihlásí k aplikaci a vyvolá dvě zásady, budou mít dvě `IAccount`. Chcete-li tohoto uživatele odebrat z mezipaměti, je nutné volat `removeAccount()` pro každou zásadu.

Při obnovování tokenů pro zásadu s `acquireTokenSilent`zadejte stejný `IAccount`, který byl vrácen z předchozích vyvolání zásady do `AcquireTokenSilentParameters`. Poskytnutí účtu vráceného jinou zásadou způsobí chybu.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o Azure Active Directory B2C (Azure AD B2C) na adrese [Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
