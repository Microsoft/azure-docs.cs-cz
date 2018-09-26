---
title: Přidat účet Microsoft (MSA) jako zprostředkovatele identity pomocí vlastních zásad v Azure Active Directory B2C | Dokumentace Microsoftu
description: Ukázka použití Microsoft jako zprostředkovatele identity pomocí protokolu OpenID Connect (OIDC).
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: b4f6901b9c44cb75e4cab1a89c521371ed14636f
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47180988"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Nastavit přihlašování pomocí účtu Microsoft pomocí vlastních zásad v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek ukazuje, jak povolit přihlášení pro uživatele z účtu Microsoft s použitím [vlastní zásady](active-directory-b2c-overview-custom.md) v Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Požadavky

- Proveďte kroky v [začít pracovat s vlastními zásadami v Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Pokud ještě nemáte účet Microsoft, vytvořte si ho na [ https://www.live.com/ ](https://www.live.com/).

## <a name="add-an-application"></a>Přidání aplikace

Pokud chcete použít účet Microsoft jako zprostředkovatele identity v Azure AD B2C, budete muset přidat aplikaci účtu Microsoft.

1. Přihlaste se k [portál pro registraci aplikací Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) pomocí svých přihlašovacích údajů účtu Microsoft.
2. V pravém horním rohu vyberte **přidat aplikaci**.
3. Zadejte **název_aplikace**a potom klikněte na tlačítko **Create** 
4. Vyberte **generovat nové heslo** a ujistěte se, že zkopírujete heslo pro použití při konfiguraci zprostředkovatele identity. Zkopírovat **Id aplikace**. 
5. Zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` v **Přesměrovaly**. Nahraďte `your-tenant-name` s názvem vašeho tenanta.
6. Vyberte **Uložit**.

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Budete muset uložit heslo, které jste vygenerovali a dříve nahrané ve vašem tenantovi Azure AD B2C.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
4. Na stránce s přehledem, vyberte **architekturu rozhraní identit - PREVIEW**.
5. Vyberte **klíče zásad** a pak vyberte **přidat**.
6. Pro **možnosti**, zvolte `Manual`.
7. Zadejte **název** klíče zásad. Například, `MSASecret`. Předpona, která `B2C_1A_` je automaticky přidán do názvu klíče.
8. V **tajný kód**, zadejte heslo, které jste si předtím poznamenali.
9. Pro **použití klíče**vyberte `Signature`.
10. Klikněte na možnost **Vytvořit**.

## <a name="add-a-claims-provider"></a>Přidat zprostředkovatele deklarací identity

Pokud chcete uživatelům umožní přihlásit pomocí účtu Microsoft, budete muset definovat účet jako poskytovatele deklarací identity, který Azure AD B2C můžou klienti komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací identity, které používají Azure AD B2C k ověření, že se ověřil konkrétního uživatele. 

Azure AD jako poskytovatele deklarací identity můžete definovat tak, že přidáte **ClaimsProvider** prvku v souboru rozšíření vašich zásad.

1. Otevřít *TrustFrameworkExtensions.xml*.
2. Najít **ClaimsProviders** elementu. Pokud neexistuje, přidejte jej pod kořenovým elementem.
3. Přidat nový **ClaimsProvider** následujícím způsobem:

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
            <Item Key="client_id">Your Microsoft application client id</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
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

4.  Nahraďte hodnotu **client_id** zadejte ID aplikace, které jste si předtím poznamenali.
5.  Uložte soubor.

### <a name="upload-the-extension-file-for-verification"></a>Nahrát soubor rozšíření pro ověřování

Nyní jste nakonfigurovali zásady tak, aby Azure AD B2C ví, jak komunikovat se svým účtem Microsoft. Zkuste nahrát soubor rozšíření zásady jenom k potvrzení, že všechny problémy nemusí zatím.

1. Na **vlastní zásady** stránky ve vašem tenantovi Azure AD B2C, vyberte **nahrát zásady**.
2. Povolit **přepsat zásady, pokud existuje**a poté vyhledejte a vyberte *TrustFrameworkExtensions.xml* souboru.
3. Klikněte na **Odeslat**.

## <a name="register-the-claims-provider"></a>Registrace zprostředkovatele deklarací identity

V tuto chvíli je nastavený zprostředkovatele identity, ale není k dispozici v některém z obrazovky registrace nebo přihlášení. Chcete-li k dispozici, vytvoření duplicitní cesty existující uživatele šablony a upravte ho tak, aby má také zprostředkovatele identity účtu Microsoft.

1. Otevřít *TrustFrameworkBase.xml* soubor z starter pack.
2. Vyhledejte a zkopírujte celý obsah **UserJourney** element, který zahrnuje `Id="SignUpOrSignIn"`.
3. Otevřít *TrustFrameworkExtensions.xml* a najít **Userjourney** elementu. Pokud element neexistuje, přidejte jeden.
4. Vložte celý obsah **UserJourney** element, který jste zkopírovali jako podřízený objekt **Userjourney** elementu.
5. Přejmenujte ID cesty uživatele. Například, `SignUpSignInMSA`.

### <a name="display-the-button"></a>Zobrazit tlačítko

**ClaimsProviderSelection** element je obdobou k tlačítku na obrazovce registrace nebo přihlášení zprostředkovatele identity. Pokud chcete přidat **ClaimsProviderSelection** – element pro účet Microsoft, nové tlačítko se zobrazí při uživatel umístil na stránce.

1. V *TrustFrameworkExtensions.xml* souboru, vyhledejte **OrchestrationStep** element, který zahrnuje `Order="1"` v cestě uživatele, který jste vytvořili.
2. V části **ClaimsProviderSelects**, přidejte následující prvek. Nastavte hodnotu **TargetClaimsExchangeId** na odpovídající hodnotu, například `MicrosoftAccountExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Tlačítko s odkazem na akci

Teď, když máte tlačítko na místě, budete potřebovat odkázat na akci. Akce v tomto případě je pro Azure AD B2C pro komunikaci pomocí účtu Twitteru získat token.

1. Najít **OrchestrationStep** , který obsahuje `Order="2"` v cestě uživatele.
2. Přidejte následující **ClaimsExchange** a ujistěte se, že používáte stejnou hodnotu pro element **Id** , který jste použili pro **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```
    
    Aktualizujte hodnotu **TechnicalProfileReferenceId** k **Id** technického profilu, který jste vytvořili dříve. Například, `MSA-OIDC`.

3. Uložit *TrustFrameworkExtensions.xml* souboru a nahrajte ji znovu pro ověření.

## <a name="update-and-test-the-relying-party-file"></a>Aktualizace a předávající strany soubor testu

Aktualizujte předávající stranu soubor, který iniciuje cesty uživatele, který jste vytvořili.

1. Vytvořte kopii *SignUpOrSignIn.xml* ve svém pracovním adresáři a přejmenujte jej. Například přejmenujte ho na *SignUpSignInMSA.xml*.
2. Otevřete nový soubor a aktualizujte hodnotu **PolicyId** atributu **TrustFrameworkPolicy** s jedinečnou hodnotu. Například, `SignUpSignInMSA`.
3. Aktualizujte hodnotu **PublicPolicyUri** s identifikátorem URI pro zásady. Například`http://contoso.com/B2C_1A_signup_signin_msa`
4. Aktualizujte hodnotu **ReferenceId** atribut **DefaultUserJourney** tak, aby odpovídaly ID nové cesty uživatele, který jste vytvořili (SignUpSignInMSA).
5. Uložte provedené změny, nahrajte soubor a otestovat ho otevřít a kliknutím na **spustit nyní**.
