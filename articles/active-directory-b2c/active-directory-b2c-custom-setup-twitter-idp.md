---
title: 'Azure Active Directory B2C: Přidání služby Twitter jako zprostředkovatel identity OAuth1 pomocí vlastních zásad'
description: Pomocí služby Twitter jako poskytovatel identit pomocí protokolu OAuth1
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 10/23/2017
ms.author: davidmu
ms.openlocfilehash: 4a7cc552f49877874dc173a47efe22873d5aeeed
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-add-twitter-as-an-oauth1-identity-provider-by-using-custom-policies"></a>Azure Active Directory B2C: Přidání služby Twitter jako zprostředkovatel identity OAuth1 pomocí vlastních zásad
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek ukazuje, jak povolit přihlášení pro uživatele služby Twitter účtu pomocí [vlastní zásady](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Požadavky
Proveďte kroky v [začít pracovat s vlastními zásadami](active-directory-b2c-get-started-custom.md) článku.

## <a name="step-1-create-a-twitter-account-application"></a>Krok 1: Vytvoření aplikace účet služby Twitter.
Použití služby Twitter jako poskytovatel identit v Azure Active Directory B2C (Azure AD B2C), musíte vytvořit aplikaci služby Twitter a zadat se správné parametry. Můžete zaregistrovat aplikaci služby Twitter přechodem na [stránku pro přihlášení služby Twitter](https://twitter.com/signup).

1. Přejděte na [Twitter vývojáři](https://apps.twitter.com/) web, přihlaste se pomocí přihlašovacích údajů účtu služby Twitter a pak vyberte **vytvořit novou aplikaci**.

    ![Účet služby Twitter – vytvoření nové aplikace](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app1.png)

2. V **vytvořit aplikaci** okno, postupujte takto:
 
    a. Typ **název** a **popis** pro novou aplikaci. 

    b. V **webu** pole, vložte **https://login.microsoftonline.com**. 

    c. V **adresu URL zpětné volání** pole, vložte **https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp**. Nahraďte {*klienta*} s názvem vašeho klienta (například contosob2c.onmicrosoft.com). Ujistěte se, že používáte schéma HTTPS. 

    d. V dolní části stránky, přečtěte si a přijměte podmínky a potom vyberte **vytvořit aplikaci služby Twitter**.

    ![Účet služby Twitter – přidání nové aplikace](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app2.png)

3. V **B2C ukázku** vyberte **nastavení**, vyberte **povolit této aplikace, který se má použít k přihlášení pomocí služby Twitter** zaškrtněte políčko a potom vyberte **aktualizace Nastavení**.

4. Vyberte **klíče a přístupové tokeny**a poznamenejte si **uživatelský klíč (klíč rozhraní API)** a **uživatelský tajný klíč (tajný klíč rozhraní API)** hodnoty.

    ![Účet služby Twitter – nastavení vlastností aplikací](media/active-directory-b2c-custom-setup-twitter-idp/adb2c-ief-setup-twitter-idp-new-app3.png)

    >[!NOTE]
    >Uživatelský tajný klíč je důležitým bezpečnostním pověřením. S kýmkoli sdílet tento tajný klíč nebo distribuovat s vaší aplikací.

## <a name="step-2-add-your-twitter-account-application-key-to-azure-ad-b2c"></a>Krok 2: Přidáte váš klíč pro Twitter účet aplikace do Azure AD B2C
Federace s účty služby Twitter vyžaduje uživatelský tajný klíč pro účet služby Twitter do vztahu důvěryhodnosti Azure AD B2C jménem aplikace. K uložení uživatelský tajný klíč pro Twitter aplikace v klientovi služby Azure AD B2C, postupujte takto: 

1. Ve vašem klientu Azure AD B2C, vyberte **nastavení B2C** > **Identity rozhraní Framework**.

2. Chcete-li zobrazit klíčů, které jsou k dispozici ve vašem klientovi, vyberte **zásad klíče**.

3. Vyberte **Přidat**.

4. V **možnosti** vyberte **ruční**.

5. V **název** vyberte **TwitterSecret**.  
    Předpona *B2C_1A_* může automaticky přidat.

6. V **tajný klíč** zadejte váš tajný klíč aplikace Microsoft z [portálu pro registraci aplikace](https://apps.dev.microsoft.com).

7. Pro **použití klíče**, použijte **šifrování**.

8. Vyberte **Vytvořit**.

9. Potvrďte, že jste vytvořili `B2C_1A_TwitterSecret` klíč.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Krok 3: Přidání poskytovatele deklarací identity v rozšíření zásady

Pokud chcete uživatelům přihlášení pomocí účtu sítě Twitter, je nutné zadat Twitter jako poskytovatele deklarací identity. Jinými slovy je nutné zadat koncové body, které komunikuje se službou Azure AD B2C. Koncové body poskytují sadu deklarací identity, které používají Azure AD B2C k ověření, že byl ověřen konkrétního uživatele.

Definování Twitter jako poskytovatele deklarací identity tak, že přidáte `<ClaimsProvider>` uzlu v souboru rozšíření zásad:

1. V pracovním adresáři, otevřete *TrustFrameworkExtensions.xml* soubor rozšíření zásad. 

2. Vyhledejte `<ClaimsProviders>` části.

3. V `<ClaimsProviders>` uzlu, přidejte následující fragment kódu XML:  

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

4. Nahraďte *client_id*' hodnotu s vaší aplikací uživatelský klíč pro Twitter účtu.

5. Uložte soubor.

## <a name="step-4-register-the-twitter-account-claims-provider-to-your-sign-up-or-sign-in-user-journey"></a>Krok 4: Registrace zprostředkovatele deklarací identity účtu služby Twitter k vám dobře slouží registrace nebo přihlášení uživatele
Nastavili jste si poskytovatele identit. Ale ho dosud nejsou k dispozici v žádném z registrace nebo přihlášení systému windows. Nyní je nutné přidat zprostředkovatele identity účtu služby Twitter pro vaše uživatele `SignUpOrSignIn` cesty uživatele.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Krok 4.1: Vytvoření kopie cesty uživatele
Aby cesty uživatele dostupné, vytvořte duplicitní existující šablony cesty uživatele a pak přidejte zprostředkovatele identity Twitter:

>[!NOTE]
>Pokud jste zkopírovali `<UserJourneys>` element ze základního souboru v zásadách *TrustFrameworkExtensions.xml* souboru rozšíření, můžete přeskočit k další části.

1. Otevřete soubor základní zásad (například TrustFrameworkBase.xml).

2. Vyhledejte `<UserJourneys>` element, vyberte celý obsah `<UserJourney>` uzel a potom vyberte **Vyjmout** přesunout vybraný text do schránky.

3. Otevřete soubor rozšíření (například TrustFrameworkExtensions.xml) a poté vyhledejte `<UserJourneys>` elementu. Pokud element neexistuje, přidejte ji.

4. Vložte celý obsah `<UserJourney>` uzlu, který jste přesunuli do schránky v kroku 2, do `<UserJourneys>` elementu.

### <a name="step-42-display-the-button"></a>Krok 4.2: Zobrazit "button"
`<ClaimsProviderSelections>` Element definuje seznam možnosti výběru poskytovatele deklarací identity a jejich pořadí. `<ClaimsProviderSelection>` Uzel je obdobou tlačítko zprostředkovatele identity na stránce registrace nebo přihlášení. Pokud přidáte `<ClaimsProviderSelection>` uzel pro účet služby Twitter, nové tlačítko se zobrazí, když uživatel pojmenováváme na stránce. Pokud chcete přidat tento element, postupujte takto:

1. Vyhledejte `<UserJourney>` uzlu, který obsahuje `Id="SignUpOrSignIn"` v cesty uživatele, který jste zkopírovali.

2. Vyhledejte `<OrchestrationStep>` uzlu, který obsahuje `Order="1"`.

3. V `<ClaimsProviderSelections>` elementu, přidejte následující fragment kódu XML:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>Krok 4.3: Odkaz na tlačítko akce
Nyní když máte tlačítka na místě, musíte ho propojit akce. Akce, v takovém případě je pro Azure AD B2C ke komunikaci s účtem služby Twitter přijmout token. Pomocí propojení technické profil pro poskytovatele deklarací identity účtu služby Twitter odkazu na tlačítko akce:

1. Vyhledejte `<OrchestrationStep>` uzlu, který obsahuje `Order="2"` v `<UserJourney>` uzlu.
2. V `<ClaimsExchanges>` elementu, přidejte následující fragment kódu XML:

    ```xml
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
    ```

    >[!NOTE]
    >* Ujistěte se, že `Id` mají stejnou hodnotu jako `TargetClaimsExchangeId` v předchozí části.
    >* Ujistěte se, že `TechnicalProfileReferenceId` ID nastavena na technické profil, který jste vytvořili starší (Twitter OAUTH1).

## <a name="step-5-upload-the-policy-to-your-tenant"></a>Krok 5: Nahrajte zásady klienta
1. V [portál Azure](https://portal.azure.com), přepnout [kontextu klienta služby Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)a potom vyberte **Azure AD B2C**.

2. Vyberte **Identity rozhraní Framework**.

3. Vyberte **všechny zásady**.

4. Vyberte **nahrát zásady**.

5. Vyberte **přepsat zásady, pokud existuje** zaškrtávací políčko.

6. Nahrát *TrustFrameworkBase.xml* a *TrustFrameworkExtensions.xml* soubory a ujistěte se, že jejich úspěšné ověření.

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>Krok 6: Testování spustit pomocí vlastních zásad

1. Vyberte **nastavení Azure AD B2C**a potom vyberte **Identity rozhraní Framework**.

    >[!NOTE]
    >Spustit nyní vyžaduje alespoň jedné aplikace do být preregistered u klienta. Další postup registrace aplikace najdete v tématu Azure AD B2C [Začínáme](active-directory-b2c-get-started.md) článek nebo [registrace aplikace](active-directory-b2c-app-registration.md) článku.

2. Otevřete **B2C_1A_signup_signin**, předávající stranu vlastních zásad, které jste nahráli a potom vyberte **spustit nyní**.  
    Teď by měla být neúspěšné přihlášení pomocí účtu sítě Twitter.

## <a name="step-7-optional-register-the-twitter-account-claims-provider-to-the-profile-edit-user-journey"></a>Krok 7: Zprostředkovatele pro úpravy profilu uživatele cesty deklarace identity (volitelné) zaregistrovat účet služby Twitter.
Můžete také přidat zprostředkovatele identity účtu služby Twitter na váš `ProfileEdit` cesty uživatele. Aby se uživatel přepravě k dispozici, opakování "krok 4." Tentokrát vyberte `<UserJourney>` uzlu, který obsahuje `Id="ProfileEdit"`. Uložit, odeslání a testování vaší zásady.


## <a name="optional-download-the-complete-policy-files"></a>(Volitelné) Stáhnout soubory dokončení zásad
Po dokončení [začít pracovat s vlastními zásadami](active-directory-b2c-get-started-custom.md) návod, doporučujeme vám vytvořit váš scénář pomocí vlastních zásad pro soubory. Pro vaši informaci uvádíme [ukázkové soubory zásad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-twitter-app).
