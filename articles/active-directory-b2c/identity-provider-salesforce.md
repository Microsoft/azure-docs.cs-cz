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
ms.date: 12/07/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 69c2bd96c7aa3bb3328784bb3b5027ade4902c43
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97669223"
---
# <a name="set-up-sign-up-and-sign-in-with-a-salesforce-account-using-azure-active-directory-b2c"></a>Nastavte si registraci a přihlaste se pomocí účtu Salesforce pomocí Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-salesforce-application"></a>Vytvoření aplikace Salesforce

Pokud chcete použít účet Salesforce v Azure Active Directory B2C (Azure AD B2C), musíte ve **správci aplikací** Salesforce vytvořit aplikaci. Další informace najdete v tématech [Konfigurace základního nastavení připojené aplikace](https://help.salesforce.com/articleView?id=connected_app_create_basics.htm)a [Povolení nastavení OAuth pro integraci rozhraní API](https://help.salesforce.com/articleView?id=connected_app_create_api_integration.htm) .

1. [Přihlaste se k Salesforce](https://login.salesforce.com/).
1. V nabídce vyberte možnost **Nastavení**.
1.  Rozbalte položku **aplikace** a pak vyberte **App Manager**.
1. Vyberte **Nová připojená aplikace**.
1. V části **základní informace** zadejte:
    1. **Název propojené aplikace** – název připojené aplikace se zobrazí ve správci aplikací a na dlaždici spouštěče aplikací. Název musí být jedinečný v rámci vaší organizace. 
    1. **Název rozhraní API** 
    1. **Kontaktní e-mail** – kontaktní E-mail pro Salesforce
1. V části **rozhraní API (povolit nastavení OAuth)** vyberte **Povolit nastavení OAuth** .
1. Do **adresy URL zpětného volání** zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Nahraďte `your-tenant-name` názvem vašeho tenanta. Při zadávání názvu tenanta musíte použít malá písmena, i když je tenant definovaný velkými písmeny v Azure AD B2C.
1. Ve **vybraných oborech OAuth** vyberte **přístup k základním informacím (ID, profil, e-mail, adresa, telefon)** a **Umožněte přístup k jedinečnému identifikátoru (OpenID)**.
1. Vyberte **vyžadovat tajný klíč pro tok webového serveru**.
1. Vyberte **Konfigurovat token ID** a pak vyberte **zahrnout standardní deklarace identity**.
1. Klikněte na **Uložit**.
1. Zkopírujte hodnoty **klíč příjemce** a **tajného kódu příjemce**. Obě tyto služby budete potřebovat ke konfiguraci Salesforce jako poskytovatele identity ve vašem tenantovi. **Tajný kód klienta** je důležité bezpečnostní pověření.

::: zone pivot="b2c-user-flow"

## <a name="configure-a-salesforce-account-as-an-identity-provider"></a>Konfigurace účtu Salesforce jako zprostředkovatele identity

1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity** a potom vyberte **Nový poskytovatel OpenID Connect**.
1. Zadejte **název**. Zadejte například *Salesforce*.
1. V poli **Adresa URL metadat** zadejte následující adresu URL, která nahrazuje `{org}` vaši organizaci Salesforce:

    ```
    https://{org}.my.salesforce.com/.well-known/openid-configuration
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
    - **E-mail**: *preferred_username*

1. Vyberte **Uložit**.
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
7. Zadejte **název** klíče zásad. Například `SalesforceSecret`. Předpona `B2C_1A_` se automaticky přidá do názvu vašeho klíče.
8. Do **tajného klíče** zadejte tajný klíč klienta, který jste předtím nahráli.
9. Pro **použití klíče** vyberte `Signature` .
10. Klikněte na **Vytvořit**.

## <a name="add-a-claims-provider"></a>Přidat zprostředkovatele deklarací identity

Pokud chcete, aby se uživatelé přihlásili pomocí účtu Salesforce, musíte účet definovat jako zprostředkovatele deklarací identity, se kterým Azure AD B2C můžou komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací, které používá Azure AD B2C k ověření, že konkrétní uživatel byl ověřen.

Účet Salesforce můžete definovat jako zprostředkovatele deklarací tak, že ho přidáte do prvku **ClaimsProviders** v souboru rozšíření zásady.

1. Otevřete *TrustFrameworkExtensions.xml*.
2. Vyhledejte element **ClaimsProviders** . Pokud neexistuje, přidejte jej pod kořenový element.
3. Přidejte nový **ClaimsProvider** následujícím způsobem:

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-OIDC">
          <DisplayName>Salesforce</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <!-- Update the {org} below to your Salesforce organization -->
            <Item Key="METADATA">https://{org}.my.salesforce.com/.well-known/openid-configuration</Item>
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

4. Nastavte identifikátor URI **metadat** `{org}` v organizaci Salesforce.
5. Nastavte **client_id** na ID aplikace z registrace aplikace.
6. Uložte soubor.

### <a name="upload-the-extension-file-for-verification"></a>Nahrajte soubor rozšíření pro ověření.

Teď jste nakonfigurovali zásady tak, aby Azure AD B2C vědět, jak komunikovat s vaším účtem Salesforce. Zkuste nahrát soubor s příponou zásady jenom tak, aby se ověřilo, že zatím nemá žádné problémy.

1. Na stránce **vlastní zásady** ve vašem tenantovi Azure AD B2C vyberte **Odeslat zásadu**.
2. Pokud existuje, zapněte **zásadu přepsat** a pak vyhledejte a vyberte soubor *TrustFrameworkExtensions.xml* .
3. Klikněte na **Odeslat**.

## <a name="register-the-claims-provider"></a>Registrace zprostředkovatele deklarací identity

V tuto chvíli je poskytovatel identity nastavený, ale není k dispozici na žádném z obrazovek pro registraci a přihlašování. Aby byl k dispozici, vytvořte duplikát existující cesty uživatele šablony a pak ji upravte, aby měl také poskytovatele identity Salesforce.

1. Otevřete soubor *TrustFrameworkBase.xml* z úvodní sady.
2. Vyhledejte a zkopírujte celý obsah prvku **UserJourney** , který obsahuje `Id="SignUpOrSignIn"` .
3. Otevřete *TrustFrameworkExtensions.xml* a vyhledejte element **userjourney** . Pokud element neexistuje, přidejte jej.
4. Vložte celý obsah elementu **UserJourney** , který jste zkopírovali jako podřízený prvek **userjourney** elementu.
5. Přejmenujte ID cesty pro uživatele. Například `SignUpSignInSalesforce`.

### <a name="display-the-button"></a>Zobrazit tlačítko

Element **claimsproviderselection.** se podobá tlačítku poskytovatele identity na obrazovce pro registraci a přihlašování. Pokud přidáte pro účet Salesforce element **claimsproviderselection.** , zobrazí se nové tlačítko, když se uživatel na stránce zařadí.

1. Vyhledejte element **OrchestrationStep** , který obsahuje `Order="1"` cestu k uživateli, kterou jste vytvořili.
2. Pod **ClaimsProviderSelects** přidejte následující element. Nastavte hodnotu **TargetClaimsExchangeId** na odpovídající hodnotu, například `SalesforceExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Propojit tlačítko s akcí

Teď, když máte tlačítko na místě, musíte ho propojit s akcí. Tato akce je v tomto případě určena pro Azure AD B2C ke komunikaci s účtem Salesforce pro příjem tokenu.

1. Najděte **OrchestrationStep** , který obsahuje `Order="2"` cestu k uživateli.
2. Přidejte následující prvek **ClaimsExchange** a ujistěte se, že používáte stejnou hodnotu pro ID, které jste použili pro **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-OIDC" />
    ```

    Aktualizujte hodnotu **TechnicalProfileReferenceId** na ID technického profilu, který jste vytvořili dříve. Například `Salesforce-OIDC`.

3. Uložte soubor *TrustFrameworkExtensions.xml* a znovu ho nahrajte pro účely ověření.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-salesforce-identity-provider-to-a-user-flow"></a>Přidání poskytovatele identity Salesforce do toku uživatele 

1. Ve vašem tenantovi Azure AD B2C vyberte **toky uživatelů**.
1. Klikněte na tok uživatele, který chcete poskytovateli identity Salesforce.
1. V části **Zprostředkovatelé sociální identity** vyberte **Salesforce**.
1. Vyberte **Uložit**.
1. Pokud chcete zásady testovat, vyberte **Spustit tok uživatele**.
1. V poli **aplikace** vyberte webovou aplikaci s názvem *testapp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Klikněte na **Spustit tok uživatele** .

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Aktualizace a testování souboru předávající strany

Aktualizujte soubor předávající strany (RP), který iniciuje cestu uživatele, kterou jste vytvořili.

1. Vytvořte kopii *SignUpOrSignIn.xml* v pracovním adresáři a přejmenujte ji. Přejmenujte ho například na *SignUpSignInSalesforce.xml*.
1. Otevřete nový soubor a aktualizujte hodnotu atributu **PolicyId** pro **TrustFrameworkPolicy** s jedinečnou hodnotou. Například `SignUpSignInSalesforce`.
1. Aktualizujte hodnotu **PUBLICPOLICYURI** identifikátorem URI pro zásadu. Například`http://contoso.com/B2C_1A_signup_signin_Salesforce`
1. Aktualizujte hodnotu atributu **ReferenceId** v **DefaultUserJourney** tak, aby odpovídala ID nové cesty uživatele, kterou jste vytvořili (SignUpSignSalesforce).
1. Uložte změny a nahrajte soubor.
1. V části **vlastní zásady** vyberte **B2C_1A_signup_signin**.
1. V části **Vybrat aplikaci** vyberte webovou aplikaci s názvem *testapp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Vyberte **Spustit nyní** a vyberte Salesforce pro přihlášení pomocí Salesforce a otestujte vlastní zásady.

::: zone-end

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [předat token Salesforce do vaší aplikace](idp-pass-through-user-flow.md).
