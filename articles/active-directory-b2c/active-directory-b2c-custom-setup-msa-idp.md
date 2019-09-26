---
title: Přidat účet Microsoft (MSA) jako zprostředkovatele identity pomocí vlastních zásad v Azure Active Directory B2C
description: Ukázka použití poskytovatele identity Microsoft as identity pomocí protokolu OpenID Connect (OIDC).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 854d45f8eb023436756d7a51c141f5eecab14db7
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315169"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Nastavení přihlašování pomocí účet Microsoft s využitím vlastních zásad v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku se dozvíte, jak povolit přihlášení uživatelů ze účet Microsoft pomocí [vlastních zásad](active-directory-b2c-overview-custom.md) v Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Požadavky

- Proveďte kroky v části Začínáme [s vlastními zásadami v Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Pokud ještě nemáte účet Microsoft, vytvořte ho na adrese [https://www.live.com/](https://www.live.com/).

## <a name="add-an-application"></a>Přidat aplikaci

Pokud chcete povolit přihlašování pro uživatele pomocí účet Microsoft, musíte zaregistrovat aplikaci v tenantovi Azure AD. Tenant Azure AD není stejný jako váš tenant Azure AD B2C.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD, a to tak, že v horní nabídce vyberete adresář a filtr **předplatného** a zvolíte adresář, který obsahuje vašeho TENANTA Azure AD.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Registrace aplikací**.
1. Vyberte **Nová registrace**.
1. Zadejte **název** vaší aplikace. Například *MSAapp1*.
1. V části **podporované typy účtů**vyberte **účty v libovolném organizačním adresáři a osobní účty Microsoft (např. Skype, Xbox, Outlook.com)** .
1. V části **identifikátor URI přesměrování (volitelné)** vyberte **Web** a `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` do textového pole zadejte. Nahraďte `your-tenant-name` názvem vašeho tenanta Azure AD B2C.
1. Vybrat **registraci**
1. Poznamenejte si **ID aplikace (klienta)** zobrazené na stránce Přehled aplikace. To budete potřebovat při konfiguraci zprostředkovatele deklarací v pozdější části.
1. Vybrat **certifikáty & tajných** kódů
1. Klikněte na **nový tajný klíč klienta** .
1. Zadejte **Popis** tajného kódu, například *MSA tajný klíč klienta aplikace*a klikněte na tlačítko **Přidat**.
1. Poznamenejte si heslo aplikace zobrazené ve sloupci **hodnota** . Tuto hodnotu použijete v další části.

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Teď, když jste vytvořili aplikaci v tenantovi Azure AD, musíte do svého tenanta Azure AD B2C Uložit tajný klíč klienta této aplikace.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Na stránce Přehled vyberte možnost **Architektura prostředí identity**.
1. Vyberte **klíče zásad** a pak vyberte **Přidat**.
1. Pro **Možnosti**vyberte `Manual`možnost.
1. Zadejte **název** klíče zásad. Například, `MSASecret`. Předpona `B2C_1A_` se automaticky přidá do názvu vašeho klíče.
1. Do pole **tajný kód**zadejte tajný klíč klienta, který jste si poznamenali v předchozí části.
1. Pro **použití klíče**vyberte `Signature`.
1. Klikněte na možnost **Vytvořit**.

## <a name="add-a-claims-provider"></a>Přidat zprostředkovatele deklarací identity

Pokud chcete uživatelům povolit, aby se přihlásili pomocí účet Microsoft, musíte účet definovat jako zprostředkovatele deklarací identity, se kterým Azure AD B2C může komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací, které používá Azure AD B2C k ověření, že konkrétní uživatel byl ověřen.

Službu Azure AD můžete definovat jako zprostředkovatele deklarací přidáním prvku **ClaimsProvider** do souboru rozšíření zásady.

1. Otevřete soubor zásad *TrustFrameworkExtensions. XML* .
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
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
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

1. Nahraďte hodnotu **CLIENT_ID** *ID aplikace (klienta)* aplikace Azure AD, kterou jste si poznamenali dříve.
1. Uložte soubor.

Nyní jste nakonfigurovali zásady tak, aby Azure AD B2C vědět, jak komunikovat s aplikací účet Microsoft ve službě Azure AD.

### <a name="upload-the-extension-file-for-verification"></a>Nahrajte soubor rozšíření pro ověření.

Než budete pokračovat, nahrajte upravenou zásadu a potvrďte, že zatím nemá žádné problémy.

1. V Azure Portal přejděte na svého tenanta Azure AD B2C a vyberte **rozhraní identity Experience Framework**.
1. Na stránce **vlastní zásady** vyberte **nahrát vlastní zásadu**.
1. Pokud existuje, zapněte **zásadu přepsat**a pak vyhledejte a vyberte soubor *TrustFrameworkExtensions. XML* .
1. Klikněte na **Odeslat**.

Pokud se na portálu nezobrazí žádné chyby, pokračujte k další části.

## <a name="register-the-claims-provider"></a>Registrace zprostředkovatele deklarací identity

V tomto okamžiku jste nastavili zprostředkovatele identity, ale ještě není dostupný na žádném z vašich přihlašovacích nebo přihlašovacích obrazovek. Aby byl k dispozici, vytvořte duplikát stávající cesty uživatele šablony a pak ho upravte, aby měl i účet Microsoft poskytovatele identity.

1. Otevřete soubor *TrustFrameworkBase. XML* z úvodní sady.
1. Vyhledejte a zkopírujte celý obsah prvku **UserJourney** , který obsahuje `Id="SignUpOrSignIn"`.
1. Otevřete *soubor TrustFrameworkExtensions. XML* a vyhledejte element **userjourney** . Pokud element neexistuje, přidejte jej.
1. Vložte celý obsah elementu **UserJourney** , který jste zkopírovali jako podřízený prvek **userjourney** elementu.
1. Přejmenujte ID cesty pro uživatele. Například, `SignUpSignInMSA`.

### <a name="display-the-button"></a>Zobrazit tlačítko

Element **claimsproviderselection.** se podobá tlačítku poskytovatele identity na obrazovce pro registraci nebo přihlášení. Pokud přidáte prvek **claimsproviderselection.** pro účet Microsoft, zobrazí se nové tlačítko, když se uživatel na stránce zařadí.

1. V souboru *TrustFrameworkExtensions. XML* vyhledejte prvek **OrchestrationStep** , který obsahuje `Order="1"` cestu k uživateli, kterou jste vytvořili.
1. Pod **ClaimsProviderSelects**přidejte následující element. Nastavte hodnotu **TargetClaimsExchangeId** na odpovídající hodnotu, například `MicrosoftAccountExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Propojit tlačítko s akcí

Teď, když máte tlačítko na místě, musíte ho propojit s akcí. Tato akce je v tomto případě určena pro Azure AD B2C ke komunikaci s účet Microsoft k získání tokenu.

1. Najděte **OrchestrationStep** , který obsahuje `Order="2"` cestu k uživateli.
1. Přidejte následující prvek **ClaimsExchange** a ujistěte se, že používáte stejnou hodnotu pro ID, které jste použili pro **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Aktualizujte hodnotu **TechnicalProfileReferenceId** tak, `Id` aby odpovídala hodnotě v elementu **TechnicalProfile** zprostředkovatele deklarací, který jste přidali dříve. Například, `MSA-OIDC`.

1. Uložte soubor *TrustFrameworkExtensions. XML* a znovu ho nahrajte k ověření.

## <a name="create-an-azure-ad-b2c-application"></a>Vytvoření aplikace Azure AD B2C

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aktualizace a testování souboru předávající strany

Aktualizujte soubor předávající strany (RP), který iniciuje cestu uživatele, kterou jste vytvořili.

1. Vytvořte kopii *SignUpOrSignIn. XML* v pracovním adresáři a přejmenujte ji. Přejmenujte ho například na *SignUpSignInMSA. XML*.
1. Otevřete nový soubor a aktualizujte hodnotu atributu **PolicyId** pro **TrustFrameworkPolicy** s jedinečnou hodnotou. Například, `SignUpSignInMSA`.
1. Aktualizujte hodnotu **PUBLICPOLICYURI** identifikátorem URI pro zásadu. Například`http://contoso.com/B2C_1A_signup_signin_msa`
1. Aktualizujte hodnotu atributu **ReferenceId** v **DefaultUserJourney** tak, aby odpovídala ID cesty uživatele, kterou jste vytvořili dříve (SignUpSignInMSA).
1. Uložte změny, nahrajte soubor a pak v seznamu vyberte novou zásadu.
1. Ujistěte se, že Azure AD B2C aplikace, kterou jste vytvořili v předchozí části (nebo dokončení požadavků, například *WebApp1* nebo *testapp1*), je vybrána v poli **Vybrat aplikaci** a potom ji otestujte kliknutím na **Spustit nyní.** .
1. Vyberte tlačítko **účet Microsoft** a přihlaste se.

    Pokud je operace přihlášení úspěšná, budete přesměrováni na `jwt.ms` to, které zobrazí Dekódovatelné tokeny, podobně jako:

    ```json
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "<key-ID>"
    }.{
      "exp": 1562365200,
      "nbf": 1562361600,
      "ver": "1.0",
      "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
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
