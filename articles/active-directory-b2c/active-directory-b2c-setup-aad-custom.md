---
title: Nastavení přihlášení pomocí účtu služby Azure Active Directory v Azure Active Directory B2C pomocí vlastních zásad | Dokumentace Microsoftu
description: Nastavte, přihlaste se pomocí účtu služby Azure Active Directory v Azure Active Directory B2C pomocí vlastních zásad.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 917d4a85ffe642146325bc9bf62068320ad5e79a
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54845482"
---
# <a name="set-up-sign-in-with-an-azure-active-directory-account-using-custom-policies-in-azure-active-directory-b2c"></a>Nastavit přihlašování pomocí účtu služby Azure Active Directory pomocí vlastních zásad v Azure Active Directory B2C 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku se dozvíte, jak povolit přihlášení pro uživatele z organizace Azure Active Directory (Azure AD) pomocí [vlastní zásady](active-directory-b2c-overview-custom.md) v Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v [začít pracovat s vlastními zásadami v Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Registrace aplikace

Povolit přihlášení pro uživatele z konkrétní organizace služby Azure AD, budete muset zaregistrovat aplikaci v rámci organizační tenanta Azure AD.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresáře, který obsahuje organizace Azure AD tenant (contoso.com) kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **registrace aplikací**.
4. Vyberte **Registrace nové aplikace**.
5. Zadejte název své aplikace. Například, `Azure AD B2C App`.
6. Pro **typ aplikace**vyberte `Web app / API`.
7. Pro **přihlašovací adresa URL**, zadejte následující adresu URL malými písmeny, kde `your-B2C-tenant-name` se nahradí názvem vašeho tenanta Azure AD B2C:

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Například, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

8. Klikněte na možnost **Vytvořit**. Kopírovat **ID aplikace** pro pozdější použití.
9. Vyberte aplikaci a pak vyberte **nastavení**.
10. Vyberte **klíče**, zadejte popis klíče, vyberte dobu trvání a potom klikněte na tlačítko **Uložit**. Zkopírujte hodnotu klíče, který se zobrazí pro pozdější použití.

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Potřebujete k uložení klíče aplikace, kterou jste vytvořili ve vašem tenantovi Azure AD B2C.

1. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
2. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
3. Na stránce s přehledem, vyberte **architekturu rozhraní identit - PREVIEW**.
4. Vyberte **klíče zásad** a pak vyberte **přidat**.
5. Pro **možnosti**, zvolte `Manual`.
6. Zadejte **název** klíče zásad. Například, `ContosoAppSecret`.  Předpona, která `B2C_1A_` je automaticky přidán do názvu klíče.
7. V **tajný kód**, zadejte svůj klíč aplikace, který jste si předtím poznamenali.
8. Pro **použití klíče**vyberte `Signature`.
9. Klikněte na možnost **Vytvořit**.

## <a name="add-a-claims-provider"></a>Přidat zprostředkovatele deklarací identity

Pokud chcete uživatelům umožní přihlásit pomocí Azure AD, musíte definovat Azure AD jako poskytovatele deklarací identity, který Azure AD B2C můžou klienti komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací identity, které používají Azure AD B2C k ověření, že se ověřil konkrétního uživatele. 

Azure AD jako poskytovatele deklarací identity můžete definovat tak, že přidáte Azure AD, aby **ClaimsProvider** prvku v souboru rozšíření vašich zásad.

1. Otevřít *TrustFrameworkExtensions.xml*.
2. Najít **ClaimsProviders** elementu. Pokud neexistuje, přidejte jej pod kořenovým elementem.
3. Přidat nový **ClaimsProvider** následujícím způsobem:

    ```XML
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="ContosoProfile">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <OutputTokenFormat>JWT</OutputTokenFormat>
          <Metadata>
            <Item Key="METADATA">https://login.windows.net/your-AD-tenant-name.onmicrosoft.com/.well-known/openid-configuration</Item>
            <Item Key="ProviderName">https://sts.windows.net/00000000-0000-0000-0000-000000000000/</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="IdTokenAudience">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="AzureADContoso" AlwaysUseDefaultValue="true" />
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

4. V části **ClaimsProvider** elementu, aktualizujte hodnotu **domény** na jedinečnou hodnotu, která je možné, aby se odlišil od jiných zprostředkovatelů identity. Například `Contoso`. Není vložíte `.com` na konci tohoto nastavení domény.
5. V části **ClaimsProvider** elementu, aktualizujte hodnotu **DisplayName** na popisný název pro zprostředkovatele deklarací identity. Tato hodnota není aktuálně používán.

### <a name="update-the-technical-profile"></a>Aktualizace technický profil

Chcete-li získat token z koncového bodu Azure AD, musíte definovat protokoly, které Azure AD B2C použít ke komunikaci s Azure AD. To se provádí uvnitř **technický profil** prvek **ClaimsProvider**.

1. Aktualizace ID **technický profil** elementu. Toto ID se používá k odkazování na tento technický profil z jiných částí zásad.
2. Aktualizujte hodnotu **DisplayName**. Tato hodnota se zobrazí na tlačítko přihlásit na obrazovce přihlášení.
3. Aktualizujte hodnotu **popis**.
4. Azure AD používá protokol OpenID Connect, tak zkontrolujte, zda hodnota **protokol** je `OpenIdConnect`.
5. Nastavte hodnotu **METADAT** k `https://login.windows.net/your-AD-tenant-name.onmicrosoft.com/.well-known/openid-configuration`, kde `your-AD-tenant-name` je název tenanta Azure AD. Například `https://login.windows.net/fabrikam.onmicrosoft.com/.well-known/openid-configuration`.
6. Otevřete prohlížeč a přejděte na **METADAT** adresu URL, kterou jste právě aktualizovali, hledejte pro **vystavitele** objektu, zkopírujte a vložte tuto hodnotu na hodnotu pro **ProviderName** v souboru XML.
8. Nastavte **client_id** a **IdTokenAudience** do ID aplikace z registrace aplikace.
9. V části **CryptograhicKeys**, aktualizujte hodnotu **StorageReferenceId** do klíče zásad, který jste definovali. Například, `ContosoAppSecret`.

