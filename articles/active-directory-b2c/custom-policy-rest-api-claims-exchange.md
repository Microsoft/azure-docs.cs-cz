---
title: Renovované rozhraní API si nároky na burze – Azure Active Directory B2C
description: Přidejte renominy deklarací rozhraní REST API do vlastních zásad ve službě Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6316165ba08d055be1186995e2fe2ad5a0079fb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330725"
---
# <a name="walkthrough-add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Návod: Přidání serveru REST API pro deklarace identity do vlastních zásad ve službě Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) umožňuje vývojářům identit integrovat interakci s rozhraním RESTful API v cestě uživatele. Na konci tohoto návodu budete moci vytvořit cestu uživatele Azure AD B2C, která spolupracuje se [službami RESTful](custom-policy-rest-api-intro.md).

V tomto scénáři obohacujeme data tokenů uživatele integrací s podnikovým pracovním postupem. Během registrace nebo přihlášení pomocí místního nebo federovaného účtu Azure AD B2C vyvolá rozhraní REST API, aby získaldata rozšířeného profilu uživatele ze vzdáleného zdroje dat. V této ukázce Azure AD B2C odešle jedinečný identifikátor uživatele, objectId. Rozhraní REST API pak vrátí zůstatek na účtu uživatele (náhodné číslo). Tuto ukázku použijte jako výchozí bod pro integraci s vlastním systémem CRM, marketingovou databází nebo jakýmkoli obchodním pracovním postupem.

Můžete také navrhnout interakci jako technický profil ověření. To je vhodné, když rozhraní REST API bude ověřování dat na obrazovce a vrácení deklarací. Další informace naleznete v [tématu Návod: Integrace serveru deklarací rozhraní REST API v cestě uživatele Azure AD B2C k ověření vstupu uživatele](custom-policy-rest-api-claims-validation.md).

## <a name="prerequisites"></a>Požadavky

