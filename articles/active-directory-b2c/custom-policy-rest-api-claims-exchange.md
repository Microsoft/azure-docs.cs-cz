---
title: REST API výměn deklarací identity – Azure Active Directory B2C
description: Přidejte REST API výměny deklarací identity do vlastních zásad v Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/18/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e22a6028f5b7fa8cf81ddf0e3e2a550859aad0ac
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91259590"
---
# <a name="walkthrough-add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Návod: Přidání výměn deklarací identity REST API do vlastních zásad v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) umožňuje vývojářům identity integrovat interakci s rozhraním API RESTful v cestě uživatele. Na konci tohoto návodu budete moct vytvořit Azure AD B2C cestu uživatele, která komunikuje s [RESTful službami](custom-policy-rest-api-intro.md).

V tomto scénáři rozšiřujeme data tokenů uživatele integrací s podnikovým pracovním postupem. Při registraci nebo přihlašování pomocí místního nebo federovaného účtu Azure AD B2C vyvolá REST API, aby se data rozšířeného profilu uživatele získala ze vzdáleného zdroje dat. V této ukázce Azure AD B2C odesílá jedinečný identifikátor uživatele, objectId. REST API pak vrátí zůstatek účtu uživatele (náhodné číslo). Tuto ukázku použijte jako výchozí bod pro integraci s vlastním systémem CRM, marketingovou databází nebo jakýmkoli obchodním pracovním postupem.

Interakci můžete také navrhnout jako technický profil ověřování. To je vhodné, když REST API bude ověřovat data na obrazovce a vracet deklarace identity. Další informace najdete v tématu [Návod: integrace REST APIch výměn deklarací identity v cestě uživatele Azure AD B2C k ověření vstupu uživatele](custom-policy-rest-api-claims-validation.md).

## <a name="prerequisites"></a>Požadavky

- Proveďte kroky v části Začínáme [s vlastními zásadami](custom-policy-get-started.md). Měli byste mít pracovní vlastní zásady pro registraci a přihlašování pomocí místních účtů.
- Naučte se [integrovat REST API výměn deklarací identity do vlastních zásad Azure AD B2C](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>Příprava REST APIho koncového bodu

Pro tento návod byste měli mít REST API, který ověří, jestli je Azure AD B2C objectId uživatele v systému back-end zaregistrovaný. Pokud je zaregistrováno, REST API vrátí zůstatek uživatelského účtu. V opačném případě REST API zaregistruje nový účet v adresáři a vrátí počáteční zůstatek `50.00` .

Následující kód JSON znázorňuje data Azure AD B2C odešle do vašeho koncového bodu REST API. 

```json
{
    "objectId": "User objectId",
    "lang": "Current UI language"
}
```

Jakmile REST API ověří data, musí vrátit HTTP 200 (ok) s následujícími daty JSON:

```json
{
    "balance": "760.50"
}
```

Nastavení koncového bodu REST API je mimo rámec tohoto článku. Vytvořili jsme ukázku [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) . K úplnému kódu funkce Azure můžete přistupovat na [GitHubu](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function).

## <a name="define-claims"></a>Definovat deklarace identity

Deklarace identity poskytuje dočasné úložiště dat během provádění zásad Azure AD B2C. Deklarace identity můžete deklarovat v části [schéma deklarací](claimsschema.md) . 

1. Otevřete soubor rozšíření vaší zásady. Například <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Vyhledejte element [BuildingBlocks](buildingblocks.md) . Pokud element neexistuje, přidejte jej.
1. Vyhledejte element [ClaimsSchema](claimsschema.md) . Pokud element neexistuje, přidejte jej.
1. Do prvku **ClaimsSchema** přidejte následující deklarace identity.  

```xml
<ClaimType Id="balance">
  <DisplayName>Your Balance</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="configure-the-restful-api-technical-profile"></a>Konfigurace technického profilu rozhraní RESTful API 

[Technický profil RESTful](restful-technical-profile.md) poskytuje podporu pro propojení s vlastní službou RESTful. Azure AD B2C odesílá data do služby RESTful v `InputClaims` kolekci a přijímá data zpátky v `OutputClaims` kolekci. V souboru vyhledejte element **ClaimsProviders** <em>**`TrustFrameworkExtensions.xml`**</em> a přidejte nového zprostředkovatele deklarací identity následujícím způsobem:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="balance" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

V tomto příkladu se do `userLanguage` služby REST pošle jako `lang` v datové části JSON. Hodnota `userLanguage` deklarace identity obsahuje ID jazyka aktuálního uživatele. Další informace najdete v tématu [překladač deklarací identity](claim-resolver-overview.md).

Výše uvedené komentáře `AuthenticationType` a `AllowInsecureAuthInProduction` Určete změny, které byste měli dělat při přesunu do produkčního prostředí. Informace o tom, jak zabezpečit rozhraní API RESTful pro produkční prostředí, najdete v tématu [Secure RESTFUL API](secure-rest-api.md).

## <a name="add-an-orchestration-step"></a>Přidat krok orchestrace

[Cesty uživatelů](userjourneys.md) určují explicitní cesty, pomocí kterých zásada umožňuje aplikaci předávající strany získat požadované deklarace identity pro uživatele. Cesta uživatele je reprezentována jako sekvence orchestrace, která musí následovat po úspěšné transakci. Můžete přidat nebo odečíst kroky orchestrace. V tomto případě přidáte nový krok orchestrace, který se použije k rozšíření informací poskytnutých aplikaci po registraci nebo přihlášení uživatele prostřednictvím volání REST API.

1. Otevřete základní soubor zásad. Například <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em> .
1. Vyhledejte `<UserJourneys>` element. Zkopírujte celý element a pak ho odstraňte.
1. Otevřete soubor rozšíření vaší zásady. Například <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Vložte `<UserJourneys>` do souboru rozšíření za zavřením `<ClaimsProviders>` elementu.
1. Vyhledejte `<UserJourney Id="SignUpOrSignIn">` a přidejte následující krok orchestrace před poslední.

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. Refaktorujte poslední krok orchestrace změnou na `Order` `8` . Vaše poslední kroky orchestrace by měly vypadat takto:

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. Zopakujte poslední dva kroky pro cesty uživatele **ProfileEdit** a **PasswordReset** .


## <a name="include-a-claim-in-the-token"></a>Zahrnutí deklarace identity do tokenu 

Pokud chcete vrátit `balance` deklaraci identity zpátky do aplikace předávající strany, přidejte do souboru výstupní deklaraci identity <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> . Přidáním výstupní deklarace identity se tato deklarace vydá do tokenu po úspěšné cestě uživatele a pošle se do aplikace. Upravte prvek Technical Profile v části předávající strany a přidejte `balance` jako výstupní deklaraci identity.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="balance" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

Tento krok opakujte pro **ProfileEdit.xml**a **PasswordReset.xml** cestu k uživateli.

Soubory, které jste změnili: *TrustFrameworkBase.xml*a *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*a *PasswordReset.xml*, uložte. 

## <a name="test-the-custom-policy"></a>Testování vlastních zásad

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD, a to tak, že v horní nabídce vyberete adresář a filtr **předplatného** a zvolíte adresář, který obsahuje vašeho TENANTA Azure AD.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Registrace aplikací**.
1. Vyberte **architekturu prostředí identity**.
1. Vyberte **Odeslat vlastní zásadu**a pak nahrajte soubory zásad, které jste změnili: *TrustFrameworkBase.xml*a *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*a *PasswordReset.xml*. 
1. Vyberte zásadu registrace nebo přihlašování, kterou jste nahráli, a klikněte na tlačítko **Spustit** .
1. Měli byste být schopni se zaregistrovat pomocí e-mailové adresy nebo účtu Facebook.
1. Token, který se odesílá zpátky do vaší aplikace, zahrnuje `balance` deklaraci identity.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584961516,
  "nbf": 1584957916,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584957916,
  "auth_time": 1584957916,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "balance": "202.75"
  ...
}
```

## <a name="next-steps"></a>Další kroky

Informace o tom, jak zabezpečit rozhraní API, najdete v následujících článcích:

- [Návod: integrace REST APIch výměn deklarací identity v Azure AD B2C cestě uživatele jako kroku orchestrace](custom-policy-rest-api-claims-exchange.md)
- [Zabezpečení rozhraní API RESTful](secure-rest-api.md)
- [Referenční dokumentace: RESTful Technical Profile](restful-technical-profile.md)
