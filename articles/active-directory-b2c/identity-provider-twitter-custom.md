---
title: Nastavení přihlášení pomocí účtu Twitteru pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Nastavte přihlášení pomocí účtu Twitteru pomocí vlastních zásad v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eb402fae083e2c1c57e47aa8ee6f7cef08d5323d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85387962"
---
# <a name="set-up-sign-in-with-a-twitter-account-by-using-custom-policies-in-azure-active-directory-b2c"></a>Nastavte přihlášení pomocí účtu Twitteru pomocí vlastních zásad v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku se dozvíte, jak povolit přihlášení uživatelům účtu Twitteru pomocí [vlastních zásad](custom-policy-overview.md) v Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Požadavky

- Proveďte kroky v části Začínáme [s vlastními zásadami v Azure Active Directory B2C](custom-policy-get-started.md).
- Pokud ještě nemáte účet na Twitteru, vytvořte ho na [stránce pro registraci Twitteru](https://twitter.com/signup).

## <a name="create-an-application"></a>Vytvoření aplikace

Pokud chcete jako poskytovatele identity v Azure AD B2C používat Twitter, musíte vytvořit aplikaci Twitter.

1. Přihlaste se k webu pro [vývojáře na Twitteru](https://developer.twitter.com/en/apps) pomocí přihlašovacích údajů k účtu Twitteru.
2. Vyberte  **vytvořit aplikaci**.
3. Zadejte **název aplikace** a **Popis aplikace**.
4. Do **adresy URL webu**zadejte `https://your-tenant.b2clogin.com` . Nahraďte `your-tenant` názvem vašeho tenanta. Například, `https://contosob2c.b2clogin.com`.
5. Jako **adresu URL zpětného volání**zadejte `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-policy-Id/oauth1/authresp` . Nahraďte `your-tenant` názvem vašeho tenanta a `your-policy-Id` identifikátorem zásady. Například, `b2c_1A_signup_signin_twitter`. Při zadávání názvu tenanta musíte použít malá písmena, i když je tenant definovaný velkými písmeny v Azure AD B2C.
6. V dolní části stránky si přečtěte a přijměte podmínky a pak vyberte **vytvořit**.
7. Na stránce **podrobností aplikace** vyberte **Upravit > upravit podrobnosti**, zaškrtněte políčko **Povolit přihlášení pomocí Twitteru**a potom vyberte **Uložit**.
8. Vyberte **klíče a tokeny** a zaznamenejte **klíč rozhraní API příjemce** a hodnoty **tajného klíče rozhraní API příjemce** , které chcete použít později.

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Je potřeba uložit tajný klíč, který jste předtím nahráli ve svém tenantovi Azure AD B2C.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Na stránce Přehled vyberte možnost **Architektura prostředí identity**.
5. Vyberte **klíče zásad** a pak vyberte **Přidat**.
6. Pro **Možnosti**vyberte možnost `Manual` .
7. Zadejte **název** klíče zásad. Například, `TwitterSecret`. Předpona `B2C_1A_` se automaticky přidá do názvu vašeho klíče.
8. Do **tajného klíče**zadejte tajný klíč klienta, který jste předtím nahráli.
9. Pro **použití klíče**vyberte `Encryption` .
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
            <Item Key="client_id">Your Twitter application consumer key</Item>
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

4. Hodnotu **client_id** nahraďte klíč příjemce, který jste předtím nahráli.
5. Uložte soubor.

### <a name="upload-the-extension-file-for-verification"></a>Nahrajte soubor rozšíření pro ověření.

Teď jste nakonfigurovali zásady tak, aby Azure AD B2C vědět, jak komunikovat s vaším účtem Twitteru. Zkuste nahrát soubor s příponou zásady jenom tak, aby se ověřilo, že zatím nemá žádné problémy.

1. Na stránce **vlastní zásady** ve vašem tenantovi Azure AD B2C vyberte **Odeslat zásadu**.
2. Pokud existuje, zapněte **zásadu přepsat**a pak vyhledejte a vyberte soubor *TrustFrameworkExtensions.xml* .
3. Klikněte na **Odeslat**.

## <a name="register-the-claims-provider"></a>Registrace zprostředkovatele deklarací identity

V tuto chvíli je poskytovatel identity nastavený, ale není k dispozici na žádném z přihlašovacích obrazovek pro registraci nebo přihlášení. Aby byl k dispozici, vytvořte duplikát existující cesty uživatele šablony a pak ji upravte, aby měl také poskytovatele identity Twitteru.

1. Otevřete soubor *TrustFrameworkBase.xml* z úvodní sady.
2. Vyhledejte a zkopírujte celý obsah prvku **UserJourney** , který obsahuje `Id="SignUpOrSignIn"` .
3. Otevřete *TrustFrameworkExtensions.xml* a vyhledejte element **userjourney** . Pokud element neexistuje, přidejte jej.
4. Vložte celý obsah elementu **UserJourney** , který jste zkopírovali jako podřízený prvek **userjourney** elementu.
5. Přejmenujte ID cesty pro uživatele. Například, `SignUpSignInTwitter`.

### <a name="display-the-button"></a>Zobrazit tlačítko

Element **claimsproviderselection.** se podobá tlačítku poskytovatele identity na obrazovce pro registraci nebo přihlášení. Pokud přidáte element **claimsproviderselection.** pro účet Twitteru, zobrazí se nové tlačítko, když se uživatel na stránce zařadí.

1. Vyhledejte element **OrchestrationStep** , který obsahuje `Order="1"` cestu k uživateli, kterou jste vytvořili.
2. Pod **ClaimsProviderSelects**přidejte následující element. Nastavte hodnotu **TargetClaimsExchangeId** na odpovídající hodnotu, například `TwitterExchange` :

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

    Aktualizujte hodnotu **TechnicalProfileReferenceId** na ID technického profilu, který jste vytvořili dříve. Například, `Twitter-OAUTH1`.

3. Uložte soubor *TrustFrameworkExtensions.xml* a znovu ho nahrajte pro účely ověření.

## <a name="create-an-azure-ad-b2c-application"></a>Vytvoření aplikace Azure AD B2C

Komunikace s Azure AD B2C probíhá prostřednictvím aplikace, kterou zaregistrujete v tenantovi B2C. V této části jsou uvedeny volitelné kroky, které můžete provést, chcete-li vytvořit testovací aplikaci, pokud jste tak již neučinili.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aktualizace a testování souboru předávající strany

Aktualizujte soubor předávající strany (RP), který iniciuje cestu uživatele, kterou jste vytvořili.

1. Vytvořte kopii *SignUpOrSignIn.xml* v pracovním adresáři a přejmenujte ji. Přejmenujte ho například na *SignUpSignInTwitter.xml*.
2. Otevřete nový soubor a aktualizujte hodnotu atributu **PolicyId** pro **TrustFrameworkPolicy** s jedinečnou hodnotou. Například, `SignUpSignInTwitter`.
3. Aktualizujte hodnotu **PUBLICPOLICYURI** identifikátorem URI pro zásadu. Například`http://contoso.com/B2C_1A_signup_signin_twitter`
4. Aktualizujte hodnotu atributu **ReferenceId** v **DefaultUserJourney** tak, aby odpovídala ID nové cesty uživatele, kterou jste vytvořili (SignUpSignTwitter).
5. Uložte změny, nahrajte soubor a pak v seznamu vyberte novou zásadu.
6. Ujistěte se, že je vybrána možnost Azure AD B2C aplikace, kterou jste vytvořili v poli **Vybrat aplikaci** , a poté ji otestujte kliknutím na tlačítko **Spustit nyní**.
