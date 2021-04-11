---
title: Nastavení registrace a přihlášení pomocí zprostředkovatele identity SAML
titleSuffix: Azure Active Directory B2C
description: Nastavte si registraci a přihlaste se pomocí libovolného zprostředkovatele identity SAML (IdP) v Azure Active Directory B2C.
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
ms.openlocfilehash: 944eff6e76f4e5759f70105fe9d09aa61093917f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028294"
---
# <a name="set-up-sign-up-and-sign-in-with-saml-identity-provider-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí zprostředkovatele identity SAML pomocí Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) podporuje federaci s poskytovateli identit SAML 2,0. V tomto článku se dozvíte, jak povolit přihlašování pomocí uživatelského účtu zprostředkovatele identity SAML a umožnit tak uživatelům přihlašovat se pomocí stávajících sociálních nebo podnikových identit, jako jsou [ADFS](./identity-provider-adfs.md) a [Salesforce](identity-provider-salesforce-saml.md).

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="scenario-overview"></a>Přehled scénáře

Můžete nakonfigurovat Azure AD B2C, aby se uživatelé mohli přihlašovat k aplikaci s přihlašovacími údaji od externích poskytovatelů nebo podnikových identit SAML (IdP). Když Azure AD B2C federuje s poskytovatelem identity SAML, funguje jako **poskytovatel služeb** , který iniciuje požadavek SAML **poskytovateli identity** SAML a čeká na odpověď SAML. V následujícím diagramu:

1. Aplikace zahájí žádost o autorizaci Azure AD B2C. Aplikace může být aplikace [OAuth 2,0](protocols-overview.md) nebo [OpenId Connect](openid-connect.md) nebo [poskytovatel služby SAML](saml-service-provider.md). 
1. Na přihlašovací stránce Azure AD B2C se uživatel rozhodne přihlašovat pomocí účtu zprostředkovatele identity SAML (například *Contoso*). Azure AD B2C inicializuje žádost o autorizaci SAML a převezme uživatele poskytovatele identity SAML, aby dokončil přihlášení.
1. Zprostředkovatel identity SAML vrátí odpověď SAML.
1. Azure AD B2C ověří token SAML, extrahuje deklarace identity, vydá vlastní token a převezme uživatele zpátky do aplikace.  

![Přihlášení pomocí toku zprostředkovatele identity SAML](./media/identity-provider-generic-saml/sign-in-with-saml-identity-provider-flow.png)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="components-of-the-solution"></a>Součásti řešení

Pro tento scénář jsou vyžadovány následující komponenty:

* **Zprostředkovatel identity** SAML s možností přijímat, dekódovat a reagovat na požadavky SAML od Azure AD B2C.
* Veřejně dostupný **koncový bod METADAT** SAML pro vašeho poskytovatele identity.
* [Tenant Azure AD B2C](tutorial-create-tenant.md).
 

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

K navázání vztahu důvěryhodnosti mezi Azure AD B2C a vaším poskytovatelem identity SAML musíte zadat platný certifikát x509 s privátním klíčem. Azure AD B2C podepíše požadavky SAML pomocí privátního klíče certifikátu. Zprostředkovatel identity ověří požadavek pomocí veřejného klíče certifikátu. Veřejný klíč je přístupný prostřednictvím metadat technického profilu. Případně můžete ručně odeslat soubor. cer do poskytovatele identity SAML.

Certifikát podepsaný svým držitelem je přijatelný pro většinu scénářů. V produkčních prostředích se doporučuje použít certifikát x509 vydaný certifikační autoritou. Kromě toho, jak je popsáno dále v tomto dokumentu, můžete u neprodukčního prostředí zakázat podepisování SAML na obou stranách.

### <a name="obtain-a-certificate"></a>Získání certifikátu

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="upload-the-certificate"></a>Odeslání certifikátu

Certifikát musíte uložit do svého tenanta Azure AD B2C.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Na stránce Přehled vyberte možnost **Architektura prostředí identity**.
1. Vyberte **klíče zásad** a pak vyberte **Přidat**.
1. Pro **Možnosti** vyberte možnost `Upload` .
1. Zadejte **název** klíče zásad. Například, `SAMLSigningCert`. Předpona `B2C_1A_` se automaticky přidá do názvu vašeho klíče.
1. Vyhledejte a vyberte soubor Certificate. pfx s privátním klíčem.
1. Klikněte na **Vytvořit**.

## <a name="configure-the-saml-technical-profile"></a>Konfigurace technického profilu SAML

Zprostředkovatele identity SAML můžete definovat tak, že ho přidáte do elementu **ClaimsProviders** v souboru rozšíření zásady. Zprostředkovatelé deklarací identity obsahují technický profil SAML, který určuje koncové body a protokoly potřebné ke komunikaci se zprostředkovatelem identity SAML. Přidání zprostředkovatele deklarací identity s technickým profilem SAML:

