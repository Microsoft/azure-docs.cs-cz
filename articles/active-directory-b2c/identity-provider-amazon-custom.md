---
title: Nastavení přihlášení pomocí účtu Amazon pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Nastavte přihlášení pomocí účtu Amazon ve službě Azure Active Directory B2C pomocí vlastních zásad.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2de891ee109677f92ff603759701f7732f5951ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188507"
---
# <a name="set-up-sign-in-with-an-amazon-account-using-custom-policies-in-azure-active-directory-b2c"></a>Nastavení přihlášení pomocí účtu Amazon pomocí vlastních zásad ve službě Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek ukazuje, jak povolit přihlášení pro uživatele z účtu Amazon pomocí [vlastních zásad](custom-policy-overview.md) v Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Požadavky

- Proveďte kroky v [části Začínáme s vlastními zásadami](custom-policy-get-started.md).
- Pokud ještě nemáte účet Amazon, vytvořte [https://www.amazon.com/](https://www.amazon.com/)si ho na adrese .

## <a name="register-the-application"></a>Registrace přihlášky

Chcete-li povolit přihlášení pro uživatele z účtu Amazon, musíte vytvořit aplikaci Amazon.

1. Přihlaste se do [Amazon Developer Center](https://login.amazon.com/) s přihlašovacími údaji svého účtu Amazon.
2. Pokud jste tak ještě neučinili, klikněte na **Zaregistrovat se**, postupujte podle kroků registrace vývojáře a přijměte zásady.
3. Vyberte **možnost Registrovat novou aplikaci**.
4. Zadejte **adresu URL název**, **popis**a oznámení **o zásadách ochrany osobních údajů**a klepněte na tlačítko **Uložit**. Oznámení o ochraně osobních údajů je stránka, kterou spravujete a která uživatelům poskytuje informace o ochraně osobních údajů.
5. V části **Nastavení webu** zkopírujte hodnoty **ID klienta**. Vyberte **Zobrazit tajný klíč,** chcete-li získat tajný klíč klienta a pak jej zkopírovat. Potřebujete oba ke konfiguraci účtu Amazon jako poskytovatele identity ve vašem tenantovi. **Tajný klíč klienta** je důležité pověření zabezpečení.
6. V části **Nastavení webu** vyberte **Upravit**a zadejte `https://your-tenant-name.b2clogin.com` do pole Povolený původ **JavaScriptu** a `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` v **adresách URL allowed return**. Nahraďte `your-tenant-name` se jménem svého tenanta. Při zadávání názvu klienta použijte všechna malá písmena, i když je klient definován velkými písmeny v Azure AD B2C.
7. Klikněte na **Uložit**.

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Musíte uložit tajný klíč klienta, který jste dříve zaznamenali v tenantovi Azure AD B2C.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C výběrem directory **+ předplatné** filtr v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta.
3. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Na stránce Přehled vyberte **rozhraní Identity Experience Framework**.
5. Vyberte **klávesy zásad** a pak vyberte **Přidat**.
6. V části `Manual` **Možnosti**zvolte .
7. Zadejte **název** klíče zásady. Například, `AmazonSecret`. Předpona `B2C_1A_` se automaticky přidá k názvu klíče.
8. V **poli Tajné**zadejte tajný klíč klienta, který jste dříve zaznamenali.
9. V případě použití `Signature` **klíče**vyberte .
10. Klikněte na **Vytvořit**.

## <a name="add-a-claims-provider"></a>Přidání poskytovatele deklarací identity

Pokud chcete, aby se uživatelé přihlašovali pomocí účtu Amazon, musíte definovat účet jako poskytovatele deklarací identity, se kterým může Azure AD B2C komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací, které používá Azure AD B2C k ověření, že konkrétní uživatel byl ověřen.

Účet Amazon můžete definovat jako poskytovatele deklarací identity přidáním do prvku **ClaimsProviders** v souboru rozšíření vaší zásady.


1. Otevřete *soubor TrustFrameworkExtensions.xml*.
2. Najít **ClaimsProviders** element. Pokud neexistuje, přidejte jej pod kořenový prvek.
3. Přidejte nového **Zprostředkovatele deklarací** identity takto:

    ```xml
    <ClaimsProvider>
      <Domain>amazon.com</Domain>
      <DisplayName>Amazon</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Amazon-OAUTH">
        <DisplayName>Amazon</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
          <Item Key="ProviderName">amazon</Item>
          <Item Key="authorization_endpoint">https://www.amazon.com/ap/oa</Item>
          <Item Key="AccessTokenEndpoint">https://api.amazon.com/auth/o2/token</Item>
          <Item Key="ClaimsEndpoint">https://api.amazon.com/user/profile</Item>
          <Item Key="scope">profile</Item>
          <Item Key="HttpBinding">POST</Item>
          <Item Key="UsePolicyInRedirectUri">0</Item>
          <Item Key="client_id">Your Amazon application client ID</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_AmazonSecret" />
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="amazon.com" />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
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

4. Nastavte **client_id** na ID aplikace z registrace aplikace.
5. Uložte soubor.

### <a name="upload-the-extension-file-for-verification"></a>Nahrát soubor rozšíření pro ověření

Teď jste nakonfigurovali zásady tak, aby Azure AD B2C ví, jak komunikovat s adresářem Azure AD. Zkuste nahrát soubor rozšíření zásad, abyste potvrdili, že zatím nemá žádné problémy.

1. Na stránce **Vlastní zásady** v tenantovi Azure AD B2C vyberte **Zásady nahrávání**.
2. Povolte **Přepište zásadu, pokud existuje**, a potom vyhledejte soubor *TrustFrameworkExtensions.xml* a vyberte jej.
3. Klikněte na **Odeslat**.

## <a name="register-the-claims-provider"></a>Registrace zprostředkovatele deklarací identity

V tomto okamžiku byl nastaven zprostředkovatel identity, ale není k dispozici v žádné z přihlašovacích a přihlašovacích obrazovek. Chcete-li ji zpřístupnit, vytvořte duplikát existující cesty uživatele šablony a pak ji upravte tak, aby měla také zprostředkovatele identity Amazon.

1. Otevřete soubor *TrustFrameworkBase.xml* ze startovního balíčku.
2. Vyhledejte a zkopírujte celý obsah prvku `Id="SignUpOrSignIn"` **UserJourney,** který obsahuje .
3. Otevřete soubor *TrustFrameworkExtensions.xml* a vyhledejte element **UserJourneys.** Pokud prvek neexistuje, přidejte jeden.
4. Vložte celý obsah **UserJourney** prvek, který jste zkopírovali jako podřízený **UserJourneys** element.
5. Přejmenujte ID cesty uživatele. Například, `SignUpSignInAmazon`.

### <a name="display-the-button"></a>Zobrazení tlačítka

**ClaimsProviderSelection** prvek je obdobou tlačítko zprostředkovatele identity na přihlašovací nebo přihlašovací obrazovce. Pokud přidáte **ClaimsProviderSelection** prvek pro účet Amazon, nové tlačítko se zobrazí, když uživatel přistane na stránce.

1. Najděte **orchestrationstep** prvek, který zahrnuje `Order="1"` v cestě uživatele, které jste vytvořili.
2. V části **ClaimsProviderSelects**přidejte následující prvek. Nastavte hodnotu **TargetClaimsExchangeId** na příslušnou hodnotu, například `AmazonExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AmazonExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Propojení tlačítka s akcí

Nyní, když máte tlačítko na místě, musíte jej propojit s akcí. Akce, v tomto případě je pro Azure AD B2C komunikovat s účtem Amazon přijímat token.

1. Najít **OrchestrationStep,** `Order="2"` který zahrnuje v cestě uživatele.
2. Přidejte následující **claimsexchange** prvek ujistěte se, že používáte stejnou hodnotu pro ID, které jste použili pro **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="AmazonExchange" TechnicalProfileReferenceId="Amazon-OAuth" />
    ```

    Aktualizujte hodnotu **TechnicalProfileReferenceId** na ID technického profilu, který jste vytvořili dříve. Například, `Amazon-OAuth`.

3. Uložte soubor *TrustFrameworkExtensions.xml* a znovu jej nahrajte k ověření.

## <a name="create-an-azure-ad-b2c-application"></a>Vytvoření aplikace Azure AD B2C

Komunikace s Azure AD B2C probíhá prostřednictvím aplikace, kterou zaregistrujete ve vašem tenantovi B2C. V této části jsou uvedeny volitelné kroky, které můžete provést a vytvořit testovací aplikaci, pokud jste tak ještě neučinili.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aktualizace a testování souboru předávající strany

Aktualizujte soubor předávající strany (RP), který iniciuje cestu uživatele, kterou jste vytvořili.

1. Vytvořte kopii *souboru SignUpOrSignIn.xml* ve svém pracovním adresáři a přejmenujte ji. Můžete jej například přejmenovat na *SignUpSignInAmazon.xml*.
2. Otevřete nový soubor a aktualizujte hodnotu atributu **PolicyId** pro **TrustFrameworkPolicy** s jedinečnou hodnotou. Například, `SignUpSignInAmazon`.
3. Aktualizujte hodnotu **PublicPolicyUri** pomocí identifikátoru URI pro zásadu. Například`http://contoso.com/B2C_1A_signup_signin_amazon`
4. Aktualizujte hodnotu atributu **ReferenceId** v **DefaultUserJourney** tak, aby odpovídalo ID nové cesty uživatele, kterou jste vytvořili (SignUpSignAmazon).
5. Uložte změny, nahrajte soubor a vyberte novou zásadu v seznamu.
6. Ujistěte se, že aplikace Azure AD B2C, kterou jste vytvořili, je vybraná v poli **Vybrat aplikaci** a pak ji otestujte kliknutím na **spustit nyní**.
