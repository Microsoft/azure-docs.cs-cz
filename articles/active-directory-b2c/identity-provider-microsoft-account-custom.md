---
title: Nastavení přihlášení pomocí účtu účtu Microsoft pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Použití vlastních zásad k povolení účtu Microsoft (MSA) jako zprostředkovatele identity pomocí protokolu OpenID Connect (OIDC).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: db81f8b60cf4883223f6fc084c19c8da1d07bc9a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388098"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Nastavení přihlašování pomocí účet Microsoft s využitím vlastních zásad v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku se dozvíte, jak povolit přihlášení uživatelů ze účet Microsoft pomocí [vlastních zásad](custom-policy-overview.md) v Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Požadavky

- Proveďte kroky v části Začínáme [s vlastními zásadami v Azure Active Directory B2C](custom-policy-get-started.md).
- Pokud ještě nemáte účet Microsoft, vytvořte ho na adrese [https://www.live.com/](https://www.live.com/) .

## <a name="register-an-application"></a>Registrace aplikace

Pokud chcete povolit přihlašování pro uživatele pomocí účet Microsoft, musíte zaregistrovat aplikaci v tenantovi Azure AD. Tenant Azure AD není stejný jako váš tenant Azure AD B2C.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD, a to tak, že v horní nabídce vyberete adresář a filtr **předplatného** a zvolíte adresář, který obsahuje vašeho TENANTA Azure AD.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Registrace aplikací**.
1. Vyberte **Nová registrace**.
1. Zadejte **název** vaší aplikace. Například *MSAapp1*.
1. V části **podporované typy účtů**vyberte **účty v jakémkoli adresáři organizace (libovolný adresář Azure AD – víceklientské) a osobní účty Microsoft (např. Skype, Xbox)**.
1. V části **identifikátor URI přesměrování (volitelné)** vyberte **Web** a `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/oauth2/authresp` do textového pole zadejte. Nahraďte `<tenant-name>` názvem vašeho tenanta Azure AD B2C.
1. Vybrat **registraci**
1. Poznamenejte si **ID aplikace (klienta)** zobrazené na stránce Přehled aplikace. To budete potřebovat při konfiguraci zprostředkovatele deklarací v pozdější části.
1. Vybrat **certifikáty & tajných** kódů
1. Klikněte na **Nový tajný kód klienta**.
1. Zadejte **Popis** tajného kódu, například *MSA tajný klíč klienta aplikace*a klikněte na tlačítko **Přidat**.
1. Poznamenejte si heslo aplikace zobrazené ve sloupci **hodnota** . Tuto hodnotu použijete v další části.

## <a name="configuring-optional-claims"></a>Konfigurace volitelných deklarací identity

Pokud chcete získat `family_name` `given_name` deklarace identity a ze služby Azure AD, můžete nakonfigurovat volitelné deklarace identity pro aplikaci v uživatelském rozhraní Azure Portal nebo manifestu aplikace. Další informace najdete v tématu [jak poskytnout volitelné deklarace identity vaší aplikaci Azure AD](../active-directory/develop/active-directory-optional-claims.md).

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). Vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Registrace aplikací**.
1. V seznamu vyberte aplikaci, pro kterou chcete nakonfigurovat volitelné deklarace identity.
1. V části **Správa** vyberte **Konfigurace tokenu (Preview)**.
1. Vyberte **přidat volitelnou deklaraci identity**.
1. Vyberte typ tokenu, který chcete konfigurovat.
1. Vyberte volitelné deklarace, které se mají přidat.
1. Klikněte na **Přidat**.

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Teď, když jste vytvořili aplikaci v tenantovi Azure AD, musíte do svého tenanta Azure AD B2C Uložit tajný klíč klienta této aplikace.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Na stránce Přehled vyberte možnost **Architektura prostředí identity**.
1. Vyberte **klíče zásad** a pak vyberte **Přidat**.
1. Pro **Možnosti**vyberte možnost `Manual` .
1. Zadejte **název** klíče zásad. Například, `MSASecret`. Předpona `B2C_1A_` se automaticky přidá do názvu vašeho klíče.
1. Do pole **tajný kód**zadejte tajný klíč klienta, který jste si poznamenali v předchozí části.
1. Pro **použití klíče**vyberte `Signature` .
1. Klikněte na **Vytvořit**.

## <a name="add-a-claims-provider"></a>Přidat zprostředkovatele deklarací identity

Pokud chcete uživatelům povolit, aby se přihlásili pomocí účet Microsoft, musíte účet definovat jako zprostředkovatele deklarací identity, se kterým Azure AD B2C může komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací, které používá Azure AD B2C k ověření, že konkrétní uživatel byl ověřen.

Službu Azure AD můžete definovat jako zprostředkovatele deklarací přidáním prvku **ClaimsProvider** do souboru rozšíření zásady.

1. Otevřete soubor zásad *TrustFrameworkExtensions.xml* .
1. Vyhledejte element **ClaimsProviders** . Pokud neexistuje, přidejte jej pod kořenový element.
1. Přidejte nový **ClaimsProvider** následujícím způsobem:

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-OIDC">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="email" />
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

1. Hodnotu **client_id** nahraďte *ID aplikace (klienta)* aplikace služby Azure AD, kterou jste si poznamenali dříve.
1. Uložte soubor.

Nyní jste nakonfigurovali zásady tak, aby Azure AD B2C vědět, jak komunikovat s aplikací účet Microsoft ve službě Azure AD.

### <a name="upload-the-extension-file-for-verification"></a>Nahrajte soubor rozšíření pro ověření.

Než budete pokračovat, nahrajte upravenou zásadu a potvrďte, že zatím nemá žádné problémy.

