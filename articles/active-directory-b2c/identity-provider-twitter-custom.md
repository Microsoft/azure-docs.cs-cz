---
title: Nastavení přihlášení pomocí účtu Twitter pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Nastavte přihlášení pomocí účtu Twitter pomocí vlastních zásad ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5804ded875ef03d7ade4414eb8f08885634748dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051598"
---
# <a name="set-up-sign-in-with-a-twitter-account-by-using-custom-policies-in-azure-active-directory-b2c"></a>Nastavení přihlášení pomocí účtu Twitter pomocí vlastních zásad ve službě Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek ukazuje, jak povolit přihlášení pro uživatele účtu Twitter pomocí [vlastních zásad](custom-policy-overview.md) v Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Požadavky

- Dokončete kroky v [části Začínáme s vlastními zásadami ve službě Azure Active Directory B2C](custom-policy-get-started.md).
- Pokud ještě nemáte účet Twitter, vytvořte si ho na [stránce registrace na Twitteru](https://twitter.com/signup).

## <a name="create-an-application"></a>Vytvoření aplikace

Chcete-li používat Twitter jako poskytovatele identity v Azure AD B2C, musíte vytvořit aplikaci Twitter.

1. Přihlaste se na web [Twitter Developers](https://developer.twitter.com/en/apps) pomocí přihlašovacích údajů k účtu Twitter.
2. Vyberte **Vytvořit aplikaci**.
3. Zadejte **název aplikace** a popis **aplikace**.
4. Do adresy URL `https://your-tenant.b2clogin.com` **webu**zadejte . Nahraďte `your-tenant` se jménem svého tenanta. Například, `https://contosob2c.b2clogin.com`.
5. Adresu **URL zpětného** `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-policy-Id/oauth1/authresp`volání zadejte . Nahraďte `your-tenant` název vašeho klienta a `your-policy-Id` identifikátorem zásad. Například, `b2c_1A_signup_signin_twitter`. Při zadávání názvu klienta musíte použít všechna malá písmena, i když je klient definován velkými písmeny v Azure AD B2C.
6. V dolní části stránky si přečtěte a přijměte podmínky a pak vyberte **Vytvořit**.
7. Na stránce **Podrobnosti o aplikaci** vyberte **Upravit > podrobnosti ,** zaškrtněte políčko **Povolit přihlášení pomocí Twitteru**a pak vyberte **Uložit**.
8. Vyberte **klíče a tokeny** a zaznamenejte **klíč rozhraní API příjemce** a hodnoty **tajných klíčů rozhraní API příjemce,** které se použijí později.

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Musíte uložit tajný klíč, který jste dříve zaznamenali v tenantovi Azure AD B2C.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C. V horní nabídce vyberte filtr **Directory + odběr** a zvolte adresář, který obsahuje vašeho klienta.
3. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Na stránce Přehled vyberte **rozhraní Identity Experience Framework**.
5. Vyberte **klávesy zásad** a pak vyberte **Přidat**.
6. V části `Manual` **Možnosti**zvolte .
7. Zadejte **název** klíče zásady. Například, `TwitterSecret`. Předpona `B2C_1A_` se automaticky přidá k názvu klíče.
8. V **poli Tajné**zadejte tajný klíč klienta, který jste dříve zaznamenali.
9. V případě použití `Encryption` **klíče**vyberte .
10. Klikněte na **Vytvořit**.

## <a name="add-a-claims-provider"></a>Přidání poskytovatele deklarací identity

Pokud chcete, aby se uživatelé přihlašovali pomocí účtu Twitter, musíte definovat účet jako poskytovatele deklarací identity, se kterým může Azure AD B2C komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací, které používá Azure AD B2C k ověření, že konkrétní uživatel byl ověřen.

Účet Twitter můžete definovat jako poskytovatele deklarací identity přidáním do prvku **ClaimsProviders** v rozšiřujícím souboru zásad.

1. Otevřete *soubor TrustFrameworkExtensions.xml*.
2. Najít **ClaimsProviders** element. Pokud neexistuje, přidejte jej pod kořenový prvek.
3. Přidejte nového **Zprostředkovatele deklarací** identity takto:

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

4. Nahraďte hodnotu **client_id** spotřebitele klíčem příjemce, který jste dříve zaznamenali.
5. Uložte soubor.

### <a name="upload-the-extension-file-for-verification"></a>Nahrát soubor rozšíření pro ověření

Teď jste nakonfigurovali zásady tak, aby Azure AD B2C ví, jak komunikovat s vaším účtem Twitter. Zkuste nahrát soubor rozšíření zásad, abyste potvrdili, že zatím nemá žádné problémy.

1. Na stránce **Vlastní zásady** v tenantovi Azure AD B2C vyberte **Zásady nahrávání**.
2. Povolte **Přepište zásadu, pokud existuje**, a potom vyhledejte soubor *TrustFrameworkExtensions.xml* a vyberte jej.
3. Klikněte na **Odeslat**.

## <a name="register-the-claims-provider"></a>Registrace zprostředkovatele deklarací identity

V tomto okamžiku byl nastaven zprostředkovatel identity, ale není k dispozici v žádné z přihlašovacích nebo přihlašovacích obrazovek. Chcete-li ji zpřístupnit, vytvořte duplikát existující cesty uživatele šablony a pak ji upravte tak, aby měla také poskytovatele identity Twitteru.

1. Otevřete soubor *TrustFrameworkBase.xml* ze startovního balíčku.
2. Vyhledejte a zkopírujte celý obsah prvku `Id="SignUpOrSignIn"` **UserJourney,** který obsahuje .
3. Otevřete soubor *TrustFrameworkExtensions.xml* a vyhledejte element **UserJourneys.** Pokud prvek neexistuje, přidejte jeden.
4. Vložte celý obsah **UserJourney** prvek, který jste zkopírovali jako podřízený **UserJourneys** element.
5. Přejmenujte ID cesty uživatele. Například, `SignUpSignInTwitter`.

### <a name="display-the-button"></a>Zobrazení tlačítka

**ClaimsProviderSelection** prvek je obdobou tlačítko zprostředkovatele identity na přihlašovací nebo přihlašovací obrazovce. Pokud přidáte **ClaimsProviderSelection** prvek pro účet Twitter, nové tlačítko se zobrazí, když uživatel přistane na stránce.

1. Najděte **orchestrationstep** prvek, který zahrnuje `Order="1"` v cestě uživatele, které jste vytvořili.
2. V části **ClaimsProviderSelects**přidejte následující prvek. Nastavte hodnotu **TargetClaimsExchangeId** na příslušnou hodnotu, například `TwitterExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Propojení tlačítka s akcí

Nyní, když máte tlačítko na místě, musíte jej propojit s akcí. Akce, v tomto případě je pro Azure AD B2C komunikovat s účtem Twitter přijímat token.

1. Najít **OrchestrationStep,** `Order="2"` který zahrnuje v cestě uživatele.
2. Přidejte následující **claimsexchange** prvek ujistěte se, že používáte stejnou hodnotu pro ID, které jste použili pro **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    Aktualizujte hodnotu **TechnicalProfileReferenceId** na ID technického profilu, který jste vytvořili dříve. Například, `Twitter-OAUTH1`.

3. Uložte soubor *TrustFrameworkExtensions.xml* a znovu jej nahrajte k ověření.

## <a name="create-an-azure-ad-b2c-application"></a>Vytvoření aplikace Azure AD B2C

Komunikace s Azure AD B2C probíhá prostřednictvím aplikace, kterou zaregistrujete ve vašem tenantovi B2C. V této části jsou uvedeny volitelné kroky, které můžete provést a vytvořit testovací aplikaci, pokud jste tak ještě neučinili.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aktualizace a testování souboru předávající strany

Aktualizujte soubor předávající strany (RP), který iniciuje cestu uživatele, kterou jste vytvořili.

1. Vytvořte kopii *souboru SignUpOrSignIn.xml* ve svém pracovním adresáři a přejmenujte ji. Přejmenujte jej například na *SignUpSignInTwitter.xml*.
2. Otevřete nový soubor a aktualizujte hodnotu atributu **PolicyId** pro **TrustFrameworkPolicy** s jedinečnou hodnotou. Například, `SignUpSignInTwitter`.
3. Aktualizujte hodnotu **PublicPolicyUri** pomocí identifikátoru URI pro zásadu. Například`http://contoso.com/B2C_1A_signup_signin_twitter`
4. Aktualizujte hodnotu atributu **ReferenceId** v **DefaultUserJourney** tak, aby odpovídalo ID nové cesty uživatele, kterou jste vytvořili (SignUpSignTwitter).
5. Uložte změny, nahrajte soubor a vyberte novou zásadu v seznamu.
6. Ujistěte se, že aplikace Azure AD B2C, kterou jste vytvořili, je vybraná v poli **Vybrat aplikaci** a pak ji otestujte kliknutím na **spustit nyní**.
