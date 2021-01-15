---
title: Nastavení registrace a přihlášení pomocí účtu Twitteru
titleSuffix: Azure AD B2C
description: Poskytněte zákazníkům registraci a přihlášení k účtům Twitteru ve vašich aplikacích pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a0f209e0ac17c62378d279a32f4a27f48a9f74bd
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2021
ms.locfileid: "98232688"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Nastavte si registraci a přihlaste se pomocí účtu Twitteru pomocí Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]
::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-application"></a>Vytvoření aplikace

Pokud chcete povolit přihlášení uživatelům s účtem Twitteru v Azure Active Directory B2C (Azure AD B2C), musíte vytvořit aplikaci Twitter. Pokud ještě nemáte účet na Twitteru, můžete se zaregistrovat [https://twitter.com/signup](https://twitter.com/signup) . Musíte se také [použít pro vývojářský účet](https://developer.twitter.com/en/apply/user.html). Další informace najdete v tématu [použití pro přístup](https://developer.twitter.com/en/apply-for-access).

1. Přihlaste se k [portálu pro vývojáře na Twitteru](https://developer.twitter.com/portal/projects-and-apps) pomocí přihlašovacích údajů k účtu Twitteru.
1. V části **samostatné aplikace** vyberte **+ vytvořit aplikaci**.
1. Zadejte **název aplikace** a pak vyberte **Dokončit**.
1. Zkopírujte hodnotu **klíče aplikace** a klíč **rozhraní API**.  Pomocí obou z nich můžete nakonfigurovat Twitter jako poskytovatele identity ve vašem tenantovi. 
1. V části **nastavení aplikace** vyberte **nastavení aplikace**.
1. V části **nastavení ověřování** vyberte **Upravit** .
    1. Zaškrtněte políčko **Povolit 3 – legged OAuth** .
    1. Zaškrtněte políčko pro **zadání e-mailové adresy uživatele** .
    1. Jako **adresu URL zpětného volání** zadejte `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp` . Nahraďte `your-tenant` názvem vašeho tenanta a `your-user-flow-Id` identifikátorem toku uživatele. Například `b2c_1A_signup_signin_twitter`. Při zadávání názvu tenanta a ID toku uživatele je potřeba použít malá písmena, i když jsou v Azure AD B2C definované velkými písmeny.
    1. Jako **adresu URL webu** zadejte `https://your-tenant.b2clogin.com` . Nahraďte `your-tenant` názvem vašeho tenanta. Například `https://contosob2c.b2clogin.com`.
    1. Zadejte adresu URL pro **účely podmínek služby**, například `http://www.contoso.com/tos` . Adresa URL zásad je stránka, kterou udržujete pro poskytování podmínek a ujednání pro vaši aplikaci.
    1. Zadejte adresu URL pro **Zásady ochrany osobních údajů**, například `http://www.contoso.com/privacy` . Adresa URL zásad je stránka, kterou udržujete pro poskytování informací o ochraně osobních údajů pro vaši aplikaci.
    1. Vyberte **Uložit**.

::: zone pivot="b2c-user-flow"

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Konfigurace Twitteru jako poskytovatele identity ve vašem tenantovi

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity** a pak vyberte **Twitter**.
1. Zadejte **název**. Například *Twitter*.
1. Pro **ID klienta** zadejte *klíč rozhraní API* aplikace Twitter, kterou jste vytvořili dříve.
1. Pro **tajný klíč klienta** zadejte *tajný kód klíče rozhraní API* , který jste si poznamenali.
1. Vyberte **Uložit**.

## <a name="add-twitter-identity-provider-to-a-user-flow"></a>Přidání poskytovatele identity Twitteru do toku uživatele 

1. Ve vašem tenantovi Azure AD B2C vyberte **toky uživatelů**.
1. Vyberte tok uživatele, který chcete přidat poskytovatele identity Twitteru.
1. V části **Zprostředkovatelé sociální identity** vyberte **Twitter**.
1. Vyberte **Uložit**.
1. Pokud chcete zásady testovat, vyberte **Spustit tok uživatele**.
1. V poli **aplikace** vyberte webovou aplikaci s názvem *testapp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Klikněte na **Spustit tok uživatele** .

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Je potřeba uložit tajný klíč, který jste předtím nahráli ve svém tenantovi Azure AD B2C.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Na stránce Přehled vyberte možnost **Architektura prostředí identity**.
5. Vyberte **klíče zásad** a pak vyberte **Přidat**.
6. Pro **Možnosti** vyberte možnost `Manual` .
7. Zadejte **název** klíče zásad. Například `TwitterSecret`. Předpona `B2C_1A_` se automaticky přidá do názvu vašeho klíče.
8. Do **tajného klíče** zadejte tajný klíč klienta, který jste předtím nahráli.
9. Pro **použití klíče** vyberte `Encryption` .
10. Klikněte na **Vytvořit**.

## <a name="add-a-claims-provider"></a>Přidat zprostředkovatele deklarací identity

Pokud chcete, aby se uživatelé přihlásili pomocí účtu Twitteru, musíte účet definovat jako zprostředkovatele deklarací identity, se kterým Azure AD B2C můžou komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací, které používá Azure AD B2C k ověření, že konkrétní uživatel byl ověřen.

Můžete definovat účet Twitter jako zprostředkovatele deklarací, a to tak, že ho přidáte do prvku **ClaimsProviders** v souboru rozšíření zásady.

1. Otevřete *TrustFrameworkExtensions.xml*.
2. Vyhledejte element **ClaimsProviders** . Pokud neexistuje, přidejte jej pod kořenový element.
3. Přidejte nový **ClaimsProvider** následujícím způsobem:

    ```xml
    <ClaimsProvider>
      <Domain>twitter.com</Domain>
      <DisplayName>Twitter</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAUTH1">
          <DisplayName>Twitter</DisplayName>
          <Protocol Name="OAuth1" />
          <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application API key</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
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

4. Hodnotu **client_id** nahraďte *tajným klíčem rozhraní API* , který jste předtím nahráli.
5. Soubor uložte.

### <a name="upload-the-extension-file-for-verification"></a>Nahrajte soubor rozšíření pro ověření.

Teď jste nakonfigurovali zásady tak, aby Azure AD B2C vědět, jak komunikovat s vaším účtem Twitteru. Zkuste nahrát soubor s příponou zásady jenom tak, aby se ověřilo, že zatím nemá žádné problémy.

1. Na stránce **vlastní zásady** ve vašem tenantovi Azure AD B2C vyberte **Odeslat zásadu**.
2. Pokud existuje, zapněte **zásadu přepsat** a pak vyhledejte a vyberte soubor *TrustFrameworkExtensions.xml* .
3. Klikněte na **Odeslat**.

## <a name="register-the-claims-provider"></a>Registrace zprostředkovatele deklarací identity

V tuto chvíli je poskytovatel identity nastavený, ale není k dispozici na žádném z přihlašovacích obrazovek pro registraci nebo přihlášení. Aby byl k dispozici, vytvořte duplikát existující cesty uživatele šablony a pak ji upravte, aby měl také poskytovatele identity Twitteru.

1. Otevřete soubor *TrustFrameworkBase.xml* z úvodní sady.
2. Vyhledejte a zkopírujte celý obsah prvku **UserJourney** , který obsahuje `Id="SignUpOrSignIn"` .
3. Otevřete *TrustFrameworkExtensions.xml* a vyhledejte element **userjourney** . Pokud element neexistuje, přidejte jej.
4. Vložte celý obsah elementu **UserJourney** , který jste zkopírovali jako podřízený prvek **userjourney** elementu.
5. Přejmenujte ID cesty pro uživatele. Například `SignUpSignInTwitter`.

### <a name="display-the-button"></a>Zobrazit tlačítko

Element **claimsproviderselection.** se podobá tlačítku poskytovatele identity na obrazovce pro registraci nebo přihlášení. Pokud přidáte element **claimsproviderselection.** pro účet Twitteru, zobrazí se nové tlačítko, když se uživatel na stránce zařadí.

1. Vyhledejte element **OrchestrationStep** , který obsahuje `Order="1"` cestu k uživateli, kterou jste vytvořili.
2. Pod **ClaimsProviderSelects** přidejte následující element. Nastavte hodnotu **TargetClaimsExchangeId** na odpovídající hodnotu, například `TwitterExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Propojit tlačítko s akcí

Teď, když máte tlačítko na místě, musíte ho propojit s akcí. Tato akce je v tomto případě určena pro Azure AD B2C ke komunikaci s účtem Twitter pro příjem tokenu.

1. Najděte **OrchestrationStep** , který obsahuje `Order="2"` cestu k uživateli.
2. Přidejte následující prvek **ClaimsExchange** a ujistěte se, že používáte stejnou hodnotu pro ID, které jste použili pro **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    Aktualizujte hodnotu **TechnicalProfileReferenceId** na ID technického profilu, který jste vytvořili dříve. Například `Twitter-OAUTH1`.

3. Uložte soubor *TrustFrameworkExtensions.xml* a znovu ho nahrajte pro účely ověření.

## <a name="update-and-test-the-relying-party-file"></a>Aktualizace a testování souboru předávající strany

Aktualizujte soubor předávající strany (RP), který iniciuje cestu uživatele, kterou jste vytvořili.

1. Vytvořte kopii *SignUpOrSignIn.xml* v pracovním adresáři a přejmenujte ji. Přejmenujte ho například na *SignUpSignInTwitter.xml*.
1. Otevřete nový soubor a aktualizujte hodnotu atributu **PolicyId** pro **TrustFrameworkPolicy** s jedinečnou hodnotou. Například `SignUpSignInTwitter`.
1. Aktualizujte hodnotu **PUBLICPOLICYURI** identifikátorem URI pro zásadu. Například`http://contoso.com/B2C_1A_signup_signin_twitter`
1. Aktualizujte hodnotu atributu **ReferenceId** v **DefaultUserJourney** tak, aby odpovídala ID nové cesty uživatele, kterou jste vytvořili (SignUpSignTwitter).
1. Uložte změny, nahrajte soubor a pak v seznamu vyberte novou zásadu.
1. Ujistěte se, že je vybrána možnost Azure AD B2C aplikace, kterou jste vytvořili v poli **Vybrat aplikaci** , a poté ji otestujte kliknutím na tlačítko **Spustit nyní**.

::: zone-end