1. V Azure Portal přejděte na svého tenanta Azure AD B2C a vyberte **rozhraní identity Experience Framework**.
1. Na stránce **vlastní zásady** vyberte **nahrát vlastní zásadu**.
1. Pokud existuje, zapněte **zásadu přepsat**a pak vyhledejte a vyberte soubor *TrustFrameworkExtensions.xml* .
1. Klikněte na **Odeslat**.

Pokud se na portálu nezobrazí žádné chyby, pokračujte k další části.

## <a name="register-the-claims-provider"></a>Registrace zprostředkovatele deklarací identity

V tomto okamžiku jste nastavili zprostředkovatele identity, ale ještě není dostupný na žádném z vašich přihlašovacích nebo přihlašovacích obrazovek. Aby byl k dispozici, vytvořte duplikát stávající cesty uživatele šablony a pak ho upravte, aby měl i účet Microsoft poskytovatele identity.

1. Otevřete soubor *TrustFrameworkBase.xml* z úvodní sady.
1. Vyhledejte a zkopírujte celý obsah prvku **UserJourney** , který obsahuje `Id="SignUpOrSignIn"` .
1. Otevřete *TrustFrameworkExtensions.xml* a vyhledejte element **userjourney** . Pokud element neexistuje, přidejte jej.
1. Vložte celý obsah elementu **UserJourney** , který jste zkopírovali jako podřízený prvek **userjourney** elementu.
1. Přejmenujte ID cesty pro uživatele. Například, `SignUpSignInMSA`.

### <a name="display-the-button"></a>Zobrazit tlačítko

Element **claimsproviderselection.** se podobá tlačítku poskytovatele identity na obrazovce pro registraci nebo přihlášení. Pokud přidáte prvek **claimsproviderselection.** pro účet Microsoft, zobrazí se nové tlačítko, když se uživatel na stránce zařadí.

1. V souboru *TrustFrameworkExtensions.xml* vyhledejte element **OrchestrationStep** , který obsahuje cestu k `Order="1"` uživateli, kterou jste vytvořili.
1. Pod **ClaimsProviderSelects**přidejte následující element. Nastavte hodnotu **TargetClaimsExchangeId** na odpovídající hodnotu, například `MicrosoftAccountExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Propojit tlačítko s akcí

Teď, když máte tlačítko na místě, musíte ho propojit s akcí. Tato akce je v tomto případě určena pro Azure AD B2C ke komunikaci s účet Microsoft k získání tokenu.

1. Najděte **OrchestrationStep** , který obsahuje `Order="2"` cestu k uživateli.
1. Přidejte následující prvek **ClaimsExchange** a ujistěte se, že používáte stejnou hodnotu pro ID, které jste použili pro **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Aktualizujte hodnotu **TechnicalProfileReferenceId** tak, aby odpovídala `Id` hodnotě v elementu **TechnicalProfile** zprostředkovatele deklarací, který jste přidali dříve. Například, `MSA-OIDC`.

1. Uložte soubor *TrustFrameworkExtensions.xml* a znovu ho nahrajte pro účely ověření.

## <a name="create-an-azure-ad-b2c-application"></a>Vytvoření aplikace Azure AD B2C

Komunikace s Azure AD B2C probíhá prostřednictvím aplikace, kterou zaregistrujete v tenantovi B2C. V této části jsou uvedeny volitelné kroky, které můžete provést, chcete-li vytvořit testovací aplikaci, pokud jste tak již neučinili.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aktualizace a testování souboru předávající strany

Aktualizujte soubor předávající strany (RP), který iniciuje cestu uživatele, kterou jste vytvořili.

1. Vytvořte kopii *SignUpOrSignIn.xml* v pracovním adresáři a přejmenujte ji. Přejmenujte ho například na *SignUpSignInMSA.xml*.
1. Otevřete nový soubor a aktualizujte hodnotu atributu **PolicyId** pro **TrustFrameworkPolicy** s jedinečnou hodnotou. Například, `SignUpSignInMSA`.
1. Aktualizujte hodnotu **PUBLICPOLICYURI** identifikátorem URI pro zásadu. Například`http://contoso.com/B2C_1A_signup_signin_msa`
1. Aktualizujte hodnotu atributu **ReferenceId** v **DefaultUserJourney** tak, aby odpovídala ID cesty uživatele, kterou jste vytvořili dříve (SignUpSignInMSA).
1. Uložte změny, nahrajte soubor a pak v seznamu vyberte novou zásadu.
1. Ujistěte se, že Azure AD B2C aplikace, kterou jste vytvořili v předchozí části (nebo dokončení požadavků, například *WebApp1* nebo *testapp1*), se vybrala v poli **Vybrat aplikaci** a potom ji otestujete kliknutím na **Spustit nyní**.
1. Vyberte tlačítko **účet Microsoft** a přihlaste se.

    Pokud je operace přihlášení úspěšná, budete přesměrováni na to, `jwt.ms` které zobrazí Dekódovatelné tokeny, podobně jako:

    ```json
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "<key-ID>"
    }.{
      "exp": 1562365200,
      "nbf": 1562361600,
      "ver": "1.0",
      "iss": "https://<tenant-name>.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
      "sub": "20000000-0000-0000-0000-000000000000",
      "aud": "30000000-0000-0000-0000-000000000000",
      "acr": "b2c_1a_signupsigninmsa",
      "nonce": "defaultNonce",
      "iat": 1562361600,
      "auth_time": 1562361600,
      "idp": "live.com",
      "name": "Azure User",
      "email": "azureuser@contoso.com",
      "tid": "6fc3b573-7b38-4c0c-b627-2e8684f6c575"
    }.[Signature]
    ```