1. Otevřete *TrustFrameworkExtensions.xml*.
1. Vyhledejte element **ClaimsProviders** . Pokud neexistuje, přidejte jej pod kořenový element.
1. Přidejte nový **ClaimsProvider** následujícím způsobem:

    ```xml
    <ClaimsProvider>
      <Domain>Contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your SAML identity provider account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="http://schemas.microsoft.com/identity/claims/displayname" />
            <OutputClaim ClaimTypeReferenceId="email"  />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
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

Aktualizujte následující prvky XML s příslušnou hodnotou:

|XML – element  |Hodnota  |
|---------|---------|
|ClaimsProvider\Domain  | Název domény, který se používá pro [přímé přihlášení](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider). Zadejte název domény, který chcete použít v přímém přihlášení. Například *contoso.com*. |
|TechnicalProfile\DisplayName|Tato hodnota se zobrazí na tlačítku pro přihlášení na přihlašovací obrazovce. Například *Contoso*. |
|Metadata\PartnerEntity|Adresa URL metadat zprostředkovatele identity SAML. Nebo můžete zkopírovat metadata zprostředkovatele identity a přidat ho do elementu CDATA `<![CDATA[Your IDP metadata]]>` .|

## <a name="map-the-claims"></a>Mapování deklarací identity

Element **OutputClaims** obsahuje seznam deklarací identity vrácených zprostředkovatelem identity SAML. Namapujte název deklarace identity definované v zásadě na název kontrolního výrazu definovaný v poskytovateli identity. Seznam deklarací identity (kontrolní výrazy) najdete u svého zprostředkovatele identity. Další informace najdete v tématu [mapování deklarací identity](identity-provider-generic-saml-options.md#claims-mapping).

Ve výše uvedeném příkladu obsahuje *Contoso-typu Saml2* deklarace identity vrácené zprostředkovatelem identity SAML:

* Deklarace identity **issuerUserId** je namapovaná na deklaraci identity **assertionSubjectName** .
* Deklarace **first_name** je namapovaná na **danou** deklaraci identity.
* Deklarace **last_name** je namapována na deklaraci identity na **příjmení** .
* Deklarace identity **DisplayName** je namapovaná na `http://schemas.microsoft.com/identity/claims/displayname` deklaraci identity.
* Deklarace **e-mailu** bez mapování názvů

Technický profil také vrací deklarace identity, které nejsou vráceny zprostředkovatelem identity:

* Deklarace identity **identityProvider** , která obsahuje název poskytovatele identity.
* Deklarace identity **authenticationSource** s výchozí hodnotou **socialIdpAuthentication**.
 
### <a name="add-the-saml-session-technical-profile"></a>Přidat technický profil relace SAML

Pokud ještě nemáte `SM-Saml-idp` technický profil relace SAML, přidejte ho do zásad rozšíření. Vyhledejte `<ClaimsProviders>` část a přidejte následující fragment kódu XML. Pokud vaše zásada již obsahuje `SM-Saml-idp` technický profil, přejděte k dalšímu kroku. Další informace najdete v tématu [Správa relace jednotného přihlašování](custom-policy-reference-sso.md).

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

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-create-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="configure-your-saml-identity-provider"></a>Konfigurace poskytovatele identity SAML

Po nakonfigurování zásad je potřeba nakonfigurovat poskytovatele identity SAML pomocí Azure AD B2C metadat SAML. Metadata SAML jsou informace, které se používají v protokolu SAML k vystavování konfigurace zásad, **poskytovatele služeb**. Definuje umístění služeb, jako je například přihlášení a odhlášení, certifikáty, metoda přihlašování a další.

Každý zprostředkovatel identity SAML má různé kroky pro nastavení poskytovatele služeb. Někteří zprostředkovatelé identity SAML žádají o metadata Azure AD B2C, zatímco jiné vyžadují ruční procházení souboru metadat a zadání informací. Pokyny najdete v dokumentaci poskytovatele identity.

Následující příklad ukazuje adresu URL pro metadata SAML Azure AD B2Cho technického profilu:

```
https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

Pokud používáte [vlastní doménu](custom-domain.md), použijte následující formát:

```
https://your-domain-name/<your-tenant-name>.onmicrosoft.com/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

Nahraďte následující hodnoty:

- název **vašeho** tenanta, jako je třeba Your-tenant.onmicrosoft.com.
- **název vaší domény** pomocí vlastního názvu domény, například Login.contoso.com.
- **vaše zásady** s názvem zásady. Například B2C_1A_signup_signin_adfs.
- **váš** technický profil s názvem vašeho technického profilu zprostředkovatele identity SAML. Například contoso-typu Saml2.

Otevřete prohlížeč a přejděte na adresu URL. Ujistěte se, že jste zadali správnou adresu URL a máte přístup k souboru metadat XML.

## <a name="test-your-custom-policy"></a>Testování vlastních zásad

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. V části **zásady** vyberte **Architektura prostředí identity**
1. Vyberte třeba zásady předávající strany `B2C_1A_signup_signin` .
1. V případě **aplikace** vyberte webovou aplikaci, kterou jste [předtím zaregistrovali](tutorial-register-applications.md). Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Vyberte tlačítko **Spustit** .
1. Na přihlašovací stránce nebo na přihlašovací stránce vyberte **Contoso** , abyste se přihlásili pomocí účtu contoso.

Pokud je proces přihlášení úspěšný, je váš prohlížeč přesměrován na `https://jwt.ms` , který zobrazuje obsah tokenu vrácený Azure AD B2C.

## <a name="next-steps"></a>Další kroky

- [Konfigurace možností zprostředkovatele identity SAML pomocí Azure Active Directory B2C](identity-provider-generic-saml-options.md)

::: zone-end