---
title: REST API výměny deklarací identity jako ověřování
titleSuffix: Azure AD B2C
description: Návod pro vytvoření Azure AD B2C cesty uživatele, která komunikuje s RESTful službami.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 761bc4db7760ef5e84e3fc3c8a5deea5d4508f51
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94951923"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-to-validate-user-input"></a>Návod: integrace REST APIch výměn deklarací identity v cestě uživatele Azure AD B2C k ověření vstupu uživatele

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Rozhraní identity Experience Framework (IEF), které je podkód Azure Active Directory B2C (Azure AD B2C), umožňuje vývojářům identity integrovat interakci s rozhraním API RESTful v cestě uživatele.  Na konci tohoto návodu budete moct vytvořit Azure AD B2C cestu uživatele, která komunikuje se službou [RESTful Services](custom-policy-rest-api-intro.md) za účelem ověření vstupu uživatele.

V tomto scénáři přidáme uživatelům možnost zadat věrnostní číslo do Azure AD B2C přihlašovací stránky. Ověříme, jestli je tato kombinace e-mailu a věrnostního čísla namapovaná na propagační kód odesláním těchto dat do REST API. Pokud REST API nalezne propagační kód pro tohoto uživatele, bude vrácen do Azure AD B2C. Nakonec se kód pro propagační akce vloží do deklarací tokenů, které má aplikace spotřebovat.

Můžete také navrhnout interakci jako krok orchestrace. Tato akce je vhodná, když REST API neověřuje data na obrazovce a vždy vrací deklarace identity. Další informace najdete v tématu [Návod: integrace REST APIch výměn deklarací identity v cestě uživatele Azure AD B2C jako krok orchestrace](custom-policy-rest-api-claims-exchange.md).

## <a name="prerequisites"></a>Požadavky

- Proveďte kroky v části Začínáme [s vlastními zásadami](custom-policy-get-started.md). Měli byste mít pracovní vlastní zásady pro registraci a přihlašování pomocí místních účtů.
- Naučte se [integrovat REST API výměn deklarací identity do vlastních zásad Azure AD B2C](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>Příprava REST APIho koncového bodu

V tomto návodu byste měli mít REST API, který ověří, jestli je e-mailová adresa registrovaná v back-endovém systému s věrnostním ID. Pokud je zaregistrováno, REST API by měl vrátit kód pro povýšení registrace, který zákazník může použít k nákupu zboží v rámci vaší aplikace. V opačném případě by REST API měla vrátit chybovou zprávu HTTP 409: "věrnostní ID {věrnostního ID} ' není přidruženo k e-mailové adrese {email}.

Následující kód JSON znázorňuje data Azure AD B2C odešle do vašeho koncového bodu REST API. 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

Jakmile REST API ověří data, musí vrátit HTTP 200 (ok) s následujícími daty JSON:

```json
{
    "promoCode": "24534"
}
```

Pokud se ověření nepovedlo, REST API musí vrátit HTTP 409 (konflikt) s `userMessage` elementem JSON. IEF očekává `userMessage` deklaraci identity, kterou REST API vrací. Pokud se ověření nezdaří, zobrazí se tato deklarace jako řetězec pro uživatele.

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
}
```

Nastavení koncového bodu REST API je mimo rámec tohoto článku. Vytvořili jsme ukázku [Azure Functions](../azure-functions/functions-reference.md) . K úplnému kódu funkce Azure můžete přistupovat na [GitHubu](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function).

## <a name="define-claims"></a>Definovat deklarace identity

Deklarace identity poskytuje dočasné úložiště dat během provádění zásad Azure AD B2C. Deklarace identity můžete deklarovat v části [schéma deklarací](claimsschema.md) . 

1. Otevřete soubor rozšíření vaší zásady. Například <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Vyhledejte element [BuildingBlocks](buildingblocks.md) . Pokud element neexistuje, přidejte jej.
1. Vyhledejte element [ClaimsSchema](claimsschema.md) . Pokud element neexistuje, přidejte jej.
1. Do prvku **ClaimsSchema** přidejte následující deklarace identity.  

```xml
<ClaimType Id="loyaltyId">
  <DisplayName>Your loyalty ID</DisplayName>
  <DataType>string</DataType>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
<ClaimType Id="promoCode">
  <DisplayName>Your promo code</DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
</ClaimType>
  <ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>Přidat technický profil rozhraní RESTful API 

[Technický profil RESTful](restful-technical-profile.md) poskytuje podporu pro propojení s vlastní službou RESTful. Azure AD B2C odesílá data do služby RESTful v `InputClaims` kolekci a přijímá data zpátky v `OutputClaims` kolekci. Vyhledejte element **ClaimsProviders** a přidejte nového zprostředkovatele deklarací identity následujícím způsobem:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Check loyaltyId Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/ValidateProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="loyaltyId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

