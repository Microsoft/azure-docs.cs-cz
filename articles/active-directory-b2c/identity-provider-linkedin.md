---
title: Nastavení registrace a přihlášení pomocí účtu LinkedIn
titleSuffix: Azure AD B2C
description: Poskytněte zákazníkům registraci a přihlášení k účtům LinkedIn ve vašich aplikacích pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ce5e8cfda4a9f51a90c8f26133a710f4d1c258b6
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448264"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu LinkedIn pomocí Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-linkedin-application"></a>Vytvoření aplikace LinkedIn

Pokud chcete povolit přihlášení uživatelům s účtem LinkedIn v Azure Active Directory B2C (Azure AD B2C), musíte vytvořit aplikaci na [webu LinkedIn Developers](https://www.developer.linkedin.com/). Další informace najdete v tématu [tok autorizačního kódu](/linkedin/shared/authentication/authorization-code-flow). Pokud ještě nemáte účet LinkedIn, můžete se zaregistrovat v [https://www.linkedin.com/](https://www.linkedin.com/) .

1. Přihlaste se na [Web vývojářům LinkedIn](https://www.developer.linkedin.com/) pomocí svých přihlašovacích údajů k účtu LinkedIn.
1. Vyberte **Moje aplikace** a pak klikněte na **vytvořit aplikaci**.
1. Zadejte **název aplikace**, **stránku LinkedIn**, **adresu URL zásad ochrany osobních údajů** a **logo aplikace**.
1. Odsouhlaste **podmínky použití rozhraní API** LinkedIn a klikněte na **vytvořit aplikaci**.
1. Vyberte kartu **ověřování** . V části **ověřovací klíče** zkopírujte hodnoty pro **ID klienta** a **tajný klíč klienta**. Obě tyto služby budete potřebovat ke konfiguraci LinkedInu jako poskytovatele identity ve vašem tenantovi. **Tajný kód klienta** je důležité bezpečnostní pověření.
1. Zaškrtněte políčko Upravit tužku vedle **autorizované adresy URL pro přesměrování vaší aplikace** a pak vyberte **Přidat adresu URL pro přesměrování**. Zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` , nahraďte `your-tenant-name` názvem svého tenanta. Při zadávání názvu tenanta musíte použít malá písmena, i když je tenant definovaný velkými písmeny v Azure AD B2C. Vyberte **Aktualizovat**.
2. Ve výchozím nastavení není vaše aplikace LinkedIn schválená pro obory související s přihlášením. Chcete-li požádat o revizi, vyberte kartu **produkty** a potom vyberte možnost **Přihlásit se pomocí LinkedInu**. Po dokončení kontroly budou do aplikace přidány požadované obory.
   > [!NOTE]
   > Rozsahy, které jsou aktuálně povolené pro vaši aplikaci, můžete zobrazit na kartě **ověřování** v části **rozsahy OAuth 2,0** .

::: zone pivot="b2c-user-flow"

## <a name="configure-linkedin-as-an-identity-provider"></a>Konfigurace LinkedInu jako zprostředkovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity** a pak vyberte **LinkedIn**.
1. Zadejte **název**. Například *LinkedIn*.
1. Pro **ID klienta** zadejte ID klienta aplikace LinkedIn, kterou jste vytvořili dříve.
1. Pro **tajný klíč klienta** zadejte tajný klíč klienta, který jste si poznamenali.
1. Vyberte **Uložit**.

## <a name="add-linkedin-identity-provider-to-a-user-flow"></a>Přidání poskytovatele identity LinkedInu do toku uživatele 

1. Ve vašem tenantovi Azure AD B2C vyberte **toky uživatelů**.
1. Klikněte na tok uživatele, který chcete přidat poskytovatele identity LinkedIn.
1. V části **Zprostředkovatelé sociální identity** vyberte **LinkedIn**.
1. Vyberte **Uložit**.
1. Pokud chcete zásady testovat, vyberte **Spustit tok uživatele**.
1. V poli **aplikace** vyberte webovou aplikaci s názvem *testapp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Vyberte tlačítko **tok spuštění uživatele** .
1. Na přihlašovací stránce nebo na přihlašovací stránce vyberte **LinkedIn** a přihlaste se pomocí účtu LinkedIn.

Pokud je proces přihlášení úspěšný, je váš prohlížeč přesměrován na `https://jwt.ms` , který zobrazuje obsah tokenu vrácený Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Je potřeba uložit tajný klíč klienta, který jste předtím nahráli ve svém tenantovi Azure AD B2C.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Na stránce Přehled vyberte možnost **Architektura prostředí identity**.
5. Vyberte **klíče zásad** a pak vyberte **Přidat**.
6. Pro **Možnosti** vyberte možnost `Manual` .
7. Zadejte **název** klíče zásad. Například, `LinkedInSecret`. *B2C_1A_* předpony se automaticky přidají do názvu vašeho klíče.
8. Do pole **tajný kód** zadejte tajný klíč klienta, který jste předtím nahráli.
9. Pro **použití klíče** vyberte `Signature` .
10. Klikněte na **Vytvořit**.

## <a name="configure-linkedin-as-an-identity-provider"></a>Konfigurace LinkedInu jako zprostředkovatele identity

Pokud chcete uživatelům povolit, aby se přihlásili pomocí účtu LinkedInu, musíte účet definovat jako zprostředkovatele deklarací identity, se kterým Azure AD B2C může komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací, které používá Azure AD B2C k ověření, že konkrétní uživatel byl ověřen.

Definujte účet LinkedIn jako zprostředkovatele deklarací, a to tak, že ho přidáte do prvku **ClaimsProviders** v souboru rozšíření zásady.

1. V editoru otevřete soubor *SocialAndLocalAccounts/* * TrustFrameworkExtensions.xml** *. Tento soubor se nachází v [úvodním balíčku vlastní zásady][starter-pack] , který jste stáhli jako součást jednoho z požadovaných součástí.
1. Vyhledejte element **ClaimsProviders** . Pokud neexistuje, přidejte jej pod kořenový element.
1. Přidejte nový **ClaimsProvider** následujícím způsobem:

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAuth2">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="scope">r_emailaddress r_liteprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <InputClaims />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Hodnotu **client_id** nahraďte ID klienta aplikace LinkedIn, kterou jste předtím nahráli.
1. Soubor uložte.

### <a name="add-the-claims-transformations"></a>Přidat transformace deklarací identity

Technický profil LinkedIn vyžaduje, aby se transformace deklarací **ExtractGivenNameFromLinkedInResponse** a **ExtractSurNameFromLinkedInResponse** přidaly do seznamu ClaimsTransformations. Pokud v souboru nemáte definován element **ClaimsTransformations** , přidejte nadřazené elementy XML, jak je znázorněno níže. Transformace deklarací identity také potřebují definovat nový typ deklarace s názvem **nullStringClaim**.

Přidejte element **BuildingBlocks** v horní části souboru *TrustFrameworkExtensions.xml* . Příklad najdete v tématu *TrustFrameworkBase.xml* .

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testování vlastních zásad

1. Vyberte třeba zásady předávající strany `B2C_1A_signup_signin` .
1. V případě **aplikace** vyberte webovou aplikaci, kterou jste [předtím zaregistrovali](troubleshoot-custom-policies.md#troubleshoot-the-runtime). Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Vyberte tlačítko **Spustit** .
1. Na přihlašovací stránce nebo na přihlašovací stránce vyberte **LinkedIn** a přihlaste se pomocí účtu LinkedIn.

Pokud je proces přihlášení úspěšný, je váš prohlížeč přesměrován na `https://jwt.ms` , který zobrazuje obsah tokenu vrácený Azure AD B2C.

## <a name="migration-from-v10-to-v20"></a>Migrace z verze 1.0 do verze 2.0

LinkedIn nedávno [aktualizoval svá rozhraní API z verze 1.0 do verze 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Chcete-li migrovat existující konfiguraci do nové konfigurace, použijte informace v následujících částech k aktualizaci prvků v technickém profilu.

### <a name="replace-items-in-the-metadata"></a>Nahradit položky v metadatech

V existujícím elementu **metadat** v **TechnicalProfile** aktualizujte následující prvky **položky** z:

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

Do:

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>Přidat položky do metadat

V **metadatech** **TechnicalProfile** přidejte následující prvky **položky** :

```xml
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>Aktualizace OutputClaims

V existujícím **OutputClaimsu** **TechnicalProfile** aktualizujte následující prvky **OutputClaim** z:

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

Do:

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>Přidat nové elementy OutputClaimsTransformation

Do **OutputClaimsTransformationsu** **TechnicalProfile** přidejte následující prvky **OutputClaimsTransformation** :

```xml
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>Definujte nové transformace deklarací identity a typ deklarace identity.

V posledním kroku jste přidali nové transformace deklarací identity, které je třeba definovat. Chcete-li definovat transformace deklarací, přidejte je do seznamu **ClaimsTransformations**. Pokud v souboru nemáte definován element **ClaimsTransformations** , přidejte nadřazené elementy XML, jak je znázorněno níže. Transformace deklarací identity také potřebují definovat nový typ deklarace s názvem **nullStringClaim**.

Element **BuildingBlocks** by měl být přidán poblíž horní části souboru. Podívejte se na *TrustframeworkBase.xml* jako příklad.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="obtain-an-email-address"></a>Získání e-mailové adresy

V rámci migrace LinkedInu z verze 1.0 do verze 2.0 je k získání e-mailové adresy vyžadováno další volání rozhraní API. Pokud potřebujete e-mailovou adresu získat během registrace, udělejte toto:

1. Provedením výše uvedených kroků umožníte Azure AD B2C federovat pomocí LinkedInu, aby se uživatel mohl přihlásit. V rámci federace Azure AD B2C obdrží přístupový token pro LinkedIn.
1. Uložte přístupový token LinkedIn do deklarace identity. [Projděte si pokyny zde](idp-pass-through-user-flow.md).
1. Přidejte následujícího zprostředkovatele deklarací identity, který odešle požadavek na `/emailAddress` rozhraní API LinkedInu. K autorizaci této žádosti potřebujete přístupový token LinkedInu.

    ```xml
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Přidejte následující krok orchestrace do cesty uživatele, aby se zprostředkovatel deklarací rozhraní API aktivoval, když se uživatel přihlásí pomocí LinkedInu. Nezapomeňte `Order` číslo odpovídajícím způsobem aktualizovat. Přidejte tento krok hned po kroku orchestrace, který aktivuje technický profil LinkedInu.

    ```xml
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

Získání e-mailové adresy z LinkedInu během registrace je volitelné. Pokud se rozhodnete nezískávat e-maily z LinkedInu, ale vyžadujete si ji při registraci, musí uživatel ručně zadat e-mailovou adresu a ověřit ji.

Úplnou ukázku zásad, které používají poskytovatele identity LinkedIn, najdete v tématu [vlastní zásady Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider).

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="migration-from-v10-to-v20"></a>Migrace z verze 1.0 do verze 2.0

LinkedIn nedávno [aktualizoval svá rozhraní API z verze 1.0 do verze 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). V rámci migrace je Azure AD B2C při registraci možné získat pouze úplné jméno uživatele LinkedIn. Pokud je e-mailová adresa jedním z atributů, které se shromažďují při registraci, uživatel musí ručně zadat e-mailovou adresu a ověřit ji.

::: zone-end