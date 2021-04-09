---
title: Nastavení registrace a přihlášení pomocí účtu Salesforce
titleSuffix: Azure AD B2C
description: Poskytněte zákazníkům registraci a přihlášení k účtům Salesforce ve vašich aplikacích pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f87ef03a3af110ec17b7d907d4c2eac1bc1ca58b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579922"
---
# <a name="set-up-sign-up-and-sign-in-with-a-salesforce-account-using-azure-active-directory-b2c"></a>Nastavte si registraci a přihlaste se pomocí účtu Salesforce pomocí Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-salesforce-application"></a>Vytvoření aplikace Salesforce

Pokud chcete povolit přihlášení pro uživatele s účtem Salesforce v Azure Active Directory B2C (Azure AD B2C), musíte ve [správci aplikací](https://login.salesforce.com/)Salesforce vytvořit aplikaci. Další informace najdete v tématech [Konfigurace základního nastavení připojené aplikace](https://help.salesforce.com/articleView?id=connected_app_create_basics.htm)a [Povolení nastavení OAuth pro integraci rozhraní API](https://help.salesforce.com/articleView?id=connected_app_create_api_integration.htm) .

1. [Přihlaste se k Salesforce](https://login.salesforce.com/).
1. V nabídce vyberte možnost **Nastavení**.
1.  Rozbalte položku **aplikace** a pak vyberte **App Manager**.
1. Vyberte **Nová připojená aplikace**.
1. V části **základní informace** zadejte:
    1. **Název propojené aplikace** – název připojené aplikace se zobrazí ve správci aplikací a na dlaždici spouštěče aplikací. Název musí být jedinečný v rámci vaší organizace. 
    1. **Název rozhraní API** 
    1. **Kontaktní e-mail** – kontaktní E-mail pro Salesforce
1. V části **rozhraní API (povolit nastavení OAuth)** vyberte **Povolit nastavení OAuth** .
    1. Jako **adresu URL zpětného volání** zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Pokud používáte [vlastní doménu](custom-domain.md), zadejte `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Nahraďte `your-tenant-name` názvem vašeho tenanta a `your-domain-name` vlastní doménou. Při zadávání názvu tenanta musíte použít malá písmena, i když je tenant definovaný velkými písmeny v Azure AD B2C.
    1. Ve **vybraných oborech OAuth** vyberte **přístup k základním informacím (ID, profil, e-mail, adresa, telefon)** a **Umožněte přístup k jedinečnému identifikátoru (OpenID)**.
    1. Vyberte **vyžadovat tajný klíč pro tok webového serveru**.
1. Vyberte **Konfigurovat token ID** . 
    1. Nastavte **token platný po dobu** 5 minut.
    1. Vyberte **zahrnout standardní deklarace identity**.
1. Klikněte na **Uložit**.
1. Zkopírujte hodnoty **klíč příjemce** a **tajného kódu příjemce**. Obě tyto služby budete potřebovat ke konfiguraci Salesforce jako poskytovatele identity ve vašem tenantovi. **Tajný kód klienta** je důležité bezpečnostní pověření.

::: zone pivot="b2c-user-flow"

## <a name="configure-salesforce-as-an-identity-provider"></a>Konfigurace Salesforce jako zprostředkovatele identity

1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity** a potom vyberte **Nový poskytovatel OpenID Connect**.
1. Zadejte **název**. Zadejte například *Salesforce*.
1. V poli **Adresa URL metadat** zadejte adresu URL [dokumentu konfigurace Salesforce OpenID Connect](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm). Pro izolovaný prostor (sandbox) se login.salesforce.com nahrazuje test.salesforce.com. V případě komunity se login.salesforce.com nahrazuje adresou URL komunity, jako je například username.force.com/.well-known/openid-configuration. Adresa URL musí být HTTPS.

    ```
    https://login.salesforce.com/.well-known/openid-configuration
    ```

1. Jako **ID klienta** zadejte ID aplikace, které jste si poznamenali dříve.
1. Jako **tajný klíč klienta** zadejte tajný klíč klienta, který jste předtím nahráli.
1. Pro **Rozsah** zadejte `openid id profile email` .
1. Ponechte výchozí hodnoty pro **typ odpovědi** a **režim odezvy**.
1. Volitelné Jako **nápovědu k doméně** zadejte `contoso.com` . Další informace najdete v tématu [Nastavení přímého přihlašování pomocí Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. V části **mapování deklarací identity zprostředkovatele identity** vyberte následující deklarace identity:

    - **ID uživatele**: *Sub*
    - **Zobrazovaný název**: *název*
    - **Křestní jméno**: *given_name*
    - **Příjmení**: *family_name*
    - **E-mail**: *e-mail*

1. Vyberte **Uložit**.

## <a name="add-salesforce-identity-provider-to-a-user-flow"></a>Přidání poskytovatele identity Salesforce do toku uživatele 

V tuto chvíli je poskytovatel identity Salesforce nastavený, ale ještě není dostupný na žádném z přihlašovacích stránek. Přidání poskytovatele identity Salesforce do toku uživatele:

1. Ve vašem tenantovi Azure AD B2C vyberte **toky uživatelů**.
1. Klikněte na tok uživatele, který chcete přidat poskytovatele identity Salesforce.
1. V části **Zprostředkovatelé sociální identity** vyberte **Salesforce**.
1. Vyberte **Uložit**.
1. Pokud chcete zásady testovat, vyberte **Spustit tok uživatele**.
1. V poli **aplikace** vyberte webovou aplikaci s názvem *testapp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Vyberte tlačítko **tok spuštění uživatele** .
1. Na stránce registrace nebo přihlášení vyberte **Salesforce** , abyste se přihlásili pomocí účtu Salesforce.

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
7. Zadejte **název** klíče zásad. Například, `SalesforceSecret`. Předpona `B2C_1A_` se automaticky přidá do názvu vašeho klíče.
8. Do **tajného klíče** zadejte tajný klíč klienta, který jste předtím nahráli.
9. Pro **použití klíče** vyberte `Signature` .
10. Klikněte na **Vytvořit**.

## <a name="configure-salesforce-as-an-identity-provider"></a>Konfigurace Salesforce jako zprostředkovatele identity

Pokud chcete uživatelům povolit, aby se přihlásili pomocí účtu Salesforce, musíte účet definovat jako zprostředkovatele deklarací identity, se kterým Azure AD B2C můžou komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací, které používá Azure AD B2C k ověření, že konkrétní uživatel byl ověřen.

Účet Salesforce můžete definovat jako zprostředkovatele deklarací tak, že ho přidáte do prvku **ClaimsProviders** v souboru rozšíření zásady.

1. Otevřete *TrustFrameworkExtensions.xml*.
2. Vyhledejte element **ClaimsProviders** . Pokud neexistuje, přidejte jej pod kořenový element.
3. Přidejte nový **ClaimsProvider** následujícím způsobem:

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-OpenIdConnect">
          <DisplayName>Salesforce</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://login.salesforce.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid id profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your Salesforce application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_SalesforceSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. **Metadata** se nastaví na adresu URL [dokumentu konfigurace Salesforce OpenID Connect](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm). Pro izolovaný prostor (sandbox) se login.salesforce.com nahrazuje test.salesforce.com. V případě komunity se login.salesforce.com nahrazuje adresou URL komunity, jako je například username.force.com/.well-known/openid-configuration. Adresa URL musí být HTTPS.
5. Nastavte **client_id** na ID aplikace z registrace aplikace.
6. Soubor uložte.

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
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testování vlastních zásad

1. Vyberte třeba zásady předávající strany `B2C_1A_signup_signin` .
1. V případě **aplikace** vyberte webovou aplikaci, kterou jste [předtím zaregistrovali](troubleshoot-custom-policies.md#troubleshoot-the-runtime). Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Vyberte tlačítko **Spustit** .
1. Na stránce registrace nebo přihlášení vyberte **Salesforce** , abyste se přihlásili pomocí účtu Salesforce.

Pokud je proces přihlášení úspěšný, je váš prohlížeč přesměrován na `https://jwt.ms` , který zobrazuje obsah tokenu vrácený Azure AD B2C.


::: zone-end

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [předat token Salesforce do vaší aplikace](idp-pass-through-user-flow.md).
