---
title: Přidat služby Google + jako zprostředkovatele identity OAuth2 pomocí vlastních zásad v Azure Active Directory B2C | Dokumentace Microsoftu
description: Ukázka pomocí Google + jako zprostředkovatele identity pomocí protokolu OAuth2.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: f076a906ba38e6c8e8c9530baba1607553b41ea6
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338324"
---
# <a name="azure-active-directory-b2c-add-google-as-an-oauth2-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: Přidání Google + jako zprostředkovatele identity OAuth2 pomocí vlastních zásad

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento průvodce vám ukáže, jak povolit přihlášení pro uživatele z účtu Google + prostřednictvím [vlastní zásady](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v [Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md) článku.

Tyto kroky zahrnují:

1.  Vytvoření aplikace pro účet Google +.
2.  Přidání aplikace klíč účtu Google + do Azure AD B2C
3.  Přidání poskytovatele deklarací identity do zásad
4.  Registrace Google + účet zprostředkovatele deklarací identity pro cestu uživatele
5.  Odeslání zásady do Azure AD B2C tenanta a testování

## <a name="create-a-google-account-application"></a>Vytvoření účtu aplikace Google +.
Pokud chcete používat Google + jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, budete muset vytvoření aplikace služby Google + a zadejte správné parametry. Můžete zaregistrovat Google + aplikace tady: [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)

1.  Přejděte [konzole pro vývojáře Google](https://console.developers.google.com/) a přihlaste se pomocí pověření účtu Google +.
2.  Klikněte na tlačítko **vytvořit projekt**, zadejte **název projektu**a potom klikněte na tlačítko **vytvořit**.

3.  Klikněte na **nabídce projekty**.

    ![Google + účet – výběr projektu](media/active-directory-b2c-custom-setup-goog-idp/goog-add-new-app1.png)

4.  Klikněte na **+** tlačítko.

    ![Google + účtu – vytvoření nového projektu](media/active-directory-b2c-custom-setup-goog-idp//goog-add-new-app2.png)

5.  Zadejte **název projektu**a potom klikněte na tlačítko **vytvořit**.

    ![Google + účet – nový projekt](media/active-directory-b2c-custom-setup-goog-idp//goog-app-name.png)

6.  Počkejte, až projekt je připravený a klikněte na **nabídce projekty**.

    ![Google + účet – Počkejte, než je připravený k použití nového projektu](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app1.png)

7.  Klikněte na název vašeho projektu.

    ![Google + účet – vyberte nový projekt](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app2.png)

8.  Klikněte na tlačítko **správce rozhraní API** a potom klikněte na tlačítko **pověření** v levém navigačním panelu.
9.  Klikněte na tlačítko **obrazovku se souhlasem OAuth** kartě v horní části.

    ![Google + účet – obrazovku se souhlasem OAuth nastavení](media/active-directory-b2c-custom-setup-goog-idp/goog-add-cred.png)

10.  Vyberte nebo zadejte platný **e-mailová adresa**, zadejte **název produktu**a klikněte na tlačítko **Uložit**.

    ![Google +.-aplikace přihlašovacích údajů](media/active-directory-b2c-custom-setup-goog-idp/goog-consent-screen.png)

11.  Klikněte na tlačítko **nové přihlašovací údaje** a klikněte na tlačítko **ID klienta OAuth**.

    ![Google + - vytvořit nové přihlašovací údaje aplikace](media/active-directory-b2c-custom-setup-goog-idp/goog-add-oauth2-client-id.png)

12.  V části **typ aplikace**vyberte **webovou aplikaci**.

    ![Google + – výběr typu aplikace](media/active-directory-b2c-custom-setup-goog-idp/goog-web-app.png)

13.  Zadejte **název** pro vaši aplikaci, zadejte `https://{tenant}.b2clogin.com` v **oprávnění JavaScript zdroje** pole, a `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` v **identifikátory URI pro přesměrování autorizovaní** pole. Nahraďte **{klient}** s názvem vašeho tenanta (například contosob2c). **{Klient}** hodnota je velká a malá písmena. Klikněte na možnost **Vytvořit**.

    ![Google +. - zadat oprávnění JavaScript zdroje a identifikátory URI pro přesměrování](media/active-directory-b2c-custom-setup-goog-idp/goog-create-client-id.png)

14.  Zkopírujte hodnoty **Id klienta** a **tajný kód klienta**. Je třeba obě konfigurace Google + jako zprostředkovatele identity ve vašem tenantovi. **Tajný kód klienta** je důležitým bezpečnostním pověřením.

    ![Google + - kopírování hodnot klienta Id a tajný klíč klienta](media/active-directory-b2c-custom-setup-goog-idp/goog-client-secret.png)

## <a name="add-the-google-account-application-key-to-azure-ad-b2c"></a>Přidat do Azure AD B2C aplikace klíč účtu Google +.
Federace se službou Google + účty se vyžaduje tajný kód klienta pro účet Google +. pro vztah důvěryhodnosti Azure AD B2C jménem aplikace. Potřebujete ukládat vaše Google + tajný klíč aplikace v tenantovi Azure AD B2C:  

1.  Přejděte do svého tenanta Azure AD B2C a vyberte **nastavení B2C** > **architekturu rozhraní identit**
2.  Vyberte **klíče zásad** zobrazíte klíče, které jsou k dispozici ve vašem tenantovi.
3.  Klikněte na tlačítko **+ přidat**.
4.  Pro **možnosti**, použijte **ruční**.
5.  Pro **název**, použijte `GoogleSecret`.  
    Předpona, která `B2C_1A_` může být automaticky přidán.
6.  V **tajný kód** zadejte váš tajný klíč aplikace Google z [konzole pro vývojáře Google](https://console.developers.google.com/) , který jste zkopírovali výše.
7.  Pro **použití klíče**, použijte **podpis**.
8.  Klikněte na **Vytvořit**
9.  Potvrďte, že jste vytvořili klíč `B2C_1A_GoogleSecret`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Přidat zprostředkovatele deklarací identity ve svojí zásadě rozšíření

Pokud chcete uživatelům umožní přihlásit pomocí účtu Google +, musíte definovat účet Google + jako poskytovatele deklarací identity. Jinými slovy budete muset zadat koncový bod, který komunikuje se službou Azure AD B2C. Koncový bod poskytuje sadu deklarací identity, které používají Azure AD B2C k ověření, že se ověřil konkrétního uživatele.

Určete účet Google + jako poskytovatele deklarací identity, tak, že přidáte `<ClaimsProvider>` uzlu v souboru rozšíření zásad:

1.  Otevřete soubor rozšíření zásad (TrustFrameworkExtensions.xml) z pracovního adresáře. Pokud potřebujete XML editor [nabídne Visual Studio Code](https://code.visualstudio.com/download), jednoduchý editor napříč platformami.
2.  Najít `<ClaimsProviders>` oddílu
3.  Přidejte následující fragment kódu XML v rámci `ClaimsProviders` prvku a nahraďte `client_id` hodnotu s vaší Google + účet ID klienta aplikace před uložením tohoto souboru.  

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
        <Item Key="scope">email</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="client_id">Your Google+ application ID</Item>
        </Metadata>
        <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
        </CryptographicKeys>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="id" />
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
        <ErrorHandlers>
        <ErrorHandler>
            <ErrorResponseFormat>json</ErrorResponseFormat>
            <ResponseMatch>$[?(@@.error == 'invalid_grant')]</ResponseMatch>
            <Action>Reauthenticate</Action>
            <!--In case of authorization code used error, we don't want the user to select his account again.-->
            <!--AdditionalRequestParameters Key="prompt">select_account</AdditionalRequestParameters-->
        </ErrorHandler>
        </ErrorHandlers>
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="register-the-google-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Registrace Google + účet zprostředkovatele deklarací identity k registraci nebo přihlášení cesty uživatele

Zprostředkovatel identity je nastavený.  To však není k dispozici v některém z obrazovky přihlášení-registrace/přihlášení. Přidat zprostředkovatele identity účtu Google +. pro vaše uživatele `SignUpOrSignIn` cesty uživatele. Chcete-li k dispozici, se nám vytvořit duplikát cesty existující uživatele šablony.  Potom přidáme zprostředkovatele identity účtu služby Google +:

>[!NOTE]
>
>Pokud jste si zkopírovali `<UserJourneys>` element ze základního souboru zásady do souboru rozšíření (TrustFrameworkExtensions.xml), můžete přeskočit k této sekci.

1.  Otevřete soubor základní zásady (například TrustFrameworkBase.xml).
2.  Najít `<UserJourneys>` elementu a zkopírujte celý obsah `<UserJourneys>` uzlu.
3.  Otevřete soubor rozšíření (například TrustFrameworkExtensions.xml) a vyhledejte `<UserJourneys>` elementu. Pokud element neexistuje, přidejte jeden.
4.  Vložte celý obsah `<UserJourney>` uzel, který jste zkopírovali jako podřízený objekt `<UserJourneys>` elementu.

### <a name="display-the-button"></a>Zobrazit tlačítko
`<ClaimsProviderSelections>` Element definuje seznam možnosti výběru zprostředkovatele deklarací identity a jejich pořadí.  `<ClaimsProviderSelection>` Element je obdobou k tlačítku na stránce přihlášení-registrace/přihlášení zprostředkovatele identity. Pokud chcete přidat `<ClaimsProviderSelection>` – element pro účet Google +, nové tlačítko se zobrazí při uživatel umístil na stránce. Chcete-li přidat tento element:

1.  Najít `<UserJourney>` uzel, který zahrnuje `Id="SignUpOrSignIn"` v cestě uživatele, který jste zkopírovali.
2.  Vyhledejte `<OrchestrationStep>` uzel, který obsahuje `Order="1"`
3.  Přidat následující fragment kódu XML v rámci `<ClaimsProviderSelections>` uzlu:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>Tlačítko s odkazem na akci
Teď, když máte tlačítko na místě, budete potřebovat odkázat na akci. Akce v tomto případě je pro Azure AD B2C ke komunikaci s účtem Google +. pro získání tokenu.

1.  Najít `<OrchestrationStep>` , který obsahuje `Order="2"` v `<UserJourney>` uzlu.
2.  Přidat následující fragment kódu XML v rámci `<ClaimsExchanges>` uzlu:

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

>[!NOTE]
>
> * Zkontrolujte, `Id` má stejnou hodnotu jako `TargetClaimsExchangeId` v předchozí části
> * Zajištění `TechnicalProfileReferenceId` ID je nastaven na technický profil vytvoříte starší (služby Google OAUTH).

## <a name="upload-the-policy-to-your-tenant"></a>Odeslání zásady do vašeho tenanta
1.  V [webu Azure portal](https://portal.azure.com), přepněte do [kontextu vašeho tenanta Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)a otevřete **Azure AD B2C** okno.
2.  Vyberte **architekturu rozhraní identit**.
3.  Otevřít **všechny zásady** okno.
4.  Vyberte **nahrát zásady**.
5.  Zkontrolujte **přepsat zásady, pokud existuje** pole.
6.  **Nahrát** TrustFrameworkExtensions.xml a ujistěte se, že se selhání ověření

## <a name="test-the-custom-policy-by-using-run-now"></a>Testování vlastní zásady pomocí možnosti spustit hned
1.  Otevřít **nastavení Azure AD B2C** a přejděte na **architekturu rozhraní identit**.

    >[!NOTE]
    >
    >    **Spustit nyní** vyžaduje aspoň jednu aplikaci do být registrované u klienta. 
    >    Informace o postupu registrace aplikací, najdete v tématu Azure AD B2C [Začínáme](active-directory-b2c-get-started.md) článku nebo [registrace aplikace](active-directory-b2c-app-registration.md) článku.


2.  Otevřít **B2C_1A_signup_signin**, předávající stranu vlastní zásady, které jste nahráli. Vyberte **spustit nyní**.
3.  Byste měli být schopni se přihlásit pomocí účtu Google +.

## <a name="optional-register-the-google-account-claims-provider-to-profile-edit-user-journey"></a>[Volitelné] Registrace Google + účet zprostředkovatele deklarací identity pro cestu uživatele úpravy profilu
Můžete také přidat zprostředkovatele identity účtu Google +. pro vaše uživatele `ProfileEdit` cesty uživatele. Chcete-li k dispozici, jsme zopakujte poslední dva kroky:

### <a name="display-the-button"></a>Zobrazit tlačítko
1.  Otevřete soubor rozšíření zásad (například TrustFrameworkExtensions.xml).
2.  Najít `<UserJourney>` uzel, který zahrnuje `Id="ProfileEdit"` v cestě uživatele, který jste zkopírovali.
3.  Vyhledejte `<OrchestrationStep>` uzel, který obsahuje `Order="1"`
4.  Přidat následující fragment kódu XML v rámci `<ClaimsProviderSelections>` uzlu:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>Tlačítko s odkazem na akci
1.  Najít `<OrchestrationStep>` , který obsahuje `Order="2"` v `<UserJourney>` uzlu.
2.  Přidat následující fragment kódu XML v rámci `<ClaimsExchanges>` uzlu:

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

### <a name="upload-the-policy-to-your-tenant"></a>Odeslání zásady do vašeho tenanta
1.  V [webu Azure portal](https://portal.azure.com), přepněte do [kontextu vašeho tenanta Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)a otevřete **Azure AD B2C** okno.
2.  Vyberte **architekturu rozhraní identit**.
3.  Otevřít **všechny zásady** okno.
4.  Vyberte **nahrát zásady**.
5.  Zkontrolujte, **přepsat zásady, pokud existuje** pole.
6.  **Nahrát** TrustFrameworkExtensions.xml a ujistěte se, že se selhání ověření.

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Pomocí možnosti spustit hned otestovat vlastní zásady úprav profilu

1.  Otevřít **nastavení Azure AD B2C** a přejděte na **architekturu rozhraní identit**.
2.  Otevřít **B2C_1A_ProfileEdit**, předávající stranu vlastní zásady, které jste nahráli. Vyberte **spustit nyní**.
3.  Byste měli být schopni se přihlásit pomocí účtu Google +.

## <a name="download-the-complete-policy-files"></a>Stažení kompletní zásad souborů
Volitelné: Doporučujeme že vytvořit váš scénář pomocí vlastní vlastní zásady, které provedou soubory po dokončení, jak začít s vlastní zásady, namísto použití těchto ukázkových souborů.  [Ukázkové soubory zásad pro odkaz](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-goog-app)
