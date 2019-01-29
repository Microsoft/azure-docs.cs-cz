---
title: Nastavení přihlášení pro zprostředkovatele identity více tenanty Azure AD pomocí vlastních zásad v Azure Active Directory B2C | Dokumentace Microsoftu
description: Přidáte zprostředkovatele identity více tenanty Azure AD pomocí vlastních zásad – Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 41b170ea66b1cb4c830ad0327ac2e1e3d2922b04
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55160721"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Nastavení přihlášení pro více tenantů Azure Active Directory pomocí vlastních zásad v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku se dozvíte, jak povolit přihlášení pro uživatele, kteří používají více tenantů koncový bod pro službu Azure Active Directory (Azure AD) pomocí [vlastní zásady](active-directory-b2c-overview-custom.md) v Azure AD B2C. To umožňuje uživatelům z více tenantů Azure AD pro přihlášení do Azure AD B2C bez konfigurace technické zprostředkovatele pro jednotlivé tenanty. Ale hosta členy v některém z těchto klientů **nebudou** moct přihlásit. K tomu je potřeba [konfigurovat jednotlivě každý tenant](active-directory-b2c-setup-aad-custom.md).

>[!NOTE]
>`Contoso.com` slouží k organizační tenanta Azure AD a `fabrikamb2c.onmicrosoft.com` slouží jako tenant Azure AD B2C v následujících pokynech.

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
7. Pro **přihlašovací adresa URL**, zadejte následující adresu URL malými písmeny, kde `your-tenant` se nahradí názvem vašeho tenanta Azure AD B2C (fabrikamb2c.onmicrosoft.com):

    ```
    https://yourtenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp
    ```
    
8. Klikněte na možnost **Vytvořit**. Kopírovat **ID aplikace** pro pozdější použití.
9. Vyberte aplikaci a pak vyberte **nastavení**.
10. Vyberte **klíče**, zadejte popis klíče, vyberte dobu trvání a potom klikněte na tlačítko **Uložit**. Zkopírujte hodnotu klíče, který se zobrazí pro pozdější použití.
11. V části **nastavení**vyberte **vlastnosti**, nastavte **víceklientských** k `Yes`a potom klikněte na tlačítko **uložit**

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
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>

            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <!-- Make sure to update the reference ID of the client secret below you just created (B2C_1A_AADAppSecret) -->
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
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

4. V části **ClaimsProvider** elementu, aktualizujte hodnotu **domény** na jedinečnou hodnotu, která je možné, aby se odlišil od jiných zprostředkovatelů identity.
5. V části **technický profil** elementu, aktualizujte hodnotu **DisplayName**. Tato hodnota je zobrazený na tlačítku přihlášení na obrazovce přihlášení.
6. Nastavte **client_id** do ID aplikace z registrace aplikace mulity tenanta služby Azure AD.

### <a name="restrict-access"></a>Omezení přístupu

> [!NOTE]
> Pomocí `https://sts.windows.net` hodnotu **ValidTokenIssuerPrefixes** umožňuje všem uživatelům Azure AD k přihlášení do vaší aplikace.

Budete muset aktualizovat seznam platní Vystavitelé tokenů a omezit přístup na konkrétní seznam uživatelů z tenanta Azure AD, kteří se můžete přihlásit. K získání hodnot, je potřeba podívejte se na metadata pro každou z konkrétních klientů Azure AD, která byste chtěli nechat uživatele přihlásit z. Formát dat vypadá takto: `https://login.windows.net/your-tenant/.well-known/openid-configuration`, kde `your-tenant` je název tenanta Azure AD (contoso.com nebo žádným jiným tenantem Azure AD).

1. Otevřete prohlížeč a přejděte na **METADAT** adresy URL a vyhledat **vystavitele** objektu a zkopírujte jeho hodnotu. Měl by vypadat nějak takto: `https://sts.windows.net/tenant-id/`.
2. Zkopírujte a vložte hodnotu **ValidTokenIssuerPrefixes** klíč. Můžete přidat více, oddělte je čárkou. Příkladem je zakomentovaný v ukázce výše XML.

### <a name="upload-the-extension-file-for-verification"></a>Nahrát soubor rozšíření pro ověřování

Nyní jste nakonfigurovali zásady tak, aby Azure AD B2C ví, jak komunikovat s adresářem Azure AD. Zkuste nahrát soubor rozšíření zásady jenom k potvrzení, že všechny problémy nemusí zatím.

1. Na **vlastní zásady** stránky ve vašem tenantovi Azure AD B2C, vyberte **nahrát zásady**.
2. Povolit **přepsat zásady, pokud existuje**a poté vyhledejte a vyberte *TrustFrameworkExtensions.xml* souboru.
3. Klikněte na **Odeslat**.

