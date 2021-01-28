---
title: Nastavení přihlašování pomocí poskytovatele služby Salesforce SAML pomocí protokolu SAML
titleSuffix: Azure AD B2C
description: Pomocí protokolu SAML v Azure Active Directory B2C nastavte přihlášení pomocí zprostředkovatele SAML služby Salesforce.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 9dce61817bdd6b42223028a624cd6e237be28bfe
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98953814"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-saml-protocol-in-azure-active-directory-b2c"></a>Nastavte přihlášení pomocí poskytovatele služby Salesforce SAML pomocí protokolu SAML v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"
[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku se dozvíte, jak povolit přihlášení uživatelů z organizace Salesforce pomocí [vlastních zásad](custom-policy-overview.md) v Azure Active Directory B2C (Azure AD B2C). Přihlášení povolíte přidáním [technického profilu zprostředkovatele identity SAML](saml-identity-provider-technical-profile.md) do vlastních zásad.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]
- Pokud jste to ještě neudělali, zaregistrujte si [bezplatný účet Developer Edition](https://developer.salesforce.com/signup). Tento článek používá [prostředí Salesforce blesk](https://developer.salesforce.com/page/Lightning_Experience_FAQ).
- [Nastavte doménu](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) pro vaši organizaci Salesforce.

## <a name="set-up-salesforce-as-an-identity-provider"></a>Nastavení Salesforce jako zprostředkovatele identity

1. [Přihlaste se k Salesforce](https://login.salesforce.com/).
2. V nabídce vlevo v části **Nastavení** rozbalte položku **Identita** a potom vyberte možnost **zprostředkovatel identity**.
3. Vyberte možnost **Povolit zprostředkovatele identity**.
4. V části **Vybrat certifikát** vyberte certifikát, který má Salesforce použít ke komunikaci s Azure AD B2C. Můžete použít výchozí certifikát.
5. Klikněte na **Uložit**.

### <a name="create-a-connected-app-in-salesforce"></a>Vytvoření připojené aplikace v Salesforce

1. Na stránce **zprostředkovatel identity** **se teď v části připojené aplikace vytvoří poskytovatelé služeb. Klikněte sem.**
2. V části **základní informace** zadejte požadované hodnoty pro připojenou aplikaci.
3. V části **nastavení webové aplikace** zaškrtněte políčko **Povolit SAML** .
4. Do pole **ID entity** zadejte následující adresu URL. Ujistěte se, že jste nahradili hodnotu pro `your-tenant` s názvem vašeho tenanta Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. Do pole **Adresa URL služby ACS** zadejte následující adresu URL. Ujistěte se, že jste nahradili hodnotu pro `your-tenant` s názvem vašeho tenanta Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Posuňte se do dolní části seznamu a klikněte na **Uložit**.

### <a name="get-the-metadata-url"></a>Získat adresu URL metadat

1. Na stránce Přehled vaší připojené aplikace klikněte na **Spravovat**.
2. Zkopírujte hodnotu pro **koncový bod zjišťování metadat** a pak ho uložte. Použijete ho později v tomto článku.

### <a name="set-up-salesforce-users-to-federate"></a>Nastavení uživatelů Salesforce na federovat

1. Na stránce **Správa** připojené aplikace klikněte na **Spravovat profily**.
2. Vyberte profily (nebo skupiny uživatelů), které chcete federovat s Azure AD B2C. Jako správce systému zaškrtněte políčko **správce systému** , abyste se mohli federovat pomocí svého účtu Salesforce.

## <a name="create-a-self-signed-certificate"></a>Vytvoření certifikátu podepsaného svým držitelem (self-signed certificate)

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Je potřeba uložit certifikát, který jste vytvořili v tenantovi Azure AD B2C.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Na stránce Přehled vyberte možnost **Architektura prostředí identity**.
5. Vyberte **klíče zásad** a pak vyberte **Přidat**.
6. Pro **Možnosti** vyberte možnost `Upload` .
7. Zadejte **Název** zásady. Například SAMLSigningCert. Předpona `B2C_1A_` se automaticky přidá do názvu vašeho klíče.
8. Vyhledejte a vyberte certifikát B2CSigningCert. pfx, který jste vytvořili.
9. Zadejte **heslo** pro certifikát.
3. Klikněte na **Vytvořit**.

## <a name="add-a-claims-provider"></a>Přidat zprostředkovatele deklarací identity

Pokud chcete, aby se uživatelé přihlásili pomocí účtu Salesforce, musíte účet definovat jako zprostředkovatele deklarací identity, se kterým Azure AD B2C můžou komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací, které používá Azure AD B2C k ověření, že konkrétní uživatel byl ověřen.

Účet Salesforce můžete definovat jako zprostředkovatele deklarací tak, že ho přidáte do prvku **ClaimsProviders** v souboru rozšíření zásady. Další informace najdete v tématu [definice technického profilu zprostředkovatele identity SAML](saml-identity-provider-technical-profile.md).

1. Otevřete *TrustFrameworkExtensions.xml*.
1. Vyhledejte element **ClaimsProviders** . Pokud neexistuje, přidejte jej pod kořenový element.
1. Přidejte nový **ClaimsProvider** následujícím způsobem:

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-SAML2">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Aktualizujte hodnotu **PartnerEntity** pomocí adresy URL metadat Salesforce, kterou jste zkopírovali dříve.
1. Aktualizujte hodnotu obou instancí **StorageReferenceId** na název klíče podpisového certifikátu. Například B2C_1A_SAMLSigningCert.
1. Vyhledejte `<ClaimsProviders>` část a přidejte následující fragment kódu XML. Pokud vaše zásada již obsahuje `SM-Saml-idp` technický profil, přejděte k dalšímu kroku. Další informace najdete v tématu [Správa relace jednotného přihlašování](custom-policy-reference-sso.md).

    ```xml
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```
1. Soubor uložte.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

::: zone-end