---
title: Přidat Twitteru jako zprostředkovatele identity OAuth1 pomocí vlastních zásad v Azure Active Directory B2C | Dokumentace Microsoftu
description: Pomocí protokolu OAuth1 pomocí Twitteru jako zprostředkovatele identity.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 786f0dfd0cf3cf2e9ab0d16e26811fabd6bfc17c
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440942"
---
# <a name="azure-active-directory-b2c-add-twitter-as-an-oauth1-identity-provider-by-using-custom-policies"></a>Azure Active Directory B2C: Přidání Twitteru jako zprostředkovatele identity OAuth1 pomocí vlastních zásad
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek ukazuje, jak povolit přihlášení pro uživatele z účtu na Twitteru s použitím [vlastní zásady](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Požadavky
Proveďte kroky v [začít pracovat s vlastními zásadami](active-directory-b2c-get-started-custom.md) článku.

## <a name="step-1-create-a-twitter-account-application"></a>Krok 1: Vytvoření aplikace Twitter účtu
Chcete-li použít Twitteru jako zprostředkovatele identity v Azure Active Directory B2C (Azure AD B2C), musíte vytvořit aplikaci Twitter a zadejte správné parametry. Můžete zaregistrovat aplikaci Twitter tak, že přejdete [registrační stránku Twitteru](https://twitter.com/signup).

1. Přejděte [Twitteru vývojáři](https://apps.twitter.com/) webu, přihlaste se pomocí přihlašovacích údajů k účtu Twitteru a pak vyberte **vytvořit novou aplikaci**.

    ![Twitterovém účtu – vytvoření nové aplikace](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app1.png)

2. V **vytvořit aplikaci** okno, postupujte takto:
 
    a. Typ **název** a **popis** pro novou aplikaci. 

    b. V **webu** vložte **https://login.microsoftonline.com**. 

    c. 4. Pro **adresu URL zpětného volání**, zadejte `https://login.microsoftonline.com/te/{tenant}/{policyId}/oauth1/authresp`. Nezapomeňte nahradit **{klient}** s názvem vašeho tenanta (například contosob2c.onmicrosoft.com) a **{policyId}** se svým id zásad (například b2c_1_policy).  **Zpětné volání adresy URL musí být v všechna písmena malá.** Měli byste přidat adresu URL zpětného volání pro všechny zásady, které používají přihlášení Twitter. Ujistěte se, že používáte `b2clogin.com` místo ` login.microsoftonline.com` Pokud používáte ho ve vaší aplikaci.

    d. V dolní části stránky, přečtěte si a přijměte podmínky a pak vyberte **vytvoření aplikace Twitter**.

    ![Twitterovém účtu – přidání nové aplikace](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app2.png)

3. V **B2C ukázka** okně **nastavení**, vyberte **povolit této aplikace se použije k přihlášení pomocí Twitteru** zaškrtněte políčko a potom vyberte **aktualizace Nastavení**.

4. Vyberte **klíče a přístupové tokeny**a Všimněte si, **uživatelský klíč (klíč rozhraní API)** a **uživatelský tajný klíč (tajný klíč rozhraní API)** hodnoty.

    ![Účet na twitteru – nastavení vlastností aplikace](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app3.png)

    >[!NOTE]
    >Uživatelský tajný klíč je důležitým bezpečnostním pověřením. S kýmkoli sdílet tento tajný kód nebo distribuovat s vaší aplikací.

## <a name="step-2-add-your-twitter-account-application-key-to-azure-ad-b2c"></a>Krok 2: Přidání aplikací klíč vašeho účtu Twitter do Azure AD B2C
Federace se službou Twitter účtů vyžaduje uživatelský tajný klíč pro účet Twitteru do vztahu důvěryhodnosti Azure AD B2C jménem aplikace. Pokud chcete uložit uživatelský tajný klíč pro Twitter application ve vašem tenantovi Azure AD B2C, postupujte takto: 

1. Ve vašem tenantovi Azure AD B2C vyberte **nastavení B2C** > **architekturu rozhraní identit**.

2. Chcete-li zobrazit klíče, které jsou k dispozici ve vašem tenantovi, vyberte **klíče zásad**.

3. Vyberte **Přidat**.

4. V **možnosti** vyberte **ruční**.

5. V **název** vyberte **TwitterSecret**.  
    Předpona, která *B2C_1A_* může být automaticky přidán.

6. V **tajný kód** zadejte váš tajný klíč aplikace Microsoft z [portál pro registraci aplikací](https://apps.dev.microsoft.com).

7. Pro **použití klíče**, použijte **šifrování**.

8. Vyberte **Vytvořit**.

9. Potvrďte, že jste vytvořili `B2C_1A_TwitterSecret` klíč.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Krok 3: Přidání zprostředkovatele deklarací identity ve svojí zásadě rozšíření

Pokud chcete uživatelům umožní přihlásit pomocí účtu sítě Twitter, je nutné definovat Twitteru jako zprostředkovatele deklarací identity. Jinými slovy je nutné zadat koncové body, které komunikuje se službou Azure AD B2C. Koncové body poskytují sadu deklarací identity, které používají Azure AD B2C k ověření, že se ověřil konkrétního uživatele.

Definujte Twitteru jako zprostředkovatele deklarací tak, že přidáte `<ClaimsProvider>` uzlu v souboru rozšíření zásad:

1. V pracovním adresáři, otevřete *TrustFrameworkExtensions.xml* soubor rozšíření zásad. 

2. Hledat `<ClaimsProviders>` oddílu.

3. V `<ClaimsProviders>` uzel, přidejte následující fragment kódu XML:  

    ```xml
    <ClaimsProvider>
        <Domain>twitter.com</Domain>
        <DisplayName>Twitter</DisplayName>
        <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAUTH1">
            <DisplayName>Twitter</DisplayName>
            <Protocol Name="OAuth1" />
            <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application consumer key</Item>
            </Metadata>
            <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
            </CryptographicKeys>
            <InputClaims />
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
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

4. Nahradit *client_id*"hodnotu s vaší aplikací uživatelský klíč pro Twitter účtu.

5. Uložte soubor.

## <a name="step-4-register-the-twitter-account-claims-provider-to-your-sign-up-or-sign-in-user-journey"></a>Krok 4: Zaregistrujte poskytovatele deklarací identity účtu Twitter na vaší cestě registrace nebo přihlašování uživatelů
Nastavení zprostředkovatele identity. Však není zatím k dispozici v některém z registrace / přihlášení ve windows. Teď musíte přidat zprostředkovatele identity účtu Twitter pro vaše uživatele `SignUpOrSignIn` cesty uživatele.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Krok 4.1: Vytvořte kopii cesty uživatele
Chcete-li k dispozici cesty uživatele, vytvořit kopii existující šablony cesty uživatele a pak přidejte zprostředkovatele identity Twitteru:

>[!NOTE]
>Pokud jste si zkopírovali `<UserJourneys>` element ze základního souboru zásadu *TrustFrameworkExtensions.xml* příponu souboru, můžete přeskočit k další části.

1. Otevřete soubor základní zásady (například TrustFrameworkBase.xml).

2. Hledat `<UserJourneys>` element, vyberte veškerý obsah `<UserJourney>` uzlu a pak vyberte **Vyjmout** přesunutí vybraného textu do schránky.

3. Otevřete soubor rozšíření (například TrustFrameworkExtensions.xml) a pak vyhledejte oddíl týkající `<UserJourneys>` elementu. Pokud element neexistuje, přidejte ji.

4. Vložte celý obsah `<UserJourney>` uzlu, na kterém jste přesunuli do schránky. v kroku 2, do `<UserJourneys>` elementu.

### <a name="step-42-display-the-button"></a>Krok 4.2: Zobrazit "tlačítko"
`<ClaimsProviderSelections>` Element definuje seznam možnosti výběru zprostředkovatele deklarací identity a jejich pořadí. `<ClaimsProviderSelection>` Uzel je obdobou k tlačítku na stránce registrace nebo přihlášení zprostředkovatele identity. Pokud chcete přidat `<ClaimsProviderSelection>` uzel účtu sítě Twitter, nové tlačítko se zobrazí, když uživatel umístil na stránce. Chcete-li přidat tento element, postupujte takto:

1. Hledat `<UserJourney>` uzel, který obsahuje `Id="SignUpOrSignIn"` v cestě uživatele, který jste zkopírovali.

2. Vyhledejte `<OrchestrationStep>` uzel, který obsahuje `Order="1"`.

3. V `<ClaimsProviderSelections>` prvku, přidejte následující fragment kódu XML:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>Krok 4.3: Odkaz na tlačítko na akci
Teď, když máte tlačítko na místě, je třeba propojit na akci. Akce v tomto případě je pro Azure AD B2C ke komunikaci s účtem Twitteru získat token. Tlačítko odkazu na akci propojením technický profil pro poskytovatele deklarací identity účtu Twitter:

1. Hledat `<OrchestrationStep>` uzel, který obsahuje `Order="2"` v `<UserJourney>` uzlu.
2. V `<ClaimsExchanges>` prvku, přidejte následující fragment kódu XML:

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    >[!NOTE]
    >* Ujistěte se, že `Id` má stejnou hodnotu jako `TargetClaimsExchangeId` v předchozí části.
    >* Ujistěte se, `TechnicalProfileReferenceId` ID je nastaven na technický profil, který jste vytvořili starší (Twitter OAUTH1).

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
    Teď by měl být schopni se přihlásit pomocí účtu sítě Twitter.

## <a name="step-7-optional-register-the-twitter-account-claims-provider-to-the-profile-edit-user-journey"></a>Krok 7: Registrace (volitelně) účtu sítě Twitter deklarací poskytovatele pro cestu uživatele úpravy profilu
Můžete také přidat zprostředkovatele identity účtu Twitter na váš `ProfileEdit` cesty uživatele. Aby se uživatel přepravě k dispozici, opakujte "krok 4." Tentokrát vyberte `<UserJourney>` uzel, který obsahuje `Id="ProfileEdit"`. Uložit nahrát a testování vaší zásady.


## <a name="optional-download-the-complete-policy-files"></a>(Volitelné) Stažení kompletní zásad souborů
Po dokončení [začít pracovat s vlastními zásadami](active-directory-b2c-get-started-custom.md) návodu, doporučujeme vám vytvořit váš scénář s využitím vlastních zásad pro soubory. Pro srovnání si uvádíme [ukázkové soubory zásad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-twitter-app).
