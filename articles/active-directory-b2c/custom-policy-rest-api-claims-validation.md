---
title: REST API deklarací exchanges jako ověření
titleSuffix: Azure AD B2C
description: Návod pro vytvoření cesty uživatele Azure AD B2C, která spolupracuje se službami RESTful.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a4902e96cd41a02953b6686b5d52d7912b27809f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330824"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-to-validate-user-input"></a>Návod: Integrace serveru REST API pro deklarace identity v cestě uživatele Azure AD B2C k ověření vstupu uživatele

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Rozhraní IEF (Identity Experience Framework), které je základem Azure Active Directory B2C (Azure AD B2C) umožňuje vývojářům identit integrovat interakci s rozhraním RESTful API v cestě uživatele.  Na konci tohoto návodu budete moci vytvořit cestu uživatele Azure AD B2C, která spolupracuje se [službami RESTful](custom-policy-rest-api-intro.md) k ověření vstupu uživatele.

V tomto scénáři přidáme možnost pro uživatele zadat věrnostní číslo na stránce registrace Azure AD B2C. Ověříme, zda je tato kombinace e-mailu a věrnostního čísla mapována na propagační kód, a to odesláním těchto dat do rozhraní REST API. Pokud rozhraní REST API najde propagační kód pro tohoto uživatele, bude vrácena do Azure AD B2C. Nakonec propagační kód bude vložen do deklarací tokenu pro aplikaci využívat.

Můžete také navrhnout interakci jako krok orchestrace. To je vhodné v případě, že rozhraní REST API nebude ověřování dat na obrazovce a vždy vrátit deklarace identity. Další informace naleznete [v tématu Návod: Integrace serveru deklarací rozhraní REST API v cestě uživatele Azure AD B2C jako krok orchestrace](custom-policy-rest-api-claims-exchange.md).

## <a name="prerequisites"></a>Požadavky

- Proveďte kroky v [části Začínáme s vlastními zásadami](custom-policy-get-started.md). Měli byste mít funkční vlastní zásady pro registraci a přihlášení pomocí místních účtů.
- Zjistěte, jak [integrovat renovovat deklarace identity rozhraní REST ve vlastních zásadách Azure AD B2C](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>Příprava koncového bodu rozhraní REST API

V tomto návodu byste měli mít rozhraní REST API, které ověřuje, zda je e-mailová adresa registrována ve vašem back-endovém systému s ID loajality. Pokud je zaregistrováno, rozhraní REST API by mělo vrátit registrační propagační kód, který může zákazník použít k nákupu zboží v rámci vaší aplikace. V opačném případě by rozhraní REST API mělo vrátit chybovou zprávu HTTP 409: "Věrnostní ID {loyalty ID}" není přidruženo k e-mailové adrese {email}.".

Následující kód JSON ilustruje data Azure AD B2C bude odesílat do koncového bodu rozhraní REST API. 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

Jakmile rozhraní REST API ověří data, musí vrátit http 200 (Ok), s následujícími daty JSON:

```json
{
    "promoCode": "24534"
}
```

Pokud se ověření nezdařilo, rozhraní REST API musí vrátit `userMessage` HTTP 409 (Conflict) s prvkem JSON. IEF očekává `userMessage` tvrzení, že rozhraní REST API vrátí. Tato deklarace bude prezentována jako řetězec pro uživatele, pokud se ověření nezdaří.

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
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

## <a name="configure-the-restful-api-technical-profile"></a>Konfigurace technického profilu rozhraní RESTful API 

[Klidný technický profil](restful-technical-profile.md) poskytuje podporu pro propojení s vaší vlastní službou RESTful. Azure AD B2C odesílá data do služby RESTful `InputClaims` v `OutputClaims` kolekci a přijímá data zpět do kolekce. Najděte element **ClaimsProviders** a přidejte nového poskytovatele deklarací identity následujícím způsobem:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Check loyaltyId Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
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

V tomto příkladu `userLanguage` bude odeslána do `lang` služby REST jako v rámci datové části JSON. Hodnota deklarace `userLanguage` obsahuje aktuální ID uživatelského jazyka. Další informace naleznete v tématu [překládání deklarací .](claim-resolver-overview.md)

Výše uvedené `AuthenticationType` `AllowInsecureAuthInProduction` komentáře a určit změny, které byste měli provést při přechodu do produkčního prostředí. Informace o tom, jak zabezpečit rozhraní API RESTful pro produkční prostředí, naleznete [v tématu Secure RESTful API](secure-rest-api.md).

## <a name="validate-the-user-input"></a>Ověření vstupu uživatele

Chcete-li získat věrnostní číslo uživatele během registrace, musíte uživateli povolit zadání těchto dat na obrazovce. Přidejte deklaraci výstupu **loyaltyId** na stránku registrace přidáním do prvku existujícího prvku technického `OutputClaims` profilu registrace. Zadejte celý seznam výstupních deklarací pro řízení pořadí, ve které jsou deklarace uvedeny na obrazovce.  

Přidejte odkaz na technický profil ověření do technického `REST-ValidateProfile`profilu registrace, který volá . Nový technický profil ověření bude přidán do `<ValidationTechnicalProfiles>` horní části kolekce definované v základní zásady. Toto chování znamená, že pouze po úspěšnéověření Azure AD B2C přesune k vytvoření účtu v adresáři.   

1. Najít **ClaimsProviders** element. Přidejte nového poskytovatele deklarací identity takto:

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

## <a name="include-a-claim-in-the-token"></a>Zahrnout deklaraci do tokenu 

Chcete-li vrátit deklaraci kódu promo zpět do aplikace předávající strany, přidejte do souboru <em> `SocialAndLocalAccounts/` </em> výstupní deklaraci. Výstupní deklarace umožní, aby deklarace byla přidána do tokenu po úspěšné cestě uživatele, a bude odeslána do aplikace. Upravte prvek technického profilu v části `promoCode` předávající strany a přidejte jako výstupní deklaraci.
 
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

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD výběrem filtru **directory + předplatné** v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta Azure AD.
1. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak **vyhledejte**a vyberte registrace aplikací .
1. Vyberte **rozhraní Identity Experience Framework**.
1. Vyberte **Nahrát vlastní zásady**a potom nahrajte změněné soubory zásad: *TrustFrameworkExtensions.xml*a *SignUpOrSignin.xml*. 
1. Vyberte zásadu registrace nebo přihlášení, kterou jste nahráli, a klikněte na tlačítko **Spustit.**
1. Měli byste být schopni se zaregistrovat pomocí e-mailové adresy.
1. Klikněte na odkaz **Přihlásit se nyní.**
1. V **id vaší loajality**zadejte 1234 a klepněte na tlačítko **Pokračovat**. V tomto okamžiku byste měli dostat chybovou zprávu ověření.
1. Změňte na jinou hodnotu a klepněte na tlačítko **Pokračovat**.
1. Token odeslaný zpět do `promoCode` vaší aplikace obsahuje deklaraci.

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

Informace o zabezpečení prostředí API naleznete v následujících článcích:

- [Návod: Integrace renomovaných deklarací rozhraní REST api v cestě uživatele Azure AD B2C jako krok orchestrace](custom-policy-rest-api-claims-exchange.md)
- [Zabezpečte své RESTful API](secure-rest-api.md)
- [Reference: Technický profil RESTful](restful-technical-profile.md)
