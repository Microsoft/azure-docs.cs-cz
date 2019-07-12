---
title: Přidat účet Microsoft (MSA) jako zprostředkovatele identity pomocí vlastních zásad v Azure Active Directory B2C
description: Ukázka použití Microsoft jako zprostředkovatele identity pomocí protokolu OpenID Connect (OIDC).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9ae944a9d587e71c4be83bd524cf3875a7b52dd1
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654156"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Nastavit přihlašování pomocí účtu Microsoft pomocí vlastních zásad v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek ukazuje, jak povolit přihlášení pro uživatele z účtu Microsoft s použitím [vlastní zásady](active-directory-b2c-overview-custom.md) v Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Požadavky

- Proveďte kroky v [začít pracovat s vlastními zásadami v Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Pokud ještě nemáte účet Microsoft, vytvořte si ho na [ https://www.live.com/ ](https://www.live.com/).

## <a name="add-an-application"></a>Přidání aplikace

Pokud chcete povolit přihlášení pro uživatele s účtem Microsoft, budete muset zaregistrovat aplikaci v tenantovi Azure AD. Tenant Azure AD není stejný jako vašeho tenanta Azure AD B2C.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresáře, který obsahuje váš tenant Azure AD kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant Azure AD.
1. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **registrace aplikací**.
1. Vyberte **registrace nové**.
1. Zadejte **název** pro vaši aplikaci. Například *MSAapp1*.
1. V části **podporovaných typů účtu**vyberte **účty v jakékoli organizaci adresáři a osobní účty Microsoft (třeba Skype, Xbox, Outlook.com)** .
1. V části **identifikátor URI pro přesměrování (volitelné)** vyberte **webové** a zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` v textovém poli. Nahraďte `your-tenant-name` názvem vašeho tenanta Azure AD B2C.
1. Vyberte **zaregistrovat**
1. Záznam **ID aplikace (klient)** zobrazený na stránce Přehled aplikace. Budete potřebovat při konfiguraci poskytovatele deklarací identity v další části.
1. Vyberte **certifikáty a tajné kódy**
1. Klikněte na tlačítko **nový tajný kód klienta**
1. Zadejte **popis** pro tajný klíč, například *tajný kód klienta aplikace MSA*a potom klikněte na tlačítko **přidat**.
1. Zaznamenat heslo aplikace je znázorněno **hodnotu** sloupce. Tuto hodnotu použijete v další části.

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Teď, když jste vytvořili aplikaci ve vašem tenantovi Azure AD, budete muset uložit tajný kód klienta vaší aplikace ve vašem tenantovi Azure AD B2C.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C. Vyberte **filtr adresářů a předplatných** v horní nabídce a vyberte adresář, který obsahuje váš tenant.
1. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
1. Na stránce s přehledem, vyberte **architekturu rozhraní identit**.
1. Vyberte **klíče zásad** a pak vyberte **přidat**.
1. Pro **možnosti**, zvolte `Manual`.
1. Zadejte **název** klíče zásad. Například, `MSASecret`. Předpona, která `B2C_1A_` je automaticky přidán do názvu klíče.
1. V **tajný kód**, zadejte tajný kód klienta, který jste si poznamenali v předchozí části.
1. Pro **použití klíče**vyberte `Signature`.
1. Klikněte na možnost **Vytvořit**.

## <a name="add-a-claims-provider"></a>Přidat zprostředkovatele deklarací identity

Povolit uživatelům přihlášení pomocí účtu Microsoft, musíte definovat účtu jako zprostředkovatele deklarací identity, který Azure AD B2C můžou klienti komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací identity, které používají Azure AD B2C k ověření, že se ověřil konkrétního uživatele.

Azure AD jako poskytovatele deklarací identity můžete definovat tak, že přidáte **ClaimsProvider** prvku v souboru rozšíření vašich zásad.

1. Otevřít *TrustFrameworkExtensions.xml* soubor zásad.
1. Najít **ClaimsProviders** elementu. Pokud neexistuje, přidejte jej pod kořenovým elementem.
1. Přidat nový **ClaimsProvider** následujícím způsobem:

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

1. Nahraďte hodnotu **client_id** s aplikací Azure AD *ID aplikace (klient)* , který jste si poznamenali dříve.
1. Uložte soubor.

Teď nakonfigurujete zásady tak, aby Azure AD B2C ví, jak komunikovat s vaší aplikací Microsoft účtu ve službě Azure AD.

### <a name="upload-the-extension-file-for-verification"></a>Nahrát soubor rozšíření pro ověřování

Než budete pokračovat, nahrajte upravenou zásadu potvrďte, že všechny problémy nemusí doposud.

1. Přejděte do vašeho tenanta Azure AD B2C v Azure portal a vyberte **architekturu rozhraní identit**.
1. Na **vlastní zásady** stránce **nahrát vlastní zásady**.
1. Povolit **přepsat zásady, pokud existuje**a poté vyhledejte a vyberte *TrustFrameworkExtensions.xml* souboru.
1. Klikněte na **Odeslat**.

Pokud na portálu se nezobrazí žádné chyby, pokračujte k další části.

## <a name="register-the-claims-provider"></a>Registrace zprostředkovatele deklarací identity

V tomto okamžiku jste nastavili zprostředkovatele identity, ale ještě není k dispozici v některém z obrazovky registrace nebo přihlášení. Chcete-li k dispozici, vytvoření duplicitní cesty existující uživatele šablonu a pak ho upravit tak, aby byly také zprostředkovatele identity účtu Microsoft.

1. Otevřít *TrustFrameworkBase.xml* soubor z starter pack.
1. Vyhledejte a zkopírujte celý obsah **UserJourney** element, který zahrnuje `Id="SignUpOrSignIn"`.
1. Otevřít *TrustFrameworkExtensions.xml* a najít **Userjourney** elementu. Pokud element neexistuje, přidejte jeden.
1. Vložte celý obsah **UserJourney** element, který jste zkopírovali jako podřízený objekt **Userjourney** elementu.
1. Přejmenujte ID cesty uživatele. Například, `SignUpSignInMSA`.

### <a name="display-the-button"></a>Zobrazit tlačítko

**ClaimsProviderSelection** element je obdobou k tlačítku na obrazovce registrace nebo přihlášení zprostředkovatele identity. Pokud chcete přidat **ClaimsProviderSelection** – element pro účet Microsoft, nové tlačítko se zobrazí, když uživatel umístil na stránce.

1. V *TrustFrameworkExtensions.xml* souboru, vyhledejte **OrchestrationStep** element, který zahrnuje `Order="1"` v cestě uživatele, který jste vytvořili.
1. V části **ClaimsProviderSelects**, přidejte následující prvek. Nastavte hodnotu **TargetClaimsExchangeId** na odpovídající hodnotu, například `MicrosoftAccountExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Tlačítko s odkazem na akci

Teď, když máte tlačítko na místě, budete potřebovat odkázat na akci. Akce v tomto případě je pro Azure AD B2C ke komunikaci s účtem Microsoft k získání tokenu.

1. Najít **OrchestrationStep** , který obsahuje `Order="2"` v cestě uživatele.
1. Přidejte následující **ClaimsExchange** a ujistěte se, že používáte stejnou hodnotu pro ID, které jste použili pro element **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Aktualizujte hodnotu **TechnicalProfileReferenceId** odpovídat `Id` hodnotu **technický profil** elementu zprostředkovatele deklarací identity, které jste přidali dříve. Například, `MSA-OIDC`.

1. Uložit *TrustFrameworkExtensions.xml* souboru a nahrajte ji znovu pro ověření.

## <a name="create-an-azure-ad-b2c-application"></a>Vytvoření aplikace Azure AD B2C

Probíhá komunikace s Azure AD B2C prostřednictvím aplikace, kterou vytvoříte ve svém tenantovi Azure AD B2C. Tato část obsahuje seznam volitelné kroky, které můžete použít k vytvoření aplikace testů, pokud jste tak již neučinili.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C. Vyberte **filtr adresářů a předplatných** v horní nabídce a vyberte adresář, který obsahuje váš tenant.
1. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **aplikací**a pak vyberte **přidat**.
1. Zadejte název aplikace, například *testapp1*.
1. Pro **webová aplikace / webové rozhraní API**vyberte `Yes`a pak zadejte `https://jwt.ms` pro **adresy URL odpovědi**.
1. Klikněte na možnost **Vytvořit**.

## <a name="update-and-test-the-relying-party-file"></a>Aktualizace a předávající strany soubor testu

Aktualizujte předávající stranu soubor, který iniciuje cesty uživatele, který jste vytvořili.

1. Vytvořte kopii *SignUpOrSignIn.xml* ve svém pracovním adresáři a přejmenujte jej. Například přejmenujte ho na *SignUpSignInMSA.xml*.
1. Otevřete nový soubor a aktualizujte hodnotu **PolicyId** atributu **TrustFrameworkPolicy** s jedinečnou hodnotu. Například, `SignUpSignInMSA`.
1. Aktualizujte hodnotu **PublicPolicyUri** s identifikátorem URI pro zásady. Například`http://contoso.com/B2C_1A_signup_signin_msa`
1. Aktualizujte hodnotu **ReferenceId** atribut **DefaultUserJourney** tak, aby odpovídaly ID cesty uživatele, který jste vytvořili starší (SignUpSignInMSA).
1. Uložte provedené změny, nahrajte soubor a pak v seznamu vyberte novou zásadu.
1. Ujistěte se, že aplikace Azure AD B2C, kterou jste vytvořili v předchozí části (nebo provedením požadované součásti, například *webapp1* nebo *testapp1*) je vybraná v **vyberte aplikace** pole a pak ho otestujte kliknutím **spustit nyní**.
1. Vyberte **Account Microsoft** tlačítko a přihlaste se.

    Pokud je úspěšná operace přihlášení, budete přesměrováni na `jwt.ms` zobrazuje dekódovat Token, podobně jako:

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
