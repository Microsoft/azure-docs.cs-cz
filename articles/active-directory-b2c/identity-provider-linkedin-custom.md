---
title: Nastavení přihlášení pomocí účtu LinkedIn pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Nastavte přihlášení pomocí účtu LinkedIn ve službě Azure Active Directory B2C pomocí vlastních zásad.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 80bd1b65d04ea49fc742033e1850d95a85021c9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188167"
---
# <a name="set-up-sign-in-with-a-linkedin-account-using-custom-policies-in-azure-active-directory-b2c"></a>Nastavení přihlášení pomocí účtu LinkedIn pomocí vlastních zásad ve službě Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek ukazuje, jak povolit přihlášení pro uživatele z účtu LinkedIn pomocí [vlastních zásad](custom-policy-overview.md) ve službě Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Požadavky

- Dokončete kroky v [části Začínáme s vlastními zásadami ve službě Azure Active Directory B2C](custom-policy-get-started.md).
- Účet LinkedIn – Pokud ho ještě nemáte, [vytvořte si účet](https://www.linkedin.com/start/join).
- Stránka LinkedIn – Ke spojení s aplikací LinkedIn, kterou vytvoříte v další části, potřebujete [stránku LinkedIn.](https://www.linkedin.com/help/linkedin/answer/710/creating-a-linkedin-company-page)

## <a name="create-an-application"></a>Vytvoření aplikace

Chcete-li používat LinkedIn jako poskytovatele identity v Azure AD B2C, musíte vytvořit aplikaci LinkedIn.

### <a name="create-app"></a>Vytvoření aplikace

1. Přihlaste se na web [správy aplikací LinkedIn](https://www.linkedin.com/secure/developer?newapp=) pomocí přihlašovacích údajů k účtu LinkedIn.
1. Vyberte **Vytvořit aplikaci**.
1. Zadejte **název aplikace**.
1. Zadejte název **společnosti** odpovídající názvu stránky LinkedIn. Pokud stránku LinkedIn ještě nemáte, vytvořte ji.
1. (Nepovinné) Zadejte **adresu URL zásad ochrany osobních údajů**. Musí se jednat o platnou adresu URL, ale nemusí se jednat o dosažitelný koncový bod.
1. Zadejte **firemní e-mail**.
1. Nahrajte obrázek **loga aplikace.** Obrázek loga musí být čtvercový a jeho rozměry musí být alespoň 100 x 100 pixelů.
1. Výchozí nastavení ponechejte v části **Produkty.**
1. Projděte si informace uvedené v **právním smyslu**. Pokud souhlasíte s podmínkami, zaškrtněte políčko.
1. Vyberte **Vytvořit aplikaci**.

### <a name="configure-auth"></a>Konfigurace ověřování

1. Vyberte kartu **Auth.**
1. Zaznamenejte **ID klienta**.
1. Odhalte a zaznamenejte **tajný klíč klienta**.
1. V části **Nastavení OAuth 2.0**přidejte následující **adresu URL přesměrování**. Nahraďte `your-tenant` se jménem svého tenanta. Použijte **všechna malá písmena** pro název klienta i v případě, že je definovánvelkými písmeny v Azure AD B2C.

    `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp`

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Musíte uložit tajný klíč klienta, který jste dříve zaznamenali v tenantovi Azure AD B2C.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C. V horní nabídce vyberte filtr **Directory + odběr** a zvolte adresář, který obsahuje vašeho klienta.
3. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Na stránce Přehled vyberte **rozhraní Identity Experience Framework**.
5. Vyberte **klávesy zásad a** pak vyberte **Přidat**.
6. V části `Manual` **Možnosti**zvolte .
7. Zadejte **název** klíče zásady. Například, `LinkedInSecret`. Předpona *B2C_1A_* se automaticky přidá k názvu klíče.
8. V **poli Tajné**zadejte tajný klíč klienta, který jste dříve zaznamenali.
9. V případě použití `Signature` **klíče**vyberte .
10. Klikněte na **Vytvořit**.

## <a name="add-a-claims-provider"></a>Přidání poskytovatele deklarací identity

Pokud chcete, aby se uživatelé přihlašovali pomocí účtu LinkedIn, musíte definovat účet jako poskytovatele deklarací identity, se kterým může Azure AD B2C komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací, které používá Azure AD B2C k ověření, že konkrétní uživatel byl ověřen.

Definujte účet LinkedIn jako poskytovatele deklarací identity přidáním do prvku **ClaimsProviders** v rozšiřujícím souboru zásad.

1. Otevřete soubor *SocialAndLocalAccounts/**TrustFrameworkExtensions.xml*** v editoru. Tento soubor je v [balíčku Vlastní zásady,][starter-pack] které jste stáhli jako součást jednoho z předpokladů.
1. Najít **ClaimsProviders** element. Pokud neexistuje, přidejte jej pod kořenový prvek.
1. Přidejte nového **Zprostředkovatele deklarací** identity takto:

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
            <Item Key="UsePolicyInRedirectUri">0</Item>
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

1. Nahraďte hodnotu **client_id** ID klienta aplikace LinkedIn, kterou jste dříve zaznamenali.
1. Uložte soubor.

### <a name="add-the-claims-transformations"></a>Přidání transformací deklarací

Technický profil LinkedIn vyžaduje **ExtractGivenNameFromLinkedInResponse** a **ExtractSurNameFromLinkedInResponse** deklarace transformace, které mají být přidány do seznamu ClaimsTransformations. Pokud nemáte **claimstransformations** element definovaný v souboru, přidejte nadřazené elementy XML, jak je znázorněno níže. Transformace deklarací také potřebují nový typ deklarace definované s názvem **nullStringClaim**.

Přidejte element **BuildingBlocks** do horní části souboru *TrustFrameworkExtensions.xml.* Příklad naleznete v souboru *TrustFrameworkBase.xml.*

```XML
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

### <a name="upload-the-extension-file-for-verification"></a>Nahrát soubor rozšíření pro ověření

Nyní máte zásady nakonfigurované tak, aby Azure AD B2C ví, jak komunikovat s vaším účtem LinkedIn. Zkuste nahrát soubor rozšíření zásad a potvrďte, že zatím nemá žádné problémy.

1. Na stránce **Vlastní zásady** v tenantovi Azure AD B2C vyberte **Zásady nahrávání**.
2. Povolte **Přepište zásadu, pokud existuje**, a potom vyhledejte soubor *TrustFrameworkExtensions.xml* a vyberte jej.
3. Klikněte na **Odeslat**.

## <a name="register-the-claims-provider"></a>Registrace zprostředkovatele deklarací identity

V tomto okamžiku byl nastaven zprostředkovatel identity, ale není k dispozici v žádné z přihlašovacích nebo přihlašovacích obrazovek. Chcete-li ji zpřístupnit, vytvořte duplikát existující cesty uživatele šablony a pak ji upravte tak, aby měla také zprostředkovatele identity LinkedIn.

1. Otevřete soubor *TrustFrameworkBase.xml* ve startovním balíčku.
2. Vyhledejte a zkopírujte celý obsah prvku `Id="SignUpOrSignIn"` **UserJourney,** který obsahuje .
3. Otevřete soubor *TrustFrameworkExtensions.xml* a vyhledejte element **UserJourneys.** Pokud prvek neexistuje, přidejte jeden.
4. Vložte celý obsah **UserJourney** prvek, který jste zkopírovali jako podřízený **UserJourneys** element.
5. Přejmenujte ID cesty uživatele. Například, `SignUpSignInLinkedIn`.

### <a name="display-the-button"></a>Zobrazení tlačítka

**ClaimsProviderSelection** prvek je obdobou tlačítko zprostředkovatele identity na přihlašovací nebo přihlašovací obrazovce. Pokud přidáte **claimsProviderSelection** prvek pro účet LinkedIn, nové tlačítko se zobrazí, když uživatel přistane na stránce.

1. Najděte **orchestrationstep** prvek, který zahrnuje `Order="1"` v cestě uživatele, které jste vytvořili.
2. V části **ClaimsProviderSelections**přidejte následující prvek. Nastavte hodnotu **TargetClaimsExchangeId** na příslušnou hodnotu, například `LinkedInExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Propojení tlačítka s akcí

Nyní, když máte tlačítko na místě, musíte jej propojit s akcí. Akce, v tomto případě je pro Azure AD B2C komunikovat s účtem LinkedIn pro příjem tokenu.

1. Najít **OrchestrationStep,** `Order="2"` který zahrnuje v cestě uživatele.
2. Přidejte následující **claimsexchange** prvek ujistěte se, že používáte stejnou hodnotu pro ID, které jste použili pro **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```

    Aktualizujte hodnotu **TechnicalProfileReferenceId** na ID technického profilu, který jste vytvořili dříve. Například, `LinkedIn-OAUTH`.

3. Uložte soubor *TrustFrameworkExtensions.xml* a znovu jej nahrajte k ověření.

## <a name="create-an-azure-ad-b2c-application"></a>Vytvoření aplikace Azure AD B2C

Komunikace s Azure AD B2C probíhá prostřednictvím aplikace, kterou zaregistrujete ve vašem tenantovi B2C. V této části jsou uvedeny volitelné kroky, které můžete provést a vytvořit testovací aplikaci, pokud jste tak ještě neučinili.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aktualizace a testování souboru předávající strany

Aktualizujte soubor předávající strany (RP), který iniciuje cestu uživatele, kterou jste vytvořili.

1. Vytvořte kopii *souboru SignUpOrSignIn.xml* ve svém pracovním adresáři a přejmenujte ji. Můžete jej například přejmenovat na *SignUpSignInLinkedIn.xml*.
2. Otevřete nový soubor a aktualizujte hodnotu atributu **PolicyId** pro **TrustFrameworkPolicy** s jedinečnou hodnotou. Například, `SignUpSignInLinkedIn`.
3. Aktualizujte hodnotu **PublicPolicyUri** pomocí identifikátoru URI pro zásadu. Například`http://contoso.com/B2C_1A_signup_signin_linkedin`
4. Aktualizujte hodnotu atributu **ReferenceId** v **DefaultUserJourney** tak, aby odpovídalo ID nové cesty uživatele, kterou jste vytvořili (SignUpSignLinkedIn).
5. Uložte změny, nahrajte soubor a vyberte novou zásadu v seznamu.
6. Ujistěte se, že aplikace Azure AD B2C, kterou jste vytvořili, je vybraná v poli **Vybrat aplikaci** a pak ji otestujte kliknutím na **spustit nyní**.

## <a name="migration-from-v10-to-v20"></a>Migrace z v1.0 na v2.0

LinkedIn nedávno [aktualizoval svá api z v1.0 na v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Chcete-li migrovat existující konfiguraci do nové konfigurace, použijte informace v následujících částech k aktualizaci prvků v technickém profilu.

### <a name="replace-items-in-the-metadata"></a>Nahrazení položek v metadatech

Ve stávajícím prvku **Metadata** **v technicalprofile**aktualizujte následující prvky **položky** z:

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

Do:

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>Přidání položek do metadat

V **metadatech** **TechnicalProfile**přidejte následující **prvky položky:**

```XML
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>Aktualizace outputclaims

Ve stávajících **OutputClaims** **technicalprofile**, aktualizovat následující **OutputClaim** prvky z:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

Do:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>Přidání nových elementů OutputClaimsTransformation

V **OutputClaimsTransformace** **TechnicalProfile**, přidejte následující **OutputClaimsTransformation** prvky:

```XML
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>Definovat nové transformace deklarací a typ deklarace identity

V posledním kroku jste přidali nové transformace deklarací identity, které je třeba definovat. Chcete-li definovat transformace deklarací identity, přidejte je do seznamu **ClaimsTransformations**. Pokud nemáte **claimstransformations** element definovaný v souboru, přidejte nadřazené elementy XML, jak je znázorněno níže. Transformace deklarací také potřebují nový typ deklarace definované s názvem **nullStringClaim**.

Prvek **BuildingBlocks** by měl být přidán v horní části souboru. Viz *TrustframeworkBase.xml* jako příklad.

```XML
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

V rámci migrace LinkedIn z v1.0 na v2.0 je k získání e-mailové adresy vyžadováno další volání do jiného rozhraní API. Pokud potřebujete získat e-mailovou adresu během registrace, postupujte takto:

1. Pomocí výše uvedených kroků umožníte Azure AD B2C federate s LinkedIn, aby uživatel mohl přihlásit. Jako součást federace Azure AD B2C obdrží přístupový token pro LinkedIn.
2. Uložte přístupový token LinkedIn do deklarace. [Viz pokyny zde](idp-pass-through-custom.md).
3. Přidejte následujícího zprostředkovatele deklarací identity, který provede požadavek do `/emailAddress` rozhraní API LinkedIn. Chcete-li tento požadavek autorizovat, potřebujete přístupový token LinkedIn.

    ```XML
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

4. Přidejte do cesty uživatele následující krok orchestrace, aby se poskytovatel deklarací api aktivoval, když se uživatel přihlásí pomocí LinkedInu. Ujistěte se, `Order` že číslo odpovídajícím způsobem aktualizujete. Přidejte tento krok ihned po kroku orchestrace, který aktivuje technický profil LinkedIn.

    ```XML
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

Získání e-mailové adresy z LinkedIn během registrace je volitelné. Pokud se rozhodnete nechcete získat e-mail od LinkedIn, ale vyžadují jeden během registrace, uživatel je povinen ručně zadat e-mailovou adresu a ověřit ji.

Úplný vzorek zásad, který používá zprostředkovatele identity LinkedIn, najdete v tématu [Vlastní zásady Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider).

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
