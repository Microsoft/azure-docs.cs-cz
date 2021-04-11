---
title: Nastavení registrace a přihlášení pomocí účtu Azure AD B2C z jiného tenanta Azure AD B2C
titleSuffix: Azure AD B2C
description: Poskytněte zákazníkům registraci a přihlášení k Azure AD B2C účtům z jiného tenanta v aplikacích pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 3aacc6c50024cfdca3af639e7c6d14f69ed00519
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029042"
---
# <a name="set-up-sign-up-and-sign-in-with-an-azure-ad-b2c-account-from-another-azure-ad-b2c-tenant"></a>Nastavení registrace a přihlášení pomocí účtu Azure AD B2C z jiného tenanta Azure AD B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="overview"></a>Přehled

Tento článek popisuje, jak nastavit federaci s jiným Azure AD B2C tenant. Když se vaše aplikace chrání pomocí Azure AD B2C, umožníte uživatelům z jiných Azure AD B2C's přihlásit se pomocí jejich stávajících účtů. V následujícím diagramu se uživatelé můžou přihlašovat k aplikaci chráněné pomocí Azure AD B2C *společnosti Contoso* s účtem, který spravuje tenant Azure AD B2C společnosti *Fabrikam*. 

![Azure AD B2C federace s jiným klientem Azure AD B2C](./media/identity-provider-azure-ad-b2c/azure-ad-b2c-federation.png)


## <a name="prerequisites"></a>Požadavky

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-azure-ad-b2c-application"></a>Vytvoření aplikace Azure AD B2C

Pokud chcete povolit přihlášení pro uživatele s účtem z jiného tenanta Azure AD B2C (například Fabrikam), v Azure AD B2C (například contoso):

1. Vytvořte [uživatelský tok](tutorial-create-user-flows.md)nebo [vlastní zásadu](custom-policy-get-started.md).
1. Pak vytvořte aplikaci v Azure AD B2C, jak je popsáno v této části. 

K vytvoření aplikace.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho jiného klienta Azure AD B2C (například Fabrikam.com).
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací** a pak vyberte **Nová registrace**.
1. Zadejte **název** aplikace. Například *ContosoApp*.
1. V části **podporované typy účtů** vyberte **účty v jakémkoli zprostředkovateli identity nebo organizačním adresáři (pro ověřování uživatelů pomocí toků uživatelů)**.
1. V části **identifikátor URI pro přesměrování** vyberte **Web** a zadejte následující adresu URL na všechna malá písmena, kde `your-B2C-tenant-name` se nahradí názvem vašeho tenanta Azure AD B2C (například contoso).

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Například, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    Pokud používáte [vlastní doménu](custom-domain.md), zadejte `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Nahraďte `your-domain-name` vlastní doménou a `your-tenant-name` názvem vašeho tenanta.

1. V části oprávnění zaškrtněte políčko **udělit souhlas správcům oprávnění OpenID a offline_access** .
1. Vyberte **Zaregistrovat**.
1. Na stránce **Azure AD B2C-registrace aplikací** vyberte aplikaci, kterou jste vytvořili, například *ContosoApp*.
1. Poznamenejte si **ID aplikace (klienta)** zobrazené na stránce Přehled aplikace. Budete ho potřebovat při konfiguraci poskytovatele identity v další části.
1. V nabídce vlevo v části **Spravovat** vyberte **certifikáty & tajných** kódů.
1. Vyberte **Nový tajný klíč klienta**.
1. Do pole **Popis** zadejte popis tajného klíče klienta. Například *clientsecret1*.
1. Pod položkou **konec platnosti** vyberte dobu, po kterou je tajný kód platný, a pak vyberte **Přidat**.
1. Poznamenejte si **hodnotu** tajného klíče. Budete ho potřebovat při konfiguraci poskytovatele identity v další části.


::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>Konfigurace Azure AD B2C jako zprostředkovatele identity

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, pro který chcete nakonfigurovat federaci (například contoso). V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje klienta Azure AD B2C (například contoso).
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity** a potom vyberte **Nový poskytovatel OpenID Connect**.
1. Zadejte **název**. Zadejte například *Fabrikam*.
1. V poli **Adresa URL metadat** zadejte následující adresu URL, která nahrazuje `{tenant}` název domény vašeho tenanta Azure AD B2C (například Fabrikam). Nahraďte `{policy}` názvem zásady, který nakonfigurujete v jiném tenantovi:

    ```
    https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration
    ```

    Například, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.

1. Jako **ID klienta** zadejte ID aplikace, které jste si poznamenali dříve.
1. Jako **tajný klíč klienta** zadejte tajný klíč klienta, který jste předtím nahráli.
1. Pro **Rozsah** zadejte `openid` .
1. Ponechte výchozí hodnoty pro **typ odpovědi** a **režim odezvy**.
1. Volitelné Jako **pomocný parametr domény** zadejte název domény, který chcete použít pro [přímé přihlášení](direct-signin.md#redirect-sign-in-to-a-social-provider). Například *fabrikam.com*.
1. V části **mapování deklarací identity zprostředkovatele identity** vyberte následující deklarace identity:

    - **ID uživatele**: *Sub*
    - **Zobrazovaný název**: *název*
    - **Křestní jméno**: *given_name*
    - **Příjmení**: *family_name*
    - **E-mail**: *e-mail*

1. Vyberte **Uložit**.

## <a name="add-azure-ad-b2c-identity-provider-to-a-user-flow"></a>Přidání poskytovatele identity Azure AD B2C do toku uživatele 

1. Ve vašem tenantovi Azure AD B2C vyberte **toky uživatelů**.
1. Klikněte na tok uživatele, který chcete přidat Azure AD B2C zprostředkovatele identity.
1. V části **Zprostředkovatelé sociální identity** vyberte **Fabrikam**.
1. Vyberte **Uložit**.
1. Pokud chcete zásady testovat, vyberte **Spustit tok uživatele**.
1. V poli **aplikace** vyberte webovou aplikaci s názvem *testapp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Vyberte tlačítko **tok spuštění uživatele** .
1. Na stránce registrace nebo přihlášení vyberte **Fabrikam** pro přihlášení k druhému tenantovi Azure AD B2C.

Pokud je proces přihlášení úspěšný, je váš prohlížeč přesměrován na `https://jwt.ms` , který zobrazuje obsah tokenu vrácený Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Je potřeba uložit klíč aplikace, který jste vytvořili dříve v tenantovi Azure AD B2C.

