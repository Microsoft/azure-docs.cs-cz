---
title: Nastavení přihlašování pomocí účtu LinkedIn pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Nastavte přihlášení pomocí účtu LinkedIn v Azure Active Directory B2C pomocí vlastních zásad.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 084a8e247ab8bb3e6e6c75cf8cfe8e204384f549
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96345146"
---
# <a name="set-up-sign-in-with-a-linkedin-account-using-custom-policies-in-azure-active-directory-b2c"></a>Nastavte přihlášení pomocí účtu LinkedIn pomocí vlastních zásad v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku se dozvíte, jak povolit přihlášení uživatelů z účtu LinkedIn pomocí [vlastních zásad](custom-policy-overview.md) v Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Požadavky

- Proveďte kroky v části Začínáme [s vlastními zásadami v Azure Active Directory B2C](custom-policy-get-started.md).
- Účet LinkedIn – Pokud ho ještě nemáte, [vytvořte účet](https://www.linkedin.com/start/join).
- LinkedIn – stránka: potřebujete [LinkedIn](https://www.linkedin.com/help/linkedin/answer/710/creating-a-linkedin-company-page) , který můžete přidružit k aplikaci LinkedIn, kterou vytvoříte v další části.

## <a name="create-an-application"></a>Vytvoření aplikace

Chcete-li použít LinkedIn jako poskytovatele identity v Azure AD B2C, je nutné vytvořit aplikaci LinkedIn.

### <a name="create-app"></a>Vytvoření aplikace

1. Přihlaste se k webu pro [správu aplikací LinkedIn](https://www.linkedin.com/secure/developer?newapp=) pomocí přihlašovacích údajů k účtu LinkedIn.
1. Vyberte **Vytvořit aplikaci**.
1. Zadejte **název aplikace**.
1. Zadejte název **společnosti** , který odpovídá názvu linkedinové stránky. Vytvořte stránku LinkedIn, pokud ji ještě nemáte.
1. Volitelné Zadejte **adresu URL zásad ochrany osobních údajů**. Musí to být platná adresa URL, ale nemusí být dosažitelný koncový bod.
1. Zadejte **obchodní e-mail**.
1. Nahrajte obrázek **loga aplikace** . Obrázek loga musí být čtvercový a jeho rozměry musí mít aspoň 100x100 pixelů.
1. V části **Products (produkty** ) ponechte výchozí nastavení.
1. Přečtěte si informace, které jsou uvedeny v **licenčních podmínkami**. Pokud souhlasíte s podmínkami, zaškrtněte políčko.
1. Vyberte **Vytvořit aplikaci**.

### <a name="configure-auth"></a>Konfigurace ověřování

1. Vyberte kartu **ověřování** .
1. Poznamenejte si **ID klienta**.
1. Odhalte a zaznamenejte **tajný klíč klienta**.
1. V části **Nastavení OAuth 2,0** přidejte následující **adresu URL pro přesměrování**. Nahraďte `your-tenant` názvem vašeho tenanta. Pro název tenanta použijte **všechna malá písmena** , i když je v Azure AD B2C definovaná velká písmena.

    `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp`

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Je potřeba uložit tajný klíč klienta, který jste předtím nahráli ve svém tenantovi Azure AD B2C.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Na stránce Přehled vyberte možnost **Architektura prostředí identity**.
5. Vyberte **klíče zásad** a pak vyberte **Přidat**.
6. Pro **Možnosti** vyberte možnost `Manual` .
7. Zadejte **název** klíče zásad. Například, `LinkedInSecret`. *B2C_1A_* předpony se automaticky přidají do názvu vašeho klíče.
8. Do pole **tajný kód** zadejte tajný klíč klienta, který jste předtím nahráli.
9. Pro **použití klíče** vyberte `Signature` .
10. Klikněte na **Vytvořit**.

## <a name="add-a-claims-provider"></a>Přidat zprostředkovatele deklarací identity

Pokud chcete, aby se uživatelé přihlásili pomocí účtu LinkedIn, musíte účet definovat jako zprostředkovatele deklarací identity, se kterým Azure AD B2C můžou komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací, které používá Azure AD B2C k ověření, že konkrétní uživatel byl ověřen.

Definujte účet LinkedIn jako zprostředkovatele deklarací, a to tak, že ho přidáte do prvku **ClaimsProviders** v souboru rozšíření zásady.

1. V editoru otevřete soubor * SocialAndLocalAccounts/**TrustFrameworkExtensions.xml** _. Tento soubor se nachází v [úvodním balíčku vlastní zásady][starter-pack] , který jste stáhli jako součást jednoho z požadovaných součástí.
1. Vyhledejte element _ *ClaimsProviders**. Pokud neexistuje, přidejte jej pod kořenový element.
1. Přidejte nový **ClaimsProvider** následujícím způsobem:

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
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
1. Uložte soubor.

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

### <a name="upload-the-extension-file-for-verification"></a>Nahrajte soubor rozšíření pro ověření.

Nyní máte nakonfigurovanou zásadu, která Azure AD B2C ví, jak komunikovat s vaším účtem LinkedIn. Zkuste nahrát soubor rozšíření vaší zásady, abyste se ujistili, že zatím nemá žádné problémy.

1. Na stránce **vlastní zásady** ve vašem tenantovi Azure AD B2C vyberte **Odeslat zásadu**.
2. Pokud existuje, zapněte **zásadu přepsat** a pak vyhledejte a vyberte soubor *TrustFrameworkExtensions.xml* .
3. Klikněte na **Odeslat**.

## <a name="register-the-claims-provider"></a>Registrace zprostředkovatele deklarací identity

V tuto chvíli je poskytovatel identity nastavený, ale není k dispozici na žádném z přihlašovacích obrazovek pro registraci nebo přihlášení. Aby byl k dispozici, vytvořte duplikát existující cesty uživatele šablony a pak ji upravte, aby měl také poskytovatele identity LinkedIn.

1. Otevřete soubor *TrustFrameworkBase.xml* v úvodní sadě.
2. Vyhledejte a zkopírujte celý obsah prvku **UserJourney** , který obsahuje `Id="SignUpOrSignIn"` .
3. Otevřete *TrustFrameworkExtensions.xml* a vyhledejte element **userjourney** . Pokud element neexistuje, přidejte jej.
4. Vložte celý obsah elementu **UserJourney** , který jste zkopírovali jako podřízený prvek **userjourney** elementu.
5. Přejmenujte ID cesty pro uživatele. Například, `SignUpSignInLinkedIn`.

### <a name="display-the-button"></a>Zobrazit tlačítko

Element **claimsproviderselection.** se podobá tlačítku poskytovatele identity na obrazovce pro registraci nebo přihlášení. Pokud přidáte pro účet LinkedIn element **claimsproviderselection.** , zobrazí se nové tlačítko, když se uživatel na stránce zařadí.

1. Vyhledejte element **OrchestrationStep** , který obsahuje `Order="1"` cestu k uživateli, kterou jste vytvořili.
2. Pod **ClaimsProviderSelections** přidejte následující element. Nastavte hodnotu **TargetClaimsExchangeId** na odpovídající hodnotu, například `LinkedInExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Propojit tlačítko s akcí

Teď, když máte tlačítko na místě, musíte ho propojit s akcí. Tato akce je v tomto případě určena pro Azure AD B2C ke komunikaci s účtem LinkedIn pro příjem tokenu.

1. Najděte **OrchestrationStep** , který obsahuje `Order="2"` cestu k uživateli.
2. Přidejte následující prvek **ClaimsExchange** a ujistěte se, že používáte stejnou hodnotu pro ID, které jste použili pro **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```

    Aktualizujte hodnotu **TechnicalProfileReferenceId** na ID technického profilu, který jste vytvořili dříve. Například, `LinkedIn-OAUTH`.

3. Uložte soubor *TrustFrameworkExtensions.xml* a znovu ho nahrajte pro účely ověření.

## <a name="create-an-azure-ad-b2c-application"></a>Vytvoření aplikace Azure AD B2C

Komunikace s Azure AD B2C probíhá prostřednictvím aplikace, kterou zaregistrujete v tenantovi B2C. V této části jsou uvedeny volitelné kroky, které můžete provést, chcete-li vytvořit testovací aplikaci, pokud jste tak již neučinili.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aktualizace a testování souboru předávající strany

Aktualizujte soubor předávající strany (RP), který iniciuje cestu uživatele, kterou jste vytvořili.

1. Vytvořte kopii *SignUpOrSignIn.xml* v pracovním adresáři a přejmenujte ji. Přejmenujte ho například na *SignUpSignInLinkedIn.xml*.
2. Otevřete nový soubor a aktualizujte hodnotu atributu **PolicyId** pro **TrustFrameworkPolicy** s jedinečnou hodnotou. Například, `SignUpSignInLinkedIn`.
3. Aktualizujte hodnotu **PUBLICPOLICYURI** identifikátorem URI pro zásadu. Například`http://contoso.com/B2C_1A_signup_signin_linkedin`
4. Aktualizujte hodnotu atributu **ReferenceId** v **DefaultUserJourney** tak, aby odpovídala ID nové cesty uživatele, kterou jste vytvořili (SignUpSignLinkedIn).
5. Uložte změny, nahrajte soubor a pak v seznamu vyberte novou zásadu.
6. Ujistěte se, že je vybrána možnost Azure AD B2C aplikace, kterou jste vytvořili v poli **Vybrat aplikaci** , a poté ji otestujte kliknutím na tlačítko **Spustit nyní**.

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
2. Uložte přístupový token LinkedIn do deklarace identity. [Projděte si pokyny zde](idp-pass-through-custom.md).
3. Přidejte následujícího zprostředkovatele deklarací identity, který odešle požadavek na `/emailAddress` rozhraní API LinkedInu. K autorizaci této žádosti potřebujete přístupový token LinkedInu.

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

4. Přidejte následující krok orchestrace do cesty uživatele, aby se zprostředkovatel deklarací rozhraní API aktivoval, když se uživatel přihlásí pomocí LinkedInu. Nezapomeňte `Order` číslo odpovídajícím způsobem aktualizovat. Přidejte tento krok hned po kroku orchestrace, který aktivuje technický profil LinkedInu.

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
