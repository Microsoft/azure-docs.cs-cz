---
title: Nastavení přihlášení s účtem LinkedIn v Azure Active Directory B2C pomocí vlastních zásad | Dokumentace Microsoftu
description: Nastavení přihlášení pomocí účtu Google v Azure Active Directory B2C pomocí vlastních zásad.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 4817ad918af66080cec1faead96c6c9448387556
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181206"
---
# <a name="set-up-sign-in-with-a-linkedin-account-using-custom-policies-in-azure-active-directory-b2c"></a>Nastavení přihlášení s účtem LinkedIn pomocí vlastních zásad v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku se dozvíte, jak povolit přihlášení pro uživatele z účtu LinkedIn pomocí [vlastní zásady](active-directory-b2c-overview-custom.md) v Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Požadavky

- Proveďte kroky v [začít pracovat s vlastními zásadami v Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Pokud ještě nemáte účet LinkedIn, vytvořte si ho na [stránku pro přihlášení na LinkedIn](https://www.linkedin.com/start/join).
- LinkedIn aplikace vyžaduje poskytnutí obrázek loga 80 X 80 pixel reprezentuje vaše aplikace.

## <a name="create-an-application"></a>Vytvoření aplikace

Použití LinkedIn jako zprostředkovatele identity v Azure AD B2C, je potřeba vytvořit aplikaci LinkedIn.

1. Přihlaste se k [správy aplikací LinkedIn](https://www.linkedin.com/secure/developer?newapp=) pomocí svých přihlašovacích údajů účtu LinkedIn.
2. Vyberte **vytvořit aplikaci**.
3. Zadejte vaše **název společnosti**, **název_aplikace**a **popis aplikace**.
4. Nahrát **Logo aplikace** , kterou jste vytvořili.
5. Zvolte **využívání aplikací** ze zadaného seznamu.
6. Pro **adresu URL webu**, zadejte `https://your-tenant.b2clogin.com`.  Nahraďte `your-tenant` s názvem vašeho tenanta Azure AD B2C. Například contoso.b2clogin.com.
7. Zadejte vaše **e-mailová adresa** adresu a **Telefon do zaměstnání** číslo.
8. V dolní části stránky, přečtěte si a přijměte podmínky použití a pak vyberte **odeslat**.
9. Vyberte **ověřování**a potom si poznamenejte **ID klienta** a **tajný kód klienta** hodnoty pro pozdější použití.
10. V **oprávnění adresy URL pro přesměrování**, zadejte `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp`. Nahraďte `your-tenant-name` s názvem vašeho tenanta. Budete muset použít jenom malá písmena. Pokud zadáte název vašeho klienta i v případě, že klient je definována s velká písmena v Azure AD B2C. 
11. Vyberte **aktualizace**.
12. Vyberte **nastavení**, změnit **stav aplikace** k **Live**a pak vyberte **aktualizace**.

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Potřebujete ukládat tajný kód klienta, který jste si dříve poznamenali ve vašem tenantovi Azure AD B2C.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
4. Na stránce s přehledem, vyberte **architekturu rozhraní identit - PREVIEW**.
5. Vyberte **klíče zásad** a pak vyberte **přidat**.
6. Pro **možnosti**, zvolte `Manual`.
7. Zadejte **název** klíče zásad. Například, `LinkedInSecret`. Předpona, která `B2C_1A_` je automaticky přidán do názvu klíče.
8. V **tajný kód**, zadejte váš tajný klíč klienta, který jste si předtím poznamenali.
9. Pro **použití klíče**vyberte `Signature`.
10. Klikněte na možnost **Vytvořit**.

## <a name="add-a-claims-provider"></a>Přidat zprostředkovatele deklarací identity

Pokud chcete uživatelům umožní přihlásit se účtem LinkedIn, musíte definovat účtu jako zprostředkovatele deklarací identity, který Azure AD B2C můžou klienti komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací identity, které používají Azure AD B2C k ověření, že se ověřil konkrétního uživatele. 

Účet LinkedIn jako poskytovatele deklarací identity můžete definovat tak, že ji přidáte **ClaimsProviders** prvku v souboru rozšíření vašich zásad.

1. Otevřít *TrustFrameworkExtensions.xml*.
2. Najít **ClaimsProviders** elementu. Pokud neexistuje, přidejte jej pod kořenovým elementem.
3. Přidat nový **ClaimsProvider** následujícím způsobem:

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
            <Item Key="ClaimsEndpointAccessTokenName">oauth2_access_token</Item>
            <Item Key="ClaimsEndpointFormatName">format</Item>
            <Item Key="ClaimsEndpointFormat">json</Item>
            <Item Key="scope">r_emailaddress r_basicprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
            <!--<OutputClaim ClaimTypeReferenceId="jobTitle" PartnerClaimType="headline" />-->
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" />
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

4. Nahraďte hodnotu **client_id** s ID klienta, který jste si předtím poznamenali.
5. Uložte soubor.

### <a name="upload-the-extension-file-for-verification"></a>Nahrát soubor rozšíření pro ověřování

Nyní jste nakonfigurovali zásady tak, aby Azure AD B2C ví, jak komunikovat s vaším účtem LinkedIn. Zkuste nahrát soubor rozšíření zásady jenom k potvrzení, že všechny problémy nemusí zatím.

1. Na **vlastní zásady** stránky ve vašem tenantovi Azure AD B2C, vyberte **nahrát zásady**.
2. Povolit **přepsat zásady, pokud existuje**a poté vyhledejte a vyberte *TrustFrameworkExtensions.xml* souboru.
3. Klikněte na **Odeslat**.

## <a name="register-the-claims-provider"></a>Registrace zprostředkovatele deklarací identity

V tuto chvíli je nastavený zprostředkovatele identity, ale není k dispozici v některém z obrazovky registrace nebo přihlášení. Chcete-li k dispozici, vytvoření duplicitní cesty existující uživatele šablony a upravte ho tak, aby má také LinkedIn zprostředkovatele identity.

1. Otevřít *TrustFrameworkBase.xml* soubor z starter pack.
2. Vyhledejte a zkopírujte celý obsah **UserJourney** element, který zahrnuje `Id="SignUpOrSignIn"`.
3. Otevřít *TrustFrameworkExtensions.xml* a najít **Userjourney** elementu. Pokud element neexistuje, přidejte jeden.
4. Vložte celý obsah **UserJourney** element, který jste zkopírovali jako podřízený objekt **Userjourney** elementu.
5. Přejmenujte ID cesty uživatele. Například, `SignUpSignInLinkedIn`.

### <a name="display-the-button"></a>Zobrazit tlačítko

**ClaimsProviderSelection** element je obdobou k tlačítku na obrazovce registrace nebo přihlášení zprostředkovatele identity. Pokud chcete přidat **ClaimsProviderSelection** – element pro účet LinkedIn, nové tlačítko se zobrazí při uživatel umístil na stránce.

1. Najít **OrchestrationStep** element, který zahrnuje `Order="1"` v cestě uživatele, který jste vytvořili.
2. V části **ClaimsProviderSelects**, přidejte následující prvek. Nastavte hodnotu **TargetClaimsExchangeId** na odpovídající hodnotu, například `LinkedInExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Tlačítko s odkazem na akci

Teď, když máte tlačítko na místě, budete potřebovat odkázat na akci. Akce v tomto případě je pro Azure AD B2C ke komunikaci s účtem LinkedIn k získání tokenu.

1. Najít **OrchestrationStep** , který obsahuje `Order="2"` v cestě uživatele.
2. Přidejte následující **ClaimsExchange** a ujistěte se, že používáte stejnou hodnotu pro element **Id** , který jste použili pro **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```
    
    Aktualizujte hodnotu **TechnicalProfileReferenceId** k **Id** technického profilu, který jste vytvořili dříve. Například, `LinkedIn-OAUTH`.

3. Uložit *TrustFrameworkExtensions.xml* souboru a nahrajte ji znovu pro ověření.

## <a name="update-and-test-the-relying-party-file"></a>Aktualizace a předávající strany soubor testu

Aktualizujte předávající stranu soubor, který iniciuje cesty uživatele, který jste vytvořili.

1. Vytvořte kopii *SignUpOrSignIn.xml* ve svém pracovním adresáři a přejmenujte jej. Například přejmenujte ho na *SignUpSignInLinkedIn.xml*.
2. Otevřete nový soubor a aktualizujte hodnotu **PolicyId** atributu **TrustFrameworkPolicy** s jedinečnou hodnotu. Například, `SignUpSignInLinkedIn`.
3. Aktualizujte hodnotu **PublicPolicyUri** s identifikátorem URI pro zásady. Například`http://contoso.com/B2C_1A_signup_signin_linkedin`
4. Aktualizujte hodnotu **ReferenceId** atribut **DefaultUserJourney** tak, aby odpovídaly ID nové cesty uživatele, který jste vytvořili (SignUpSignLinkedIn).
5. Uložte provedené změny, nahrajte soubor a otestovat ho otevřít a kliknutím na **spustit nyní**.
