---
title: Nastavení přihlášení pomocí účtu Azure AD pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Nastavte přihlášení pomocí účtu Azure Active Directory ve službě Azure Active Directory B2C pomocí vlastních zásad.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: be3a7a3ce4ce3a06398436058ea5d4d935ef5a5c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678092"
---
# <a name="set-up-sign-in-with-an-azure-active-directory-account-using-custom-policies-in-azure-active-directory-b2c"></a>Nastavení přihlášení pomocí účtu Azure Active Directory pomocí vlastních zásad ve službě Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek ukazuje, jak povolit přihlášení pro uživatele z organizace Azure Active Directory (Azure AD) pomocí [vlastních zásad](custom-policy-overview.md) v Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Požadavky

Dokončete kroky v [části Začínáme s vlastními zásadami ve službě Azure Active Directory B2C](custom-policy-get-started.md).


[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Musíte uložit klíč aplikace, který jste vytvořili v tenantovi Azure AD B2C.

1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C. V horní nabídce vyberte **filtr Directory + subscription** a pak zvolte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. V části **Zásady**vyberte **rozhraní Identity Experience Framework**.
1. Vyberte **klávesy zásad a** pak vyberte **Přidat**.
1. V části `Manual` **Možnosti**zvolte .
1. Zadejte **název** klíče zásady. Například, `ContosoAppSecret`.  Předpona `B2C_1A_` se při vytvoření automaticky přidá k názvu klíče, takže její odkaz ve formátu XML v následující části je *B2C_1A_ContosoAppSecret*.
1. V **poli Tajné**zadejte tajný klíč klienta, který jste nahráli dříve.
1. V případě použití `Signature` **klíče**vyberte .
1. Vyberte **Vytvořit**.

## <a name="add-a-claims-provider"></a>Přidání poskytovatele deklarací identity

Pokud chcete, aby se uživatelé přihlašovali pomocí Azure AD, musíte definovat Azure AD jako poskytovatele deklarací identity, se kterým může Azure AD B2C komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací, které používá Azure AD B2C k ověření, že konkrétní uživatel byl ověřen.

Azure AD můžete definovat jako poskytovatele deklarací identity přidáním Azure AD do prvku **ClaimsProvider** v souboru rozšíření zásad.

1. Otevřete soubor *TrustFrameworkExtensions.xml.*
2. Najít **ClaimsProviders** element. Pokud neexistuje, přidejte jej pod kořenový prvek.
3. Přidejte nového **Zprostředkovatele deklarací** identity takto:
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

4. V rámci **ClaimsProvider** element, aktualizovat hodnotu **domény** na jedinečnou hodnotu, která lze odlišit od jiných poskytovatelů identity. Například `Contoso`. Na konec tohoto `.com` nastavení domény se nevložíte.
5. V části **ClaimsProvider** aktualizujte hodnotu **pro DisplayName** na popisný název zprostředkovatele deklarací. Tato hodnota není aktuálně používána.

### <a name="update-the-technical-profile"></a>Aktualizace technického profilu

Chcete-li získat token z koncového bodu Azure AD, musíte definovat protokoly, které by měl Azure AD B2C použít ke komunikaci s Azure AD. To se provádí uvnitř **prvku TechnicalProfile** **claimsprovider**.

1. Aktualizujte ID prvku **TechnicalProfile.** Toto ID se používá k označení tohoto technického profilu `OIDC-Contoso`z jiných částí zásady, například .
1. Aktualizujte hodnotu pro **DisplayName**. Tato hodnota se zobrazí na přihlašovacím tlačítku na přihlašovací obrazovce.
1. Aktualizujte hodnotu pro **Popis**.
1. Azure AD používá protokol OpenID Connect, takže **Protocol** se `OpenIdConnect`ujistěte, že hodnota protokolu je .
1. Nastavte hodnotu **metadata** `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration`na `tenant-name` , kde je název klienta Azure AD. Například `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.
1. Nastavte **client_id** na ID aplikace z registrace aplikace.
1. V části **CryptographicKeys**aktualizujte hodnotu **StorageReferenceId** na název klíče zásady, který jste vytvořili dříve. Například, `B2C_1A_ContosoAppSecret`.

### <a name="upload-the-extension-file-for-verification"></a>Nahrát soubor rozšíření pro ověření

Teď jste nakonfigurovali zásady tak, aby Azure AD B2C ví, jak komunikovat s adresářem Azure AD. Zkuste nahrát soubor rozšíření zásad, abyste potvrdili, že zatím nemá žádné problémy.

1. Na stránce **Vlastní zásady** v tenantovi Azure AD B2C vyberte **Zásady nahrávání**.
1. Povolte **Přepište zásadu, pokud existuje**, a potom vyhledejte soubor *TrustFrameworkExtensions.xml* a vyberte jej.
1. Klikněte na **Odeslat**.

## <a name="register-the-claims-provider"></a>Registrace zprostředkovatele deklarací identity

V tomto okamžiku byl nastaven zprostředkovatel identity, ale ještě není k dispozici na žádné ze stránek pro přihlášení nebo přihlášení. Chcete-li ji zpřístupnit, vytvořte duplikát existující cesty uživatele šablony a pak ji upravte tak, aby měla také poskytovatele identity Azure AD:

1. Otevřete soubor *TrustFrameworkBase.xml* ze startovního balíčku.
1. Vyhledejte a zkopírujte celý obsah prvku `Id="SignUpOrSignIn"` **UserJourney,** který obsahuje .
1. Otevřete soubor *TrustFrameworkExtensions.xml* a vyhledejte element **UserJourneys.** Pokud prvek neexistuje, přidejte jeden.
1. Vložte celý obsah **UserJourney** prvek, který jste zkopírovali jako podřízený **UserJourneys** element.
1. Přejmenujte ID cesty uživatele. Například, `SignUpSignInContoso`.

### <a name="display-the-button"></a>Zobrazení tlačítka

**ClaimsProviderSelection** Prvek je obdobou tlačítko zprostředkovatele identity na stránce registrace nebo přihlášení. Pokud přidáte **ClaimsProviderSelection** prvek pro Azure AD, nové tlačítko se zobrazí, když uživatel přistane na stránce.

1. Najděte element **OrchestrationStep,** který zahrnuje `Order="1"` cestu uživatele, kterou jste vytvořili v *souboru TrustFrameworkExtensions.xml*.
1. V části **ClaimsProviderSelections**přidejte následující prvek. Nastavte hodnotu **TargetClaimsExchangeId** na příslušnou hodnotu, například `ContosoExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Propojení tlačítka s akcí

Nyní, když máte tlačítko na místě, musíte jej propojit s akcí. Akce, v tomto případě je pro Azure AD B2C komunikovat s Azure AD přijímat token. Propojte tlačítko s akcí propojením technického profilu pro poskytovatele deklarací identity Azure AD:

1. Najít **OrchestrationStep,** `Order="2"` který zahrnuje v cestě uživatele.
1. Přidejte následující **claimsexchange** prvek ujistěte se, že používáte stejnou hodnotu pro **ID,** které jste použili pro **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="OIDC-Contoso" />
    ```

    Aktualizujte hodnotu **TechnicalProfileReferenceId** na **ID** technického profilu, který jste vytvořili dříve. Například, `OIDC-Contoso`.

1. Uložte soubor *TrustFrameworkExtensions.xml* a znovu jej nahrajte k ověření.

## <a name="create-an-azure-ad-b2c-application"></a>Vytvoření aplikace Azure AD B2C

Komunikace s Azure AD B2C probíhá prostřednictvím aplikace, kterou zaregistrujete ve vašem tenantovi B2C. V této části jsou uvedeny volitelné kroky, které můžete provést a vytvořit testovací aplikaci, pokud jste tak ještě neučinili.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aktualizace a testování souboru předávající strany

Aktualizujte soubor předávající strany (RP), který iniciuje cestu uživatele, kterou jste vytvořili.

1. Vytvořte kopii *souboru SignUpOrSignIn.xml* ve svém pracovním adresáři a přejmenujte ji. Přejmenujte jej například na *SignUpSignInContoso.xml*.
1. Otevřete nový soubor a aktualizujte hodnotu atributu **PolicyId** pro **TrustFrameworkPolicy** s jedinečnou hodnotou. Například, `SignUpSignInContoso`.
1. Aktualizujte hodnotu **PublicPolicyUri** pomocí identifikátoru URI pro zásadu. Například, `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Aktualizujte hodnotu atributu **ReferenceId** v **DefaultUserJourney** tak, aby odpovídalo ID cesty uživatele, kterou jste vytvořili dříve. Například *SignUpSignInContoso*.
1. Uložte změny a nahrajte soubor.
1. V části **Vlastní zásady**vyberte novou zásadu v seznamu.
1. V rozevíracím souboru **Select aplikace** vyberte aplikaci Azure AD B2C, kterou jste vytvořili dříve. Například *testapp1*.
1. Zkopírujte **koncový bod Spustit nyní** a otevřete jej v okně soukromého prohlížeče, například v anonymním režimu v prohlížeči Google Chrome nebo v okně InPrivate v prohlížeči Microsoft Edge. Otevření v okně soukromého prohlížeče umožňuje otestovat celou cestu uživatele tím, že nepoužíváte žádné aktuálně uložené přihlašovací údaje Azure AD.
1. Vyberte přihlašovací tlačítko Azure AD, například *Contoso Employee*, a zadejte přihlašovací údaje pro uživatele ve vašem tenantovi organizace Azure AD. Budete vyzváni k autorizaci přihlášky a zadání informací pro svůj profil.

Pokud je proces přihlášení úspěšný, váš prohlížeč `https://jwt.ms`je přesměrován na , který zobrazuje obsah tokenu vráceného Azure AD B2C.

## <a name="next-steps"></a>Další kroky

Při práci s vlastními zásadami může někdy potřebovat další informace při řešení potíží se zásadami během jejího vývoje.

Chcete-li diagnostikovat problémy, můžete dočasně umístit zásady do "režimu pro vývojáře" a shromažďovat protokoly pomocí Azure Application Insights. Zjistěte, jak ve [službě Azure Active Directory B2C: Shromažďování protokolů](troubleshoot-with-application-insights.md).