### <a name="upload-the-extension-file-for-verification"></a>Nahrát soubor rozšíření pro ověřování

Nyní jste nakonfigurovali zásady tak, aby Azure AD B2C ví, jak komunikovat s adresářem Azure AD. Zkuste nahrát soubor rozšíření zásady jenom k potvrzení, že všechny problémy nemusí zatím.

1. Na **vlastní zásady** stránky ve vašem tenantovi Azure AD B2C, vyberte **nahrát zásady**.
2. Povolit **přepsat zásady, pokud existuje**a poté vyhledejte a vyberte *TrustFrameworkExtensions.xml* souboru.
3. Klikněte na **Odeslat**.

## <a name="register-the-claims-provider"></a>Registrace zprostředkovatele deklarací identity

V tomto okamžiku je nastavený zprostředkovatele identity, ale není k dispozici v některém z obrazovky přihlášení-registrace/přihlášení. Chcete-li k dispozici, vytvoření duplicitní cesty existující uživatele šablony a upravte ho tak, aby má také zprostředkovatele identity Azure AD:

1. Otevřít *TrustFrameworkBase.xml* soubor z starter pack.
2. Vyhledejte a zkopírujte celý obsah **UserJourney** element, který zahrnuje `Id="SignUpOrSignIn"`.
3. Otevřít *TrustFrameworkExtensions.xml* a najít **Userjourney** elementu. Pokud element neexistuje, přidejte jeden.
4. Vložte celý obsah **UserJourney** element, který jste zkopírovali jako podřízený objekt **Userjourney** elementu.
5. Přejmenujte ID cesty uživatele. Například, `SignUpSignInContoso`.

### <a name="display-the-button"></a>Zobrazit tlačítko

**ClaimsProviderSelection** element je obdobou k tlačítku na obrazovce přihlášení-registrace/přihlášení zprostředkovatele identity. Pokud chcete přidat **ClaimsProviderSelection** – element pro Azure AD, nové tlačítko se zobrazí při uživatel umístil na stránce.

1. Najít **OrchestrationStep** element, který zahrnuje `Order="1"` v cestě uživatele, který jste vytvořili.
2. V části **ClaimsProviderSelections**, přidejte následující prvek. Nastavte hodnotu **TargetClaimsExchangeId** na odpovídající hodnotu, například `ContosoExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Tlačítko s odkazem na akci

Teď, když máte tlačítko na místě, budete potřebovat odkázat na akci. Akce v tomto případě je pro Azure AD B2C ke komunikaci s Azure AD za účelem získání tokenu. Tlačítko odkazu na akci propojením technický profil pro vašeho poskytovatele deklarací identity Azure AD:

1. Najít **OrchestrationStep** , který obsahuje `Order="2"` v cestě uživatele.
2. Přidejte následující **ClaimsExchange** a ujistěte se, že používáte stejnou hodnotu pro element **Id** , který jste použili pro **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```
    
    Aktualizujte hodnotu **TechnicalProfileReferenceId** k **Id** technického profilu, který jste vytvořili dříve. Například, `ContosoProfile`.

3. Uložit *TrustFrameworkExtensions.xml* souboru a nahrajte ji znovu pro ověření.

## <a name="create-an-azure-ad-b2c-application"></a>Vytvoření aplikace Azure AD B2C

Probíhá komunikace s Azure AD B2c prostřednictvím aplikace, kterou vytvoříte ve vašem tenantovi. Tato část obsahuje seznam volitelné kroky, které můžete použít k vytvoření aplikace testů, pokud jste tak již neučinili.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **aplikací**a pak vyberte **přidat**.
5. Zadejte název aplikace, například *testapp1*.
6. Pro **webová aplikace / webové rozhraní API**vyberte `Yes`a pak zadejte `https://jwt.ms` pro **adresy URL odpovědi**.
7. Klikněte na možnost **Vytvořit**.

## <a name="update-and-test-the-relying-party-file"></a>Aktualizace a předávající strany soubor testu

Aktualizujte předávající stranu soubor, který iniciuje cesty uživatele, který jste vytvořili.

1. Vytvořte kopii *SignUpOrSignIn.xml* ve svém pracovním adresáři a přejmenujte jej. Například přejmenujte ho na *SignUpSignInContoso.xml*.
2. Otevřete nový soubor a aktualizujte hodnotu **PolicyId** atributu **TrustFrameworkPolicy** s jedinečnou hodnotu. Například, `SignUpSignInContoso`.
3. Aktualizujte hodnotu **PublicPolicyUri** s identifikátorem URI pro zásady. Například`http://contoso.com/B2C_1A_signup_signin_contoso`
4. Aktualizujte hodnotu **ReferenceId** atribut **DefaultUserJourney** tak, aby odpovídaly ID nové cesty uživatele, který jste vytvořili (SignUpSignInContoso).
5. Uložte provedené změny, nahrajte soubor a pak v seznamu vyberte novou zásadu.
6. Ujistěte se, že je vybraná aplikaci Azure AD B2C, kterou jste vytvořili v **vyberte aplikaci** pole a pak ho otestujte kliknutím **spustit nyní**.

