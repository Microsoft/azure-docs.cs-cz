---
title: Nastavení přihlášení pomocí účtu Azure AD pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Nastavte přihlášení pomocí účtu Azure Active Directory v Azure Active Directory B2C pomocí vlastních zásad.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 06ba565de8ca24c8c0baa576b74e70035384be09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388421"
---
# <a name="set-up-sign-in-with-an-azure-active-directory-account-using-custom-policies-in-azure-active-directory-b2c"></a>Nastavení přihlašování pomocí účtu Azure Active Directory s využitím vlastních zásad v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku se dozvíte, jak povolit přihlášení uživatelů z Azure Active Directory (Azure AD) pomocí [vlastních zásad](custom-policy-overview.md) v Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v části Začínáme [s vlastními zásadami v Azure Active Directory B2C](custom-policy-get-started.md).


[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Je potřeba uložit klíč aplikace, který jste vytvořili v tenantovi Azure AD B2C.

1. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte **Filtr adresář + odběr** a pak zvolte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. V části **zásady**vyberte **Architektura prostředí identity**.
1. Vyberte **klíče zásad** a pak vyberte **Přidat**.
1. Pro **Možnosti**vyberte možnost `Manual` .
1. Zadejte **název** klíče zásad. Například, `ContosoAppSecret`.  Předpona `B2C_1A_` se automaticky přidá do názvu klíče při jeho vytvoření, takže jeho odkaz v XML v následující části je *B2C_1A_ContosoAppSecret*.
1. Do **tajného klíče**zadejte tajný klíč klienta, který jste si poznamenali dříve.
1. Pro **použití klíče**vyberte `Signature` .
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
1. V části **CryptographicKeys**aktualizujte hodnotu **StorageReferenceId** na název klíče zásad, který jste vytvořili dříve. Například, `B2C_1A_ContosoAppSecret`.

### <a name="upload-the-extension-file-for-verification"></a>Nahrajte soubor rozšíření pro ověření.

Teď jste nakonfigurovali zásady tak, aby Azure AD B2C vědět, jak komunikovat s adresářem služby Azure AD. Zkuste nahrát soubor s příponou zásady jenom tak, aby se ověřilo, že zatím nemá žádné problémy.

1. Na stránce **vlastní zásady** ve vašem tenantovi Azure AD B2C vyberte **Odeslat zásadu**.
1. Pokud existuje, zapněte **zásadu přepsat**a pak vyhledejte a vyberte soubor *TrustFrameworkExtensions.xml* .
1. Klikněte na **Odeslat**.

## <a name="register-the-claims-provider"></a>Registrace zprostředkovatele deklarací identity

V tuto chvíli se poskytovatel identity nastavil, ale ještě není dostupný na žádném z přihlašovacích a přihlašovacích stránek. Aby byl k dispozici, vytvořte duplikát stávající cesty uživatele šablony a pak ho upravte, aby měl také poskytovatele identity Azure AD:

1. Otevřete soubor *TrustFrameworkBase.xml* z úvodní sady.
1. Vyhledejte a zkopírujte celý obsah prvku **UserJourney** , který obsahuje `Id="SignUpOrSignIn"` .
1. Otevřete *TrustFrameworkExtensions.xml* a vyhledejte element **userjourney** . Pokud element neexistuje, přidejte jej.
1. Vložte celý obsah elementu **UserJourney** , který jste zkopírovali jako podřízený prvek **userjourney** elementu.
1. Přejmenujte ID cesty pro uživatele. Například, `SignUpSignInContoso`.

### <a name="display-the-button"></a>Zobrazit tlačítko

Element **claimsproviderselection.** se podobá tlačítku poskytovatele identity na stránce pro registraci nebo přihlášení. Pokud přidáte element **claimsproviderselection.** pro Azure AD, zobrazí se nové tlačítko, když se uživatel na stránce zařadí.

1. Vyhledejte element **OrchestrationStep** , který obsahuje `Order="1"` cestu uživatele, kterou jste vytvořili v *TrustFrameworkExtensions.xml*.
1. Pod **ClaimsProviderSelections**přidejte následující element. Nastavte hodnotu **TargetClaimsExchangeId** na odpovídající hodnotu, například `ContosoExchange` :

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

    Aktualizujte hodnotu **TechnicalProfileReferenceId** na **ID** technického profilu, který jste vytvořili dříve. Například, `OIDC-Contoso`.

1. Uložte soubor *TrustFrameworkExtensions.xml* a znovu ho nahrajte pro účely ověření.

## <a name="create-an-azure-ad-b2c-application"></a>Vytvoření aplikace Azure AD B2C

Komunikace s Azure AD B2C probíhá prostřednictvím aplikace, kterou zaregistrujete v tenantovi B2C. V této části jsou uvedeny volitelné kroky, které můžete provést, chcete-li vytvořit testovací aplikaci, pokud jste tak již neučinili.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aktualizace a testování souboru předávající strany

Aktualizujte soubor předávající strany (RP), který iniciuje cestu uživatele, kterou jste vytvořili.

1. Vytvořte kopii *SignUpOrSignIn.xml* v pracovním adresáři a přejmenujte ji. Přejmenujte ho například na *SignUpSignInContoso.xml*.
1. Otevřete nový soubor a aktualizujte hodnotu atributu **PolicyId** pro **TrustFrameworkPolicy** s jedinečnou hodnotou. Například, `SignUpSignInContoso`.
1. Aktualizujte hodnotu **PUBLICPOLICYURI** identifikátorem URI pro zásadu. Například, `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Aktualizujte hodnotu atributu **ReferenceId** v **DefaultUserJourney** tak, aby odpovídala ID cesty uživatele, kterou jste vytvořili dříve. Například *SignUpSignInContoso*.
1. Uložte změny a odešlete soubor.
1. V části **vlastní zásady**vyberte v seznamu novou zásadu.
1. V rozevíracím seznamu **Vybrat aplikaci** vyberte aplikaci Azure AD B2C, kterou jste vytvořili dříve. Například *testapp1*.
1. Zkopírujte **koncový bod spustit nyní** a otevřete ho v privátním okně prohlížeče, například v anonymním režimu v Google Chrome nebo v okně InPrivate na Microsoft Edge. Otevření v privátním okně prohlížeče vám umožní testovat celou cestu uživatele, protože nepoužívá žádná z aktuálně uložených přihlašovacích údajů Azure AD.
1. Vyberte tlačítko pro přihlášení ke službě Azure AD, například *Zaměstnanec společnosti Contoso*, a potom zadejte přihlašovací údaje uživatele do svého tenanta organizace Azure AD. Zobrazí se výzva k autorizaci aplikace a zadání informací pro váš profil.

Pokud je proces přihlášení úspěšný, je váš prohlížeč přesměrován na `https://jwt.ms` , který zobrazuje obsah tokenu vrácený Azure AD B2C.

## <a name="next-steps"></a>Další kroky

Při práci s vlastními zásadami můžete někdy potřebovat další informace při řešení potíží se zásadami během jejího vývoje.

Abychom vám pomohli diagnostikovat problémy, můžete dočasně umístit zásady do režimu pro vývojáře a shromažďovat protokoly pomocí Application Insights Azure. Zjistěte, jak [Azure Active Directory B2C: shromažďování protokolů](troubleshoot-with-application-insights.md).