1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, pro který chcete nakonfigurovat federaci (například contoso). V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje klienta Azure AD B2C (například contoso).
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. V části **zásady** vyberte **Architektura prostředí identity**.
1. Vyberte **klíče zásad** a pak vyberte **Přidat**.
1. Pro **Možnosti** vyberte možnost `Manual` .
1. Zadejte **název** klíče zásad. Například, `FabrikamAppSecret`.  Předpona `B2C_1A_` se automaticky přidá do názvu klíče při jeho vytvoření, takže jeho odkaz v XML v následující části je *B2C_1A_FabrikamAppSecret*.
1. Do **tajného klíče** zadejte tajný klíč klienta, který jste si poznamenali dříve.
1. Pro **použití klíče** vyberte `Signature` .
1. Vyberte **Vytvořit**.

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>Konfigurace Azure AD B2C jako zprostředkovatele identity

Pokud chcete uživatelům povolit, aby se přihlásili pomocí účtu z jiného tenanta Azure AD B2C (Fabrikam), musíte definovat druhý Azure AD B2C jako zprostředkovatele deklarací identity, se kterým Azure AD B2C může komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací, které používá Azure AD B2C k ověření, že konkrétní uživatel byl ověřen.

Azure AD B2C můžete definovat jako zprostředkovatele deklarací přidáním Azure AD B2C do prvku **ClaimsProvider** v souboru rozšíření zásady.

1. Otevřete soubor *TrustFrameworkExtensions.xml* .
1. Vyhledejte element **ClaimsProviders** . Pokud neexistuje, přidejte jej pod kořenový element.
1. Přidejte nový **ClaimsProvider** následujícím způsobem:
    ```xml
    <ClaimsProvider>
      <Domain>fabrikam.com</Domain>
      <DisplayName>Federation with Fabrikam tenant</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AzureADB2CFabrikam-OpenIdConnect">
        <DisplayName>Fabrikam</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <Metadata>
          <!-- Update the Client ID below to the Application ID -->
          <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
          <!-- Update the metadata URL with the other Azure AD B2C tenant name and policy name -->
          <Item Key="METADATA">https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration</Item>
          <Item Key="UsePolicyInRedirectUri">false</Item>
          <Item Key="response_types">code</Item>
          <Item Key="scope">openid</Item>
          <Item Key="response_mode">form_post</Item>
          <Item Key="HttpBinding">POST</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_FabrikamAppSecret"/>
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
          <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
          <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss"  />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          <OutputClaim ClaimTypeReferenceId="otherMails" PartnerClaimType="emails"/>    
        </OutputClaims>
        <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
      </TechnicalProfile>
     </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Aktualizujte následující prvky XML s příslušnou hodnotou:

    |XML – element  |Hodnota  |
    |---------|---------|
    |ClaimsProvider\Domain  | Název domény, který se používá pro [přímé přihlášení](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider). Zadejte název domény, který chcete použít v přímém přihlášení. Například *fabrikam.com*. |
    |TechnicalProfile\DisplayName|Tato hodnota se zobrazí na tlačítku pro přihlášení na přihlašovací obrazovce. Například *Fabrikam*. |
    |Metadata \ client_id|Identifikátor aplikace zprostředkovatele identity Aktualizujte ID klienta s ID aplikace, které jste vytvořili dříve v druhém tenantovi Azure AD B2C.|
    |Metadata\METADATA|Adresa URL, která odkazuje na dokument konfigurace zprostředkovatele identity OpenID Connect, který se taky označuje jako OpenID dobře známý koncový bod konfigurace. Zadejte následující adresu URL, která nahrazuje `{tenant}` název domény druhého tenanta Azure AD B2C (Fabrikam). Nahraďte `{tenant}` názvem zásady, který nakonfigurujete v druhém tenantovi, a `{policy]` názvem zásady: `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration` . Například, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.|
    |CryptographicKeys| Aktualizujte hodnotu **StorageReferenceId** na název klíče zásad, který jste vytvořili dříve. Například, `B2C_1A_FabrikamAppSecret`.| 
    

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADB2CFabrikamExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADB2CFabrikamExchange" TechnicalProfileReferenceId="AzureADB2CFabrikam-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]


## <a name="test-your-custom-policy"></a>Testování vlastních zásad

1. Vyberte třeba zásady předávající strany `B2C_1A_signup_signin` .
1. V případě **aplikace** vyberte webovou aplikaci, kterou jste [předtím zaregistrovali](tutorial-register-applications.md). Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Vyberte tlačítko **Spustit** .
1. Na stránce registrace nebo přihlášení vyberte **Fabrikam** pro přihlášení k druhému tenantovi Azure AD B2C.

Pokud je proces přihlášení úspěšný, je váš prohlížeč přesměrován na `https://jwt.ms` , který zobrazuje obsah tokenu vrácený Azure AD B2C.

::: zone-end

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [předat druhý token Azure AD B2C vaší aplikaci](idp-pass-through-user-flow.md).
