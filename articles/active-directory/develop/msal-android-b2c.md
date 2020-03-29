---
title: Azure AD B2C (MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o konkrétních aspektech při používání Azure AD B2C s Knihovnou ověřování Microsoftu pro Android (MSAL.Learn about specific a considerations when using Azure AD B2C with the Microsoft Authentication Library for Android (MSAL. Android)
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
ms.openlocfilehash: 0998bb04b0dfc69db4696f2e390cfe259eba6718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696517"
---
# <a name="use-msal-for-android-with-b2c"></a>Použití MSAL pro Android s B2C

Microsoft Authentication Library (MSAL) umožňuje vývojářům aplikací ověřovat uživatele pomocí sociálních a místních identit pomocí [Azure Active Directory B2C (Azure AD B2C).](https://docs.microsoft.com/azure/active-directory-b2c/) Azure AD B2C je služba správy identit. Slouží k přizpůsobení a řízení způsobu, jakým se zákazníci přihlašují, přihlašují a spravují své profily při používání vašich aplikací.

## <a name="configure-known-authorities-and-redirect-uri"></a>Konfigurace známých autorit a přesměrování identifikátoru URI

V MSAL pro Android zásady B2C (cesty uživatelů) jsou konfigurovány jako jednotlivé autority.

Vzhledem k tomu, B2C aplikace, která má dvě zásady:
- Registrace / přihlášení
    * Nazývá`B2C_1_SISOPolicy`
- Upravit profil
    * Nazývá`B2C_1_EditProfile`

Konfigurační soubor pro `authorities`aplikaci by deklaroval dva . Jeden pro každou pojistku. Vlastnostkaždého `type` orgánu je `B2C`.

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

Musí `redirect_uri` být registrovány v konfiguraci `AndroidManifest.xml` aplikace a také v pro podporu přesměrování během [toku udělení autorizačního kódu](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-oauth-code).

## <a name="initialize-ipublicclientapplication"></a>Inicializovat aplikaci IPublicClientApplication

`IPublicClientApplication`je konstruován metodou výroby, aby bylo možné asynchronně analyzovat konfiguraci aplikace.

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

## <a name="interactively-acquire-a-token"></a>Interaktivní získání tokenu

Chcete-li získat token interaktivně s `AcquireTokenParameters` MSAL, vytvořte instanci a dodávat ji k metodě. `acquireToken` Níže uvedený požadavek `default` tokenu používá autoritu.

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

## <a name="silently-renew-a-token"></a>Tiché obnovení tokenu

Chcete-li získat token tiše s MSAL, `AcquireTokenSilentParameters` vytvořte `acquireTokenSilentAsync` instanci a dodávat ji k metodě. Na `acquireToken` rozdíl od `authority` metody musí být zadán získat token tiše.

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

## <a name="specify-a-policy"></a>Určení zásady

Vzhledem k tomu, že zásady v B2C jsou reprezentovány jako samostatné `fromAuthority` autority, `acquireToken` vyvolání jiné zásady než default je dosaženo zadáním klauzule při vytváření nebo `acquireTokenSilent` parametry.  Například:

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(...) // provide callback here
    .fromAuthority("<url_of_policy_defined_in_configuration_json>")
    .build();
```

## <a name="handle-password-change-policies"></a>Zpracování zásad změny hesla

Při registraci místního účtu nebo toku přihlašovacího uživatele se zobrazí '**Zapomenuté heslo?**' Odkaz. Kliknutím na tento odkaz automaticky nespustíte tok uživatelů pro obnovení hesla.

Místo toho se `AADB2C90118` kód chyby vrátí do vaší aplikace. Vaše aplikace by měla tento kód chyby zpracovat spuštěním konkrétního toku uživatele, který resetuje heslo.

Chcete-li zachytit kód chyby pro obnovení hesla, lze ve vašem `AuthenticationCallback`:

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

## <a name="use-iauthenticationresult"></a>Použít iAuthenticationResult

Úspěšné získání tokenu `IAuthenticationResult` má za následek objekt. Obsahuje přístupový token, deklarace identity uživatelů a metadata.

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

### <a name="get-the-authorized-account"></a>Získání autorizovaného účtu

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

### <a name="idtoken-claims"></a>Deklarace identity IdToken

Deklarace vrácené v Tokenii IdToken jsou naplněny službou tokenů zabezpečení (STS), nikoli msal. V závislosti na použitém zprostředkovateli identity (IdP) mohou některé deklarace chybět. Některé idps v současné době `preferred_username` neposkytují nárok. Vzhledem k tomu, že tato deklarace používá MSAL pro ukládání do mezipaměti, zástupná hodnota , `MISSING FROM THE TOKEN RESPONSE`se používá na jeho místě. Další informace o deklaracích Identity IdToken B2C najdete [v tématu Přehled tokenů ve službě Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#claims).

## <a name="managing-accounts-and-policies"></a>Správa účtů a zásad

B2C zachází s každou zásadou jako s samostatným orgánem. Proto přístupové tokeny, tokeny aktualizace a tokeny ID vrácené z každé zásady nejsou zaměnitelné. To znamená, že `IAccount` každá zásada vrátí samostatný objekt, jehož tokeny nelze použít k vyvolání jiných zásad.

Každá zásada `IAccount` přidá do mezipaměti pro každého uživatele. Pokud se uživatel přihlásí k aplikaci a vyvolá dvě `IAccount`zásady, bude mít dvě s. Chcete-li odebrat tohoto uživatele `removeAccount()` z mezipaměti, musíte volat pro každou zásadu.

Při obnovení tokeny pro zásady s `acquireTokenSilent` `IAccount` , zadejte stejné, které bylo `AcquireTokenSilentParameters`vráceno z předchozích vyvolání zásady . Poskytnutí účtu vráceného jinou zásadou bude mít za následek chybu.

## <a name="next-steps"></a>Další kroky

Další informace o Azure Active Directory B2C (Azure AD B2C) na [webu Co je Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