## <a name="register-the-claims-provider"></a>Registrace zprostředkovatele deklarací identity

V tomto okamžiku je nastavený zprostředkovatele identity, ale není k dispozici v některém z obrazovky přihlášení-registrace/přihlášení. Chcete-li k dispozici, vytvoření duplicitní cesty existující uživatele šablony a upravte ho tak, aby má také zprostředkovatele identity Azure AD.

1. Otevřít *TrustFrameworkBase.xml* soubor z starter pack.
2. Vyhledejte a zkopírujte celý obsah **UserJourney** element, který zahrnuje `Id="SignUpOrSignIn"`.
3. Otevřít *TrustFrameworkExtensions.xml* a najít **Userjourney** elementu. Pokud element neexistuje, přidejte jeden.
4. Vložte celý obsah **UserJourney** element, který jste zkopírovali jako podřízený objekt **Userjourney** elementu.
5. Přejmenujte ID cesty uživatele. Například, `SignUpSignInContoso`.

### <a name="display-the-button"></a>Zobrazit tlačítko

**ClaimsProviderSelection** element je obdobou k tlačítku na obrazovce přihlášení-registrace/přihlášení zprostředkovatele identity. Pokud chcete přidat **ClaimsProviderSelection** – element pro Azure AD, nové tlačítko se zobrazí při uživatel umístil na stránce.

1. Najít **OrchestrationStep** element, který zahrnuje `Order="1"` v cestě uživatele, který jste vytvořili.
2. V části **ClaimsProviderSelects**, přidejte následující prvek. Nastavte hodnotu **TargetClaimsExchangeId** na odpovídající hodnotu, například `AzureADExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Tlačítko s odkazem na akci

Teď, když máte tlačítko na místě, budete potřebovat odkázat na akci. Akce v tomto případě je pro Azure AD B2C ke komunikaci s Azure AD za účelem získání tokenu. Tlačítko s odkazem na akci propojením technický profil pro vašeho poskytovatele deklarací identity Azure AD.

1. Najít **OrchestrationStep** , který obsahuje `Order="2"` v cestě uživatele.
2. Přidejte následující **ClaimsExchange** a ujistěte se, že používáte stejnou hodnotu pro element **Id** , který jste použili pro **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```
    
    Aktualizujte hodnotu **TechnicalProfileReferenceId** k **Id** technického profilu, který jste vytvořili dříve. Například, `Common-AAD`.

3. Uložit *TrustFrameworkExtensions.xml* souboru a nahrajte ji znovu pro ověření.

## <a name="create-an-azure-ad-b2c-application"></a>Vytvoření aplikace Azure AD B2C

Probíhá komunikace s Azure AD B2C prostřednictvím aplikace, kterou vytvoříte ve vašem tenantovi. Tato část obsahuje seznam volitelné kroky, které můžete použít k vytvoření aplikace testů, pokud jste tak již neučinili.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **aplikací**a pak vyberte **přidat**.
5. Zadejte název aplikace, například *testapp1*.
6. Pro **webová aplikace / webové rozhraní API**vyberte `Yes`a pak zadejte `https://jwt.ms` pro **adresy URL odpovědi**.
7. Klikněte na možnost **Vytvořit**.

## <a name="update-and-test-the-relying-party-file"></a>Aktualizace a předávající strany soubor testu

Aktualizujte předávající stranu soubor, který iniciuje cesty uživatele, který jste vytvořili.

1. Vytvořte kopii *SignUpOrSignIn.xml* ve svém pracovním adresáři a přejmenujte jej. Například přejmenujte ho na *SignUpSignContoso.xml*.
2. Otevřete nový soubor a aktualizujte hodnotu **PolicyId** atributu **TrustFrameworkPolicy** s jedinečnou hodnotu. Například, `SignUpSignInContoso`.
3. Aktualizujte hodnotu **PublicPolicyUri** s identifikátorem URI pro zásady. Například`http://contoso.com/B2C_1A_signup_signin_contoso`
4. Aktualizujte hodnotu **ReferenceId** atribut **DefaultUserJourney** tak, aby odpovídaly ID nové cesty uživatele, který jste vytvořili (SignUpSignContoso).
5. Uložte provedené změny, nahrajte soubor a pak v seznamu vyberte novou zásadu.
6. Ujistěte se, že je vybraná aplikaci Azure AD B2C, kterou jste vytvořili v **vyberte aplikaci** pole a pak ho otestujte kliknutím **spustit nyní**.
