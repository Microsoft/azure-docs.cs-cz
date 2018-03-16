---
title: "Azure Active Directory B2C: Přidejte LinkedIn jako zprostředkovatel identity OAuth2 pomocí vlastních zásad"
description: "Postupy: článek o nastavení aplikace LinkedIn pomocí OAuth2 protokol a vlastní zásady"
services: active-directory-b2c
documentationcenter: 
author: yoelhor
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 10/23/2017
ms.author: yoelh
ms.openlocfilehash: 77e2b9b283e4051370ffb905681135c27512834e
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2018
---
# <a name="azure-active-directory-b2c-add-linkedin-as-an-identity-provider-by-using-custom-policies"></a>Azure Active Directory B2C: Přidejte LinkedIn jako zprostředkovatel identity pomocí vlastních zásad
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek ukazuje, jak povolit přihlášení pro uživatele účtu LinkedIn pomocí [vlastní zásady](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Požadavky
Proveďte kroky v [začít pracovat s vlastními zásadami](active-directory-b2c-get-started-custom.md) článku.

## <a name="step-1-create-a-linkedin-account-application"></a>Krok 1: Vytvoření aplikace účet LinkedIn
Použít LinkedIn jako poskytovatel identit v Azure Active Directory B2C (Azure AD B2C), musíte vytvořit aplikaci LinkedIn a zadat se správné parametry. Můžete zaregistrovat aplikaci LinkedIn přechodem na [stránku pro přihlášení LinkedIn](https://LinkedIn.com/signup).

1. Přejděte na [Správa aplikací LinkedIn](https://www.linkedin.com/secure/developer?newapp=) web, přihlaste se pomocí přihlašovacích údajů účtu LinkedIn a pak vyberte **vytvořit aplikaci**.

    ![LinkedIn účet – vytvoření aplikace](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app1.png)

2. Na **vytvořte novou aplikaci** proveďte následující:

    a. Typ vaše **název společnosti**, popisný **název** pro společnosti a **popis** nové aplikace.

    b. Nahrát váš **Logo aplikace**.

    c. Vyberte **využívání aplikací**.

    d. V **adresu URL webu** pole, vložte  **https://login.microsoftonline.com** .

    e. Typ vaše **e-mailová adresa** adresu a **Telefon do zaměstnání** číslo.

    f. V dolní části stránky, přečtěte si a přijměte podmínky použití a pak vyberte **odeslání**.

    ![LinkedIn účet - konfigurovat vlastnosti aplikace](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app2.png)

3. Vyberte **ověřování**a poznamenejte si **ID klienta** a **tajný klíč klienta** hodnoty.

4. V **oprávnění adres URL pro přesměrování** pole, vložte  **https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/oauth2/authresp** . Nahraďte {*klienta*} s názvem vašeho klienta (například contosob2c.onmicrosoft.com). Ujistěte se, že používáte schéma HTTPS. 

    ![Účet LinkedIn – sadu oprávnění přesměrování adresy URL](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app3.png)

    >[!NOTE]
    >Tajný klíč klienta je důležitým bezpečnostním pověřením. S kýmkoli sdílet tento tajný klíč nebo distribuovat s vaší aplikací.

5. Vyberte **Přidat**.

6. Vyberte **nastavení**, změnit **stav aplikace** k **živé**a potom vyberte **aktualizace**.

    ![Účet LinkedIn – nastavit stav aplikace](media/active-directory-b2c-custom-setup-li-idp/adb2c-ief-setup-li-idp-new-app4.png)

## <a name="step-2-add-your-linkedin-application-key-to-azure-ad-b2c"></a>Krok 2: Přidejte svůj klíč LinkedIn aplikace do Azure AD B2C
Federace s účty LinkedIn vyžaduje tajný klíč klienta pro účet LinkedIn do vztahu důvěryhodnosti Azure AD B2C jménem aplikace. K uložení tajný klíč aplikace LinkedIn v klientovi služby Azure AD B2C, postupujte takto:  

1. Ve vašem klientu Azure AD B2C, vyberte **nastavení B2C** > **Identity rozhraní Framework**.

2. Chcete-li zobrazit klíčů, které jsou k dispozici ve vašem klientovi, vyberte **zásad klíče**.

3. Vyberte **Přidat**.

4. V **možnosti** vyberte **nahrát**.

5. V **název** zadejte **B2cRestClientCertificate**.  
    Předpona *B2C_1A_* může automaticky přidat.

6. V **tajný klíč** zadejte váš tajný klíč aplikace LinkedIn z [portálu pro registraci aplikace](https://apps.dev.microsoft.com).

7. Pro **použití klíče**, vyberte **šifrování**.

8. Vyberte **Vytvořit**. 

9. Potvrďte, že jste vytvořili `B2C_1A_LinkedInSecret`klíč.

## <a name="step-3-add-a-claims-provider-in-your-extension-policy"></a>Krok 3: Přidání poskytovatele deklarací identity v rozšíření zásady
Pokud chcete uživatelům přihlásit pomocí svého účtu LinkedIn, je nutné zadat LinkedIn jako poskytovatele deklarací identity. Jinými slovy je nutné zadat koncové body, které komunikuje se službou Azure AD B2C. Koncové body poskytují sadu deklarací identity, které používají Azure AD B2C k ověření, že byl ověřen konkrétního uživatele.

Definování LinkedIn jako poskytovatele deklarací identity tak, že přidáte `<ClaimsProvider>` uzlu v souboru rozšíření zásad:

1. V pracovním adresáři, otevřete *TrustFrameworkExtensions.xml* soubor rozšíření zásad. 

2. Vyhledejte `<ClaimsProviders>` elementu.

3. V `<ClaimsProviders>` elementu, přidejte následující fragment kódu XML: 

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

4. Nahraďte *client_id* hodnotu s vaším ID klienta aplikace LinkedIn.

5. Uložte soubor.

## <a name="step-4-register-the-linkedin-account-claims-provider"></a>Krok 4: Registrace zprostředkovatele deklarací identity účtu LinkedIn
Nastavili jste si poskytovatele identit. Ale ho dosud nejsou k dispozici v žádném z registrace nebo přihlášení systému windows. Nyní je nutné přidat zprostředkovatele identity účtu LinkedIn pro vaše uživatele `SignUpOrSignIn` cesty uživatele.

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Krok 4.1: Vytvoření kopie cesty uživatele
Chcete-li k dispozici cesty uživatele, vytvořte duplicitní existující šablony cesty uživatele a pak přidejte zprostředkovatele identity LinkedIn:

>[!NOTE]
>Pokud jste zkopírovali `<UserJourneys>` element ze základního souboru v zásadách *TrustFrameworkExtensions.xml* souboru rozšíření, můžete tuto část přeskočit.

1. Otevřete soubor základní zásad (například TrustFrameworkBase.xml).

2. Vyhledejte `<UserJourneys>` element, vyberte celý obsah `<UserJourney>` uzel a potom vyberte **Vyjmout** přesunout vybraný text do schránky.

3. Otevřete soubor rozšíření (například TrustFrameworkExtensions.xml) a vyhledejte `<UserJourneys>` elementu. Pokud element neexistuje, přidejte ji.

4. Vložte celý obsah `<UserJourney>` uzlu, který jste přesunuli do schránky v kroku 2, do `<UserJourneys>` elementu.

### <a name="step-42-display-the-button"></a>Krok 4.2: Zobrazit "button"
`<ClaimsProviderSelections>` Element definuje seznam možnosti výběru poskytovatele deklarací identity a jejich pořadí. `<ClaimsProviderSelection>` Uzel je obdobou tlačítko zprostředkovatele identity na stránce registrace nebo přihlášení. Pokud přidáte `<ClaimsProviderSelection>` uzel pro účet LinkedIn nové tlačítko se zobrazí, když uživatel pojmenováváme na stránce. Pokud chcete přidat tento element, postupujte takto:

1. Vyhledejte `<UserJourney>` uzlu, který obsahuje `Id="SignUpOrSignIn"` v cesty uživatele, který jste zkopírovali.

2. Vyhledejte `<OrchestrationStep>` uzlu, který zahrnuje `Order="1"`.

3. V `<ClaimsProviderSelections>` elementu, přidejte následující fragment kódu XML:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="step-43-link-the-button-to-an-action"></a>Krok 4.3: Odkaz na tlačítko akce
Nyní když máte tlačítka na místě, musíte ho propojit akce. Akce, v takovém případě je pro Azure AD B2C ke komunikaci s účtem LinkedIn přijmout token. Pomocí propojení technické profil pro poskytovatele deklarací identity účtu LinkedIn odkazu na tlačítko akce:

1. Vyhledejte `<OrchestrationStep>` uzlu, který obsahuje `Order="2"` v `<UserJourney>` uzlu.

2. V `<ClaimsExchanges>` elementu, přidejte následující fragment kódu XML:

    ```xml
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth" />
    ```

    >[!NOTE]
    >* Ujistěte se, že `Id` mají stejnou hodnotu jako `TargetClaimsExchangeId` v předchozí části.
    >* Ujistěte se, že `TechnicalProfileReferenceId` ID je nastaven na technické profil vytvořený starší (-OAuth pro LinkedIn).

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
    Nyní byste měli být moct přihlásit pomocí účtu LinkedIn.

## <a name="step-7-optional-register-the-linkedin-account-claims-provider-to-the-profile-edit-user-journey"></a>Krok 7: (Volitelné) zaregistrovat účet LinkedIn deklarací zprostředkovatele, který se cesty úpravy profilu uživatele
Můžete také přidat zprostředkovatele identity LinkedIn účet, který má vaše `ProfileEdit` cesty uživatele. Aby se uživatel přepravě k dispozici, opakování "krok 4." Tentokrát vyberte `<UserJourney>` uzlu, který obsahuje `Id="ProfileEdit"`. Uložit, odeslání a testování vaší zásady.

## <a name="optional-download-the-complete-policy-files"></a>(Volitelné) Stáhnout soubory dokončení zásad
Po dokončení [začít pracovat s vlastními zásadami](active-directory-b2c-get-started-custom.md) návod, doporučujeme vám vytvořit váš scénář pomocí vlastních zásad pro soubory. Pro vaši informaci uvádíme [ukázkové soubory zásad](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-li-app).
