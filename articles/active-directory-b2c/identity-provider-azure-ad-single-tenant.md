---
title: Nastavení přihlášení pro organizaci Azure AD
titleSuffix: Azure AD B2C
description: Nastavte přihlášení pro konkrétní Azure Active Directory organizaci v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2b640730bac410136ef8fdd4ea8e0261f68a3284
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2021
ms.locfileid: "98538146"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Nastavení přihlášení pro konkrétní organizaci Azure Active Directory v Azure Active Directory B2C

V tomto článku se dozvíte, jak povolit přihlášení pro uživatele z konkrétní organizace Azure AD pomocí toku uživatele v Azure AD B2C.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="register-an-azure-ad-app"></a>Registrace aplikace Azure AD

Pokud chcete povolit přihlášení pro uživatele s účtem Azure AD z konkrétní organizace Azure AD, v Azure Active Directory B2C (Azure AD B2C) musíte vytvořit aplikaci v [Azure Portal](https://portal.azure.com). Další informace najdete v tématu [Registrace aplikace s platformou Microsoft Identity](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje váš tenant organizace Azure AD (například contoso.com). V horní nabídce vyberte **Filtr adresář + odběr** a pak zvolte adresář, který obsahuje vašeho TENANTA Azure AD.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Registrace aplikací**.
1. Vyberte **Nová registrace**.
1. Zadejte **název** vaší aplikace. Například `Azure AD B2C App`.
1. Přijměte výchozí výběr **účtů v tomto organizačním adresáři pouze** pro tuto aplikaci.
1. Pro **identifikátor URI přesměrování** přijměte hodnotu **Web** a zadejte následující adresu URL na všechna malá písmena, kde `your-B2C-tenant-name` se nahradí názvem vašeho tenanta Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Například `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Vyberte **Zaregistrovat**. Poznamenejte si **ID aplikace (klienta)** pro použití v pozdějším kroku.
1. Vyberte **certifikáty & tajných** kódů a pak vyberte **nový tajný klíč klienta**.
1. Zadejte **Popis** tajného kódu, vyberte vypršení platnosti a pak vyberte **Přidat**. Poznamenejte si **hodnotu** tajného kódu pro použití v pozdějším kroku.

### <a name="configuring-optional-claims"></a>Konfigurace volitelných deklarací identity

Pokud chcete získat `family_name` `given_name` deklarace identity a ze služby Azure AD, můžete nakonfigurovat volitelné deklarace identity pro aplikaci v uživatelském rozhraní Azure Portal nebo manifestu aplikace. Další informace najdete v tématu [jak poskytnout volitelné deklarace identity vaší aplikaci Azure AD](../active-directory/develop/active-directory-optional-claims.md).

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). Vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Registrace aplikací**.
1. V seznamu vyberte aplikaci, pro kterou chcete nakonfigurovat volitelné deklarace identity.
1. V části **Správa** vyberte **Konfigurace tokenu**.
1. Vyberte **přidat volitelnou deklaraci identity**.
1. Jako **typ tokenu** vyberte **ID**.
1. Vyberte volitelné deklarace identity, které chcete přidat, `family_name` a `given_name` .
1. Klikněte na **Přidat**.

::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurace Azure AD jako zprostředkovatele identity

1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity** a potom vyberte **Nový poskytovatel OpenID Connect**.
1. Zadejte **název**. Zadejte například *Contoso Azure AD*.
1. V poli **Adresa URL metadat** zadejte následující adresu URL, která nahrazuje `{tenant}` název domény vašeho tenanta Azure AD:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Například `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.
    Například `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration`.

1. Jako **ID klienta** zadejte ID aplikace, které jste si poznamenali dříve.
1. Jako **tajný klíč klienta** zadejte tajný klíč klienta, který jste předtím nahráli.
1. Pro **Rozsah** zadejte `openid profile` .
1. Ponechte výchozí hodnoty pro **typ odpovědi** a **režim odezvy**.
1. Volitelné Jako **nápovědu k doméně** zadejte `contoso.com` . Další informace najdete v tématu [Nastavení přímého přihlašování pomocí Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. V části **mapování deklarací identity zprostředkovatele identity** vyberte následující deklarace identity:

    - **ID uživatele**: *OID*
    - **Zobrazovaný název**: *název*
    - **Křestní jméno**: *given_name*
    - **Příjmení**: *family_name*
    - **E-mail**: *preferred_username*

1. Vyberte **Uložit**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Je potřeba uložit klíč aplikace, který jste vytvořili v tenantovi Azure AD B2C.

1. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte **Filtr adresář + odběr** a pak zvolte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. V části **zásady** vyberte **Architektura prostředí identity**.
1. Vyberte **klíče zásad** a pak vyberte **Přidat**.
1. Pro **Možnosti** vyberte možnost `Manual` .
1. Zadejte **název** klíče zásad. Například `ContosoAppSecret`.  Předpona `B2C_1A_` se automaticky přidá do názvu klíče při jeho vytvoření, takže jeho odkaz v XML v následující části je *B2C_1A_ContosoAppSecret*.
1. Do **tajného klíče** zadejte tajný klíč klienta, který jste si poznamenali dříve.
1. Pro **použití klíče** vyberte `Signature` .
1. Vyberte **Vytvořit**.

## <a name="add-a-claims-provider"></a>Přidat zprostředkovatele deklarací identity

Pokud chcete, aby se uživatelé přihlásili pomocí Azure AD, je třeba definovat Azure AD jako zprostředkovatele deklarací identity, se kterým Azure AD B2C může komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací, které používá Azure AD B2C k ověření, že konkrétní uživatel byl ověřen.

Službu Azure AD můžete definovat jako zprostředkovatele deklarací přidáním služby Azure AD do prvku **ClaimsProvider** v souboru rozšíření zásady.

1. Otevřete soubor *TrustFrameworkExtensions.xml* .
2. Vyhledejte element **ClaimsProviders** . Pokud neexistuje, přidejte jej pod kořenový element.
3. Přidejte nový **ClaimsProvider** následujícím způsobem:
    ```xml
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="OIDC-Contoso">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. V rámci elementu **ClaimsProvider** aktualizujte hodnotu pro **doménu** na jedinečnou hodnotu, která se dá použít k odlišení od jiných zprostředkovatelů identity. Příklad: `Contoso`. Neumísťujete na `.com` konec tohoto nastavení domény.
5. V rámci elementu **ClaimsProvider** aktualizujte hodnotu vlastnosti **DisplayName** na popisný název zprostředkovatele deklarací. Tato hodnota se aktuálně nepoužívá.

### <a name="update-the-technical-profile"></a>Aktualizace technického profilu

Pokud chcete získat token z koncového bodu Azure AD, musíte definovat protokoly, které Azure AD B2C by měly používat ke komunikaci se službou Azure AD. To se provádí v rámci **TechnicalProfile** elementu  **ClaimsProvider**.

1. Aktualizujte ID elementu **TechnicalProfile** . Toto ID se používá k odkazování na tento technický profil z jiných částí zásad, například `OIDC-Contoso` .
1. Aktualizujte hodnotu vlastnosti **DisplayName**. Tato hodnota se zobrazí na tlačítku pro přihlášení na přihlašovací obrazovce.
1. Aktualizujte hodnotu pro **Popis**.
1. Azure AD používá protokol OpenID Connect, takže se ujistěte, že hodnota pro **protokol** je `OpenIdConnect` .
1. Nastavte hodnotu **metadat** na `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration` , kde `tenant-name` je název vašeho tenanta Azure AD. Například `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.
1. Nastavte **client_id** na ID aplikace z registrace aplikace.
1. V části **CryptographicKeys** aktualizujte hodnotu **StorageReferenceId** na název klíče zásad, který jste vytvořili dříve. Například `B2C_1A_ContosoAppSecret`.

### <a name="upload-the-extension-file-for-verification"></a>Nahrajte soubor rozšíření pro ověření.

Teď jste nakonfigurovali zásady tak, aby Azure AD B2C vědět, jak komunikovat s adresářem služby Azure AD. Zkuste nahrát soubor s příponou zásady jenom tak, aby se ověřilo, že zatím nemá žádné problémy.

1. Na stránce **vlastní zásady** ve vašem tenantovi Azure AD B2C vyberte **Odeslat zásadu**.
1. Pokud existuje, zapněte **zásadu přepsat** a pak vyhledejte a vyberte soubor *TrustFrameworkExtensions.xml* .
1. Klikněte na **Odeslat**.

## <a name="register-the-claims-provider"></a>Registrace zprostředkovatele deklarací identity

V tuto chvíli se poskytovatel identity nastavil, ale ještě není dostupný na žádném z přihlašovacích a přihlašovacích stránek. Aby byl k dispozici, vytvořte duplikát stávající cesty uživatele šablony a pak ho upravte, aby měl také poskytovatele identity Azure AD:

1. Otevřete soubor *TrustFrameworkBase.xml* z úvodní sady.
1. Vyhledejte a zkopírujte celý obsah prvku **UserJourney** , který obsahuje `Id="SignUpOrSignIn"` .
1. Otevřete *TrustFrameworkExtensions.xml* a vyhledejte element **userjourney** . Pokud element neexistuje, přidejte jej.
1. Vložte celý obsah elementu **UserJourney** , který jste zkopírovali jako podřízený prvek **userjourney** elementu.
1. Přejmenujte ID cesty pro uživatele. Například `SignUpSignInContoso`.

### <a name="display-the-button"></a>Zobrazit tlačítko

Element **claimsproviderselection.** se podobá tlačítku poskytovatele identity na stránce pro registraci nebo přihlášení. Pokud přidáte element **claimsproviderselection.** pro Azure AD, zobrazí se nové tlačítko, když se uživatel na stránce zařadí.

1. Vyhledejte element **OrchestrationStep** , který obsahuje `Order="1"` cestu uživatele, kterou jste vytvořili v *TrustFrameworkExtensions.xml*.
1. Pod **ClaimsProviderSelections** přidejte následující element. Nastavte hodnotu **TargetClaimsExchangeId** na odpovídající hodnotu, například `ContosoExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Propojit tlačítko s akcí

Teď, když máte tlačítko na místě, musíte ho propojit s akcí. Tato akce je v tomto případě určena pro Azure AD B2C ke komunikaci se službou Azure AD za účelem získání tokenu. Propojte tlačítko s akcí tak, že propojíte technický profil pro poskytovatele deklarací identity Azure AD:

1. Najděte **OrchestrationStep** , který obsahuje `Order="2"` cestu k uživateli.
1. Přidejte následující prvek **ClaimsExchange** a ujistěte se, že používáte stejnou hodnotu pro **ID** , které jste použili pro **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="OIDC-Contoso" />
    ```

    Aktualizujte hodnotu **TechnicalProfileReferenceId** na **ID** technického profilu, který jste vytvořili dříve. Například `OIDC-Contoso`.

1. Uložte soubor *TrustFrameworkExtensions.xml* a znovu ho nahrajte pro účely ověření.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-azure-ad-identity-provider-to-a-user-flow"></a>Přidání poskytovatele identity Azure AD do toku uživatele 

1. Ve vašem tenantovi Azure AD B2C vyberte **toky uživatelů**.
1. Klikněte na tok uživatele, který chcete přidat poskytovatele identity Azure AD.
1. V části **Zprostředkovatelé sociální identity** vyberte **Contoso Azure AD**.
1. Vyberte **Uložit**.
1. Pokud chcete zásady testovat, vyberte **Spustit tok uživatele**.
1. V poli **aplikace** vyberte webovou aplikaci s názvem *testapp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Klikněte na **Spustit tok uživatele** .

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="update-and-test-the-relying-party-file"></a>Aktualizace a testování souboru předávající strany

Aktualizujte soubor předávající strany (RP), který iniciuje cestu uživatele, kterou jste vytvořili.

1. Vytvořte kopii *SignUpOrSignIn.xml* v pracovním adresáři a přejmenujte ji. Přejmenujte ho například na *SignUpSignInContoso.xml*.
1. Otevřete nový soubor a aktualizujte hodnotu atributu **PolicyId** pro **TrustFrameworkPolicy** s jedinečnou hodnotou. Například `SignUpSignInContoso`.
1. Aktualizujte hodnotu **PUBLICPOLICYURI** identifikátorem URI pro zásadu. Například `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Aktualizujte hodnotu atributu **ReferenceId** v **DefaultUserJourney** tak, aby odpovídala ID cesty uživatele, kterou jste vytvořili dříve. Například *SignUpSignInContoso*.
1. Uložte změny a odešlete soubor.
1. V části **vlastní zásady** vyberte v seznamu novou zásadu.
1. V rozevíracím seznamu **Vybrat aplikaci** vyberte aplikaci Azure AD B2C, kterou jste vytvořili dříve. Například *testapp1*.
1. Zkopírujte **koncový bod spustit nyní** a otevřete ho v privátním okně prohlížeče, například v anonymním režimu v Google Chrome nebo v okně InPrivate na Microsoft Edge. Otevření v privátním okně prohlížeče vám umožní testovat celou cestu uživatele, protože nepoužívá žádná z aktuálně uložených přihlašovacích údajů Azure AD.
1. Vyberte tlačítko pro přihlášení ke službě Azure AD, například *Zaměstnanec společnosti Contoso*, a potom zadejte přihlašovací údaje uživatele do svého tenanta organizace Azure AD. Zobrazí se výzva k autorizaci aplikace a zadání informací pro váš profil.

Pokud je proces přihlášení úspěšný, je váš prohlížeč přesměrován na `https://jwt.ms` , který zobrazuje obsah tokenu vrácený Azure AD B2C.

## <a name="next-steps"></a>Další kroky

Při práci s vlastními zásadami můžete někdy potřebovat další informace při řešení potíží se zásadami během jejího vývoje.

Abychom vám pomohli diagnostikovat problémy, můžete dočasně umístit zásady do režimu pro vývojáře a shromažďovat protokoly pomocí Application Insights Azure. Zjistěte, jak [Azure Active Directory B2C: shromažďování protokolů](troubleshoot-with-application-insights.md).

::: zone-end