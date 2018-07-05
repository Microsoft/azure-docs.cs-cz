---
title: Přidat LinkedIn jako zprostředkovatele identity OAuth2 pomocí vlastních zásad v Azure Active Directory B2C | Dokumentace Microsoftu
description: Článek o nastavení Linkedinu aplikaci pomocí protokolu OAuth2 a vlastních zásad.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 334f696d79cf801facf7c5301b2240b69f7134f7
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444374"
---
# <a name="azure-active-directory-b2c-add-linkedin-as-an-identity-provider-by-using-custom-policies"></a>Azure Active Directory B2C: Přidání LinkedIn jako zprostředkovatele identity pomocí vlastních zásad
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku se dozvíte, jak povolit přihlášení pro uživatele s účtem LinkedIn pomocí [vlastní zásady](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Požadavky
Proveďte kroky v [začít pracovat s vlastními zásadami](active-directory-b2c-get-started-custom.md) článku.

## <a name="step-1-create-a-linkedin-account-application"></a>Krok 1: Vytvoření aplikace účet LinkedIn
Použití LinkedIn jako zprostředkovatele identity v Azure Active Directory B2C (Azure AD B2C), musíte vytvořit aplikaci LinkedIn a zadejte správné parametry. Můžete zaregistrovat LinkedIn aplikace tak, že přejdete [stránku pro přihlášení na LinkedIn](https://www.linkedin.com/start/join).

1. Přejděte [LinkedIn správy aplikací](https://www.linkedin.com/secure/developer?newapp=) webu, přihlaste se pomocí přihlašovacích údajů k účtu LinkedIn a pak vyberte **vytvořit aplikaci**.

    ![LinkedIn účtu – vytvoření aplikace](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app1.png)

2. Na **vytvořte novou aplikaci** stránce, postupujte takto:

    a. Typ vaší **název společnosti**, popisný **název** pro společnosti a **popis** nové aplikace.

    b. Nahrajte vaše **Logo aplikace**.

    c. Vyberte **využívání aplikací**.

    d. V **adresu URL webu** vložte **https://login.microsoftonline.com**.

    e. Typ vaší **e-mailová adresa** adresu a **Telefon do zaměstnání** číslo.

    f. V dolní části stránky, přečtěte si a přijměte podmínky použití a pak vyberte **odeslat**.

    ![LinkedIn účtu – konfigurace vlastností aplikace](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app2.png)

3. Vyberte **ověřování**a potom si poznamenejte **ID klienta** a **tajný kód klienta** hodnoty.

4. V **oprávnění adresy URL pro přesměrování** vložte **https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp**. Nahradit {*tenanta*} s názvem vašeho tenanta (například contosob2c.onmicrosoft.com). Ujistěte se, že budou používat schéma HTTPS. 

    ![Účet LinkedIn – sada oprávnění přesměrovat adresy URL](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app3.png)

    >[!NOTE]
    >Tajný kód klienta je důležitým bezpečnostním pověřením. S kýmkoli sdílet tento tajný kód nebo distribuovat s vaší aplikací.

5. Vyberte **Přidat**.

6. Vyberte **nastavení**, změnit **stav aplikace** k **Live**a pak vyberte **aktualizace**.

    ![Účet LinkedIn - nastavit stav aplikace](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app4.png)

## <a name="step-2-add-your-linkedin-application-key-to-azure-ad-b2c"></a>Krok 2: Přidejte klíč LinkedIn aplikace Azure AD B2C
Federace se službou LinkedIn účtů vyžaduje tajný kód klienta pro účet LinkedIn, abyste vztah důvěryhodnosti Azure AD B2C jménem aplikace. Pokud chcete uložit LinkedIn tajný klíč aplikace ve vašem tenantovi Azure AD B2C, postupujte takto:  

1. Ve vašem tenantovi Azure AD B2C vyberte **nastavení B2C** > **architekturu rozhraní identit**.

2. Chcete-li zobrazit klíče, které jsou k dispozici ve vašem tenantovi, vyberte **klíče zásad**.

3. Vyberte **Přidat**.

4. V **možnosti** vyberte **nahrát**.

5. V **název** zadejte **B2cRestClientCertificate**.  
    Předpona, která *B2C_1A_* může být automaticky přidán.

6. V **tajný kód** zadejte váš tajný klíč aplikace LinkedIn z [portál pro registraci aplikací](https://apps.dev.microsoft.com).

7. Pro **použití klíče**vyberte **šifrování**.

8. Vyberte **Vytvořit**. 

9. Potvrďte, že jste vytvořili `B2C_1A_LinkedInSecret`klíč.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Krok 3: Přidání zprostředkovatele deklarací identity ve svojí zásadě rozšíření
Pokud chcete uživatelům umožní přihlásit se pomocí svého účtu LinkedIn, je nutné definovat LinkedIn jako poskytovatele deklarací identity. Jinými slovy je nutné zadat koncové body, které komunikuje se službou Azure AD B2C. Koncové body poskytují sadu deklarací identity, které používají Azure AD B2C k ověření, že se ověřil konkrétního uživatele.

Definujte LinkedIn jako poskytovatele deklarací identity tak, že přidáte `<ClaimsProvider>` uzlu v souboru rozšíření zásad:

1. V pracovním adresáři, otevřete *TrustFrameworkExtensions.xml* soubor rozšíření zásad. 

2. Hledat `<ClaimsProviders>` elementu.

3. V `<ClaimsProviders>` prvku, přidejte následující fragment kódu XML: 

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

4. Nahradit *client_id* hodnotu s vaším ID klienta aplikace LinkedIn.

5. Uložte soubor.

## <a name="step-4-register-the-linkedin-account-claims-provider"></a>Krok 4: Registrace zprostředkovatele deklarací identity účet LinkedIn
Nastavení zprostředkovatele identity. Však není zatím k dispozici v některém z registrace / přihlášení ve windows. Teď musíte přidat zprostředkovatele identity účet LinkedIn pro vaše uživatele `SignUpOrSignIn` cesty uživatele.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Krok 4.1: Vytvořte kopii cesty uživatele
Zpřístupnit cesty uživatele, vytvořte kopii existující šablony cesty uživatele a pak přidejte zprostředkovatele identity LinkedIn:

>[!NOTE]
>Pokud jste si zkopírovali `<UserJourneys>` element ze základního souboru zásadu *TrustFrameworkExtensions.xml* příponu souboru, můžete tuto část přeskočit.

1. Otevřete soubor základní zásady (například TrustFrameworkBase.xml).

2. Hledat `<UserJourneys>` element, vyberte veškerý obsah `<UserJourney>` uzlu a pak vyberte **Vyjmout** přesunutí vybraného textu do schránky.

3. Otevřete soubor rozšíření (například TrustFrameworkExtensions.xml) a vyhledejte `<UserJourneys>` elementu. Pokud element neexistuje, přidejte ji.

4. Vložte celý obsah `<UserJourney>` uzlu, na kterém jste přesunuli do schránky. v kroku 2, do `<UserJourneys>` elementu.

### <a name="step-42-display-the-button"></a>Krok 4.2: Zobrazit "tlačítko"
`<ClaimsProviderSelections>` Element definuje seznam možnosti výběru zprostředkovatele deklarací identity a jejich pořadí. `<ClaimsProviderSelection>` Uzel je obdobou k tlačítku na stránce registrace nebo přihlášení zprostředkovatele identity. Pokud chcete přidat `<ClaimsProviderSelection>` uzel pro účet LinkedIn, nové tlačítko se zobrazí, když uživatel umístil na stránce. Chcete-li přidat tento element, postupujte takto:

1. Hledat `<UserJourney>` uzel, který obsahuje `Id="SignUpOrSignIn"` v cestě uživatele, který jste zkopírovali.

2. Vyhledejte `<OrchestrationStep>` uzel, který zahrnuje `Order="1"`.

3. V `<ClaimsProviderSelections>` prvku, přidejte následující fragment kódu XML:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>Krok 4.3: Odkaz na tlačítko na akci
Teď, když máte tlačítko na místě, je třeba propojit na akci. Akce v tomto případě je pro Azure AD B2C ke komunikaci s účtem LinkedIn k získání tokenu. Tlačítko odkazu na akci propojením technický profil pro vašeho poskytovatele deklarací identity účet LinkedIn:

1. Hledat `<OrchestrationStep>` uzel, který obsahuje `Order="2"` v `<UserJourney>` uzlu.

2. V `<ClaimsExchanges>` prvku, přidejte následující fragment kódu XML:

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth" />
    ```

    >[!NOTE]
    >* Ujistěte se, že `Id` má stejnou hodnotu jako `TargetClaimsExchangeId` v předchozí části.
    >* Ujistěte se, `TechnicalProfileReferenceId` ID je nastaven na technický profil, který jste vytvořili starší (-OAuth pro LinkedIn).

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Krok 5: Nahrajte zásady pro vašeho tenanta
1. V [webu Azure portal](https://portal.azure.com), přepněte [kontextu vašeho tenanta Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)a pak vyberte **Azure AD B2C**.

2. Vyberte **architekturu rozhraní identit**.

3. Vyberte **všechny zásady**.

4. Vyberte **nahrát zásady**.

5. Vyberte **přepsat zásady, pokud existuje** zaškrtávací políčko.

6. Nahrát *TrustFrameworkBase.xml* a *TrustFrameworkExtensions.xml* soubory a ujistěte se, že bude úspěšné ověření.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Krok 6: Testování spustit pomocí vlastních zásad
1. Vyberte **nastavení Azure AD B2C**a pak vyberte **architekturu rozhraní identit**.

    >[!NOTE]
    >Spustit nyní vyžaduje aspoň jednu aplikaci do být registrované u klienta. Informace o postupu registrace aplikací, najdete v tématu Azure AD B2C [Začínáme](active-directory-b2c-get-started.md) článku nebo [registrace aplikace](active-directory-b2c-app-registration.md) článku.

2. Otevřít **B2C_1A_signup_signin**, předávající stranu vlastní zásady, které jste nahráli a pak vyberte **spustit nyní**.  
    Teď by měl být schopni se přihlásit pod účtem LinkedIn.

## <a name="step-7-optional-register-the-linkedin-account-claims-provider-to-the-profile-edit-user-journey"></a>Krok 7: (Volitelné) zaregistrujte účet LinkedIn deklarací poskytovatele pro cestu uživatele úpravy profilu
Můžete také přidat zprostředkovatele identity účet LinkedIn k vaší `ProfileEdit` cesty uživatele. Aby se uživatel přepravě k dispozici, opakujte "krok 4." Tentokrát vyberte `<UserJourney>` uzel, který obsahuje `Id="ProfileEdit"`. Uložit nahrát a testování vaší zásady.

## <a name="optional-download-the-complete-policy-files"></a>(Volitelné) Stažení kompletní zásad souborů
Po dokončení [začít pracovat s vlastními zásadami](active-directory-b2c-get-started-custom.md) návodu, doporučujeme vám vytvořit váš scénář s využitím vlastních zásad pro soubory. Pro srovnání si uvádíme [ukázkové soubory zásad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-li-app).