V tomto příkladu se do `userLanguage` služby REST pošle jako `lang` v datové části JSON. Hodnota `userLanguage` deklarace identity obsahuje ID jazyka aktuálního uživatele. Další informace najdete v tématu [překladač deklarací identity](claim-resolver-overview.md).

### <a name="configure-the-restful-api-technical-profile"></a>Konfigurace technického profilu rozhraní RESTful API 

Po nasazení REST API nastavte metadata `REST-ValidateProfile` technického profilu tak, aby odrážela vaše vlastní REST API, včetně:

- **ServiceUrl**. Nastavte adresu URL koncového bodu REST API.
- **SendClaimsIn**. Určete, jakým způsobem se vstupní deklarace identity odesílají do zprostředkovatele deklarací RESTful.
- **AuthenticationType**. Nastavte typ ověřování prováděného zprostředkovatelem deklarací RESTful. 
- **AllowInsecureAuthInProduction**. V produkčním prostředí nezapomeňte nastavit tato metadata na `true`
    
Další konfigurace najdete v článku [metadata RESTful Technical Profile](restful-technical-profile.md#metadata) .

Výše uvedené komentáře `AuthenticationType` a `AllowInsecureAuthInProduction` Určete změny, které byste měli dělat při přesunu do produkčního prostředí. Informace o tom, jak zabezpečit rozhraní API RESTful pro produkční prostředí, najdete v tématu [Secure RESTFUL API](secure-rest-api.md).

## <a name="validate-the-user-input"></a>Ověření vstupu uživatele

Pokud chcete získat věrnostní číslo uživatele během registrace, musíte uživateli dovolit, aby na obrazovce zadal tato data. Přidejte výstupní deklaraci identity **loyaltyId** do přihlašovací stránky tak, že ji přidáte do stávajícího elementu oddílu registračního technického profilu `OutputClaims` . Zadejte celý seznam výstupních deklarací identity, abyste mohli řídit pořadí, na kterém jsou deklarace identity zobrazené na obrazovce.  

Přidejte odkaz na technický profil ověření do technického profilu registrace, který volá `REST-ValidateProfile` . Nový technický profil ověření se přidá na začátek `<ValidationTechnicalProfiles>` kolekce definované v základních zásadách. Toto chování znamená, že až po úspěšném ověření dojde k vytvoření účtu v adresáři, Azure AD B2C se přesune k.   

1. Vyhledejte element **ClaimsProviders** . Přidejte nového zprostředkovatele deklarací identity následujícím způsobem:

    ```xml
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surName"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Self Asserted</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SelfAsserted-Social">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" />
          </InputClaims>
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surname"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile"/>
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="include-a-claim-in-the-token"></a>Zahrnutí deklarace identity do tokenu 

Pokud chcete vrátit deklaraci propagačního kódu zpátky do aplikace předávající strany, přidejte do souboru výstupní deklaraci identity <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> . Výstupní deklarace identity umožní přidání deklarace do tokenu po úspěšné cestě uživatele a pošle se do aplikace. Upravte element Technical Profile v části předávající strany a přidejte tak `promoCode` jako výstupní deklaraci identity.
 
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
      <OutputClaim ClaimTypeReferenceId="promoCode" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Testování vlastních zásad

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD, a to tak, že v horní nabídce vyberete adresář a filtr **předplatného** a zvolíte adresář, který obsahuje vašeho TENANTA Azure AD.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Registrace aplikací**.
1. Vyberte **architekturu prostředí identity**.
1. Vyberte **Odeslat vlastní zásadu** a pak nahrajte soubory zásad, které jste změnili: *TrustFrameworkExtensions.xml* a *SignUpOrSignin.xml*. 
1. Vyberte zásadu registrace nebo přihlašování, kterou jste nahráli, a klikněte na tlačítko **Spustit** .
1. Měli byste být schopni se zaregistrovat pomocí e-mailové adresy.
1. Klikněte na odkaz **registrace nyní** .
1. Do pole **věrnostní ID** zadejte 1234 a klikněte na **pokračovat**. V tomto okamžiku byste měli získat chybovou zprávu ověření.
1. Přejděte na jinou hodnotu a klikněte na **pokračovat**.
1. Token, který se odesílá zpátky do vaší aplikace, zahrnuje `promoCode` deklaraci identity.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584295703,
  "nbf": 1584292103,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584292103,
  "auth_time": 1584292103,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "promoCode": "84362"
  ...
}
```

## <a name="next-steps"></a>Další kroky

Informace o tom, jak zabezpečit rozhraní API, najdete v následujících článcích:

- [Návod: integrace REST APIch výměn deklarací identity v Azure AD B2C cestě uživatele jako kroku orchestrace](custom-policy-rest-api-claims-exchange.md)
- [Zabezpečení rozhraní API RESTful](secure-rest-api.md)
- [Referenční dokumentace: RESTful Technical Profile](restful-technical-profile.md)