- Proveďte kroky v [části Začínáme s vlastními zásadami](custom-policy-get-started.md). Měli byste mít funkční vlastní zásady pro registraci a přihlášení pomocí místních účtů.
- Zjistěte, jak [integrovat renovovat deklarace identity rozhraní REST ve vlastních zásadách Azure AD B2C](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>Příprava koncového bodu rozhraní REST API

Pro tento návod byste měli mít rozhraní REST API, které ověřuje, zda je objekt Id Azure AD B2C registrované ve vašem back-endovém systému. Pokud je zaregistrováno, rozhraní REST API vrátí zůstatek uživatelského účtu. V opačném případě rozhraní REST API zaregistruje nový účet `50.00`v adresáři a vrátí počáteční zůstatek .

Následující kód JSON ilustruje data Azure AD B2C bude odesílat do koncového bodu rozhraní REST API. 

```json
{
    "objectId": "User objectId",
    "language": "Current UI language"
}
```

Jakmile rozhraní REST API ověří data, musí vrátit http 200 (Ok), s následujícími daty JSON:

```json
{
    "balance": "760.50"
}
```

Nastavení koncového bodu rozhraní REST API je mimo rozsah tohoto článku. Vytvořili jsme ukázku [funkce Azure.](https://docs.microsoft.com/azure/azure-functions/functions-reference) K kompletnímu kódu funkce Azure můžete přistupovat na [GitHubu](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function).

## <a name="define-claims"></a>Definovat deklarace identity

Deklarace poskytuje dočasné úložiště dat během provádění zásad Azure AD B2C. Deklarace identity můžete deklarovat v části [schématu deklarací](claimsschema.md) identity. 

1. Otevřete soubor přípon zásad. Například <em> `SocialAndLocalAccounts/` </em>.
1. Vyhledejte element [BuildingBlocks.](buildingblocks.md) Pokud prvek neexistuje, přidejte jej.
1. Vyhledejte [ClaimsSchema](claimsschema.md) element. Pokud prvek neexistuje, přidejte jej.
1. Přidejte následující deklarace identity do prvku **ClaimsSchema.**  

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

[Klidný technický profil](restful-technical-profile.md) poskytuje podporu pro propojení s vlastní službou RESTful. Azure AD B2C odesílá data do služby RESTful `InputClaims` v `OutputClaims` kolekci a přijímá data zpět do kolekce. Vyhledejte v <em>**`TrustFrameworkExtensions.xml`**</em> souboru element **ClaimsProviders** a přidejte nového poskytovatele deklarací identity následujícím způsobem:

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

V tomto příkladu `userLanguage` bude odeslána do `lang` služby REST jako v rámci datové části JSON. Hodnota deklarace `userLanguage` obsahuje aktuální ID uživatelského jazyka. Další informace naleznete v tématu [překládání deklarací .](claim-resolver-overview.md)

Výše uvedené `AuthenticationType` `AllowInsecureAuthInProduction` komentáře a určit změny, které byste měli provést při přechodu do produkčního prostředí. Informace o tom, jak zabezpečit rozhraní API RESTful pro produkční prostředí, naleznete [v tématu Secure RESTful API](secure-rest-api.md).

## <a name="add-an-orchestration-step"></a>Přidání kroku orchestrace

[Cesty uživatele](userjourneys.md) určují explicitní cesty, jejichž prostřednictvím zásada umožňuje aplikaci předávající strany získat požadované deklarace identity pro uživatele. Cesta uživatele je reprezentována jako sekvence orchestrace, která musí být dodržena pro úspěšnou transakci. Můžete přidat nebo odečíst kroky orchestrace. V takovém případě přidáte nový krok orchestrace, který se používá k rozšíření informací poskytnutých do aplikace po registraci uživatele nebo přihlášení prostřednictvím volání rozhraní REST API.

1. Otevřete základní soubor zásad. Například <em> `SocialAndLocalAccounts/` </em>.
1. Vyhledejte `<UserJourneys>` prvek. Zkopírujte celý prvek a odstraňte jej.
1. Otevřete soubor přípon zásad. Například <em> `SocialAndLocalAccounts/` </em>.
1. Po `<UserJourneys>` uzavření `<ClaimsProviders>` prvku vložte do souboru přípony.
1. Vyhledejte `<UserJourney Id="SignUpOrSignIn">`a přidejte následující krok orchestrace před poslední krok.

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. Refaktorovat poslední krok orchestrace `Order` `8`změnou na . Poslední dva kroky orchestrace by měly vypadat takto:

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. Opakujte poslední dva kroky pro cesty uživatele **ProfileEdit** a **PasswordReset.**


## <a name="include-a-claim-in-the-token"></a>Zahrnout deklaraci do tokenu 

Chcete-li `balance` vrátit deklaraci zpět do aplikace předávající <em> `SocialAndLocalAccounts/` </em> strany, přidejte výstupní deklaraci do souboru. Přidání výstupní deklarace vystaví deklaraci do tokenu po úspěšné cestě uživatele a bude odesláno do aplikace. Upravte prvek technického profilu v části `balance` předávající strany a přidejte jej jako výstupní deklaraci.
 
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

Tento krok opakujte pro cesty uživatele **ProfileEdit.xml**a **PasswordReset.xml.**

Uložte změněné soubory: *TrustFrameworkBase.xml*a *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*a *PasswordReset.xml*. 

## <a name="test-the-custom-policy"></a>Testování vlastních zásad

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD výběrem filtru **directory + předplatné** v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta Azure AD.
1. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak **vyhledejte**a vyberte registrace aplikací .
1. Vyberte **rozhraní Identity Experience Framework**.
1. Vyberte **Nahrát vlastní zásady**a potom nahrajte změněné soubory zásad: *TrustFrameworkBase.xml*a *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*a *PasswordReset.xml*. 
1. Vyberte zásadu registrace nebo přihlášení, kterou jste nahráli, a klikněte na tlačítko **Spustit.**
1. Měli byste být schopni se zaregistrovat pomocí e-mailové adresy nebo účtu na Facebooku.
1. Token odeslaný zpět do `balance` vaší aplikace obsahuje deklaraci.

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


## <a name="next-steps"></a>Další kroky

Informace o zabezpečení prostředí API naleznete v následujících článcích:

- [Návod: Integrace renomovaných deklarací rozhraní REST api v cestě uživatele Azure AD B2C jako krok orchestrace](custom-policy-rest-api-claims-exchange.md)
- [Zabezpečte své RESTful API](secure-rest-api.md)
- [Reference: Technický profil RESTful](restful-technical-profile.md)
