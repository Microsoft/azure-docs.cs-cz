---
title: Nastavení přihlášení pomocí účtu Google pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Nastavte přihlášení pomocí účtu Google v Azure Active Directory B2C pomocí vlastních zásad.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 91a55782492c1b2612652b147e0aca37941bf4db
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388200"
---
# <a name="set-up-sign-in-with-a-google-account-using-custom-policies-in-azure-active-directory-b2c"></a>Nastavte přihlášení pomocí účtu Google pomocí vlastních zásad v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku se dozvíte, jak povolit přihlášení uživatelům s účtem Google pomocí [vlastních zásad](custom-policy-overview.md) v Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Požadavky

- Dokončete kroky v části [Začínáme s vlastními zásadami v Active Directory B2C](custom-policy-get-started.md).
- Pokud ještě nemáte účet Google, vytvořte si ho vytvořením [účtu Google](https://accounts.google.com/SignUp).

## <a name="register-the-application"></a>Registrace aplikace

Pokud chcete povolit přihlášení uživatelům z účtu Google, musíte vytvořit projekt aplikace Google.

1. Přihlaste se ke [konzole pro vývojáře Google](https://console.developers.google.com/) pomocí svých přihlašovacích údajů k účtu.
2. Zadejte **název projektu**, klikněte na **vytvořit**a potom se ujistěte, že používáte nový projekt.
3. V nabídce vlevo vyberte **přihlašovací údaje** a potom vyberte **vytvořit přihlašovací údaje > ID klienta OAuth**.
4. Vyberte možnost **Konfigurovat obrazovku pro vyjádření souhlasu**.
5. Vyberte nebo zadejte platnou **e-mailovou adresu**, zadejte **název produktu** , který se zobrazí uživatelům, zadejte `b2clogin.com` v **autorizovaných doménách**a pak klikněte na **Uložit**.
6. V části **Typ aplikace**vyberte **Webová aplikace**.
7. Zadejte **název** vaší aplikace.
8. V **autorizovaných zdrojích JavaScriptu**zadejte `https://your-tenant-name.b2clogin.com` a v **autorizovaných identifikátorech URI pro přesměrování**zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Nahraďte název-tenanta názvem vašeho tenanta. Při zadávání názvu tenanta musíte použít malá písmena, i když je tenant definovaný velkými písmeny v Azure AD B2C.
8. Klikněte na **Vytvořit**.
9. Zkopírujte hodnoty **ID klienta** a **tajný klíč klienta**. Obě tyto služby budete potřebovat ke konfiguraci Google jako poskytovatele identity ve vašem tenantovi. Tajný kód klienta je důležité bezpečnostní pověření.

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Je potřeba uložit tajný klíč klienta, který jste předtím nahráli ve svém tenantovi Azure AD B2C.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Na stránce Přehled vyberte možnost **Architektura prostředí identity**.
5. Vyberte **klíče zásad** a pak vyberte **Přidat**.
6. Pro **Možnosti**vyberte možnost `Manual` .
7. Zadejte **název** klíče zásad. Například, `GoogleSecret`. Předpona `B2C_1A_` se automaticky přidá do názvu vašeho klíče.
8. Do **tajného klíče**zadejte tajný klíč klienta, který jste předtím nahráli.
9. Pro **použití klíče**vyberte `Signature` .
10. Klikněte na **Vytvořit**.

## <a name="add-a-claims-provider"></a>Přidat zprostředkovatele deklarací identity

Pokud chcete, aby se uživatelé přihlásili pomocí účtu Google, musíte účet definovat jako zprostředkovatele deklarací identity, se kterým Azure AD B2C můžou komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací, které používá Azure AD B2C k ověření, že konkrétní uživatel byl ověřen.

Účet Google můžete definovat jako zprostředkovatele deklarací, a to tak, že ho přidáte do prvku **ClaimsProviders** v souboru rozšíření zásady.

1. Otevřete *TrustFrameworkExtensions.xml*.
2. Vyhledejte element **ClaimsProviders** . Pokud neexistuje, přidejte jej pod kořenový element.
3. Přidejte nový **ClaimsProvider** následujícím způsobem:

    ```xml
    <ClaimsProvider>
      <Domain>google.com</Domain>
      <DisplayName>Google</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Google-OAUTH">
          <DisplayName>Google</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">google</Item>
            <Item Key="authorization_endpoint">https://accounts.google.com/o/oauth2/auth</Item>
            <Item Key="AccessTokenEndpoint">https://accounts.google.com/o/oauth2/token</Item>
            <Item Key="ClaimsEndpoint">https://www.googleapis.com/oauth2/v1/userinfo</Item>
            <Item Key="scope">email profile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your Google application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="google.com" />
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

4. Nastavte **client_id** na ID aplikace z registrace aplikace.
5. Uložte soubor.

### <a name="upload-the-extension-file-for-verification"></a>Nahrajte soubor rozšíření pro ověření.

Teď jste nakonfigurovali zásady tak, aby Azure AD B2C vědět, jak komunikovat s adresářem služby Azure AD. Zkuste nahrát soubor s příponou zásady jenom tak, aby se ověřilo, že zatím nemá žádné problémy.

1. Na stránce **vlastní zásady** ve vašem tenantovi Azure AD B2C vyberte **Odeslat zásadu**.
2. Pokud existuje, zapněte **zásadu přepsat**a pak vyhledejte a vyberte soubor *TrustFrameworkExtensions.xml* .
3. Klikněte na **Odeslat**.

## <a name="register-the-claims-provider"></a>Registrace zprostředkovatele deklarací identity

V tuto chvíli je poskytovatel identity nastavený, ale není k dispozici na žádném z obrazovek pro registraci a přihlašování. Aby byl k dispozici, vytvořte duplikát existující cesty uživatele šablony a pak ji upravte, aby měl také poskytovatele identity služby Azure AD.

1. Otevřete soubor *TrustFrameworkBase.xml* z úvodní sady.
2. Vyhledejte a zkopírujte celý obsah prvku **UserJourney** , který obsahuje `Id="SignUpOrSignIn"` .
3. Otevřete *TrustFrameworkExtensions.xml* a vyhledejte element **userjourney** . Pokud element neexistuje, přidejte jej.
4. Vložte celý obsah elementu **UserJourney** , který jste zkopírovali jako podřízený prvek **userjourney** elementu.
5. Přejmenujte ID cesty pro uživatele. Například, `SignUpSignInGoogle`.

### <a name="display-the-button"></a>Zobrazit tlačítko

Element **claimsproviderselection.** se podobá tlačítku poskytovatele identity na obrazovce pro registraci a přihlašování. Pokud přidáte element **claimsproviderselection.** pro účet Google, zobrazí se nové tlačítko, když se uživatel na stránce zařadí.

1. Vyhledejte element **OrchestrationStep** , který obsahuje `Order="1"` cestu k uživateli, kterou jste vytvořili.
2. Pod **ClaimsProviderSelects**přidejte následující element. Nastavte hodnotu **TargetClaimsExchangeId** na odpovídající hodnotu, například `GoogleExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Propojit tlačítko s akcí

Teď, když máte tlačítko na místě, musíte ho propojit s akcí. Tato akce je v tomto případě určena pro Azure AD B2C ke komunikaci s účtem Google pro příjem tokenu.

1. Najděte **OrchestrationStep** , který obsahuje `Order="2"` cestu k uživateli.
2. Přidejte následující prvek **ClaimsExchange** a ujistěte se, že používáte stejnou hodnotu pro ID, které jste použili pro **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAuth" />
    ```

    Aktualizujte hodnotu **TechnicalProfileReferenceId** na ID technického profilu, který jste vytvořili dříve. Například, `Google-OAuth`.

3. Uložte soubor *TrustFrameworkExtensions.xml* a znovu ho nahrajte pro účely ověření.

## <a name="create-an-azure-ad-b2c-application"></a>Vytvoření aplikace Azure AD B2C

Komunikace s Azure AD B2C probíhá prostřednictvím aplikace, kterou zaregistrujete v tenantovi B2C. V této části jsou uvedeny volitelné kroky, které můžete provést, chcete-li vytvořit testovací aplikaci, pokud jste tak již neučinili.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aktualizace a testování souboru předávající strany

Aktualizujte soubor předávající strany (RP), který iniciuje cestu uživatele, kterou jste vytvořili.

1. Vytvořte kopii *SignUpOrSignIn.xml* v pracovním adresáři a přejmenujte ji. Přejmenujte ho například na *SignUpSignInGoogle.xml*.
2. Otevřete nový soubor a aktualizujte hodnotu atributu **PolicyId** pro **TrustFrameworkPolicy** s jedinečnou hodnotou. Například, `SignUpSignInGoogle`.
3. Aktualizujte hodnotu **PUBLICPOLICYURI** identifikátorem URI pro zásadu. Například`http://contoso.com/B2C_1A_signup_signin_google`
4. Aktualizujte hodnotu atributu **ReferenceId** v **DefaultUserJourney** tak, aby odpovídala ID nové cesty uživatele, kterou jste vytvořili (SignUpSignGoogle).
5. Uložte změny, nahrajte soubor a pak v seznamu vyberte novou zásadu.
6. Ujistěte se, že je vybrána možnost Azure AD B2C aplikace, kterou jste vytvořili v poli **Vybrat aplikaci** , a poté ji otestujte kliknutím na tlačítko **Spustit nyní**.
