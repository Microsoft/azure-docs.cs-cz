---
title: Přidat účet Microsoft (MSA) jako zprostředkovatele identity pomocí vlastních zásad v Azure Active Directory B2C | Dokumentace Microsoftu
description: Ukázka použití Microsoft jako zprostředkovatele identity pomocí protokolu OpenID Connect (OIDC).
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6a981f112c97ee35b476c92f6f698e68a12a1363
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43336817"
---
# <a name="azure-active-directory-b2c-add-microsoft-account-msa-as-an-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: Přidáte účet Microsoft (MSA) jako zprostředkovatele identity pomocí vlastních zásad

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku se dozvíte, jak povolit přihlášení pro uživatele z účtu Microsoft (MSA) prostřednictvím [vlastní zásady](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Požadavky
Proveďte kroky v [Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md) článku.

Tyto kroky zahrnují:

1.  Vytvoření aplikace účtu Microsoft.
2.  Přidání klíče aplikace účtu Microsoft Azure AD B2C
3.  Přidání poskytovatele deklarací identity do zásad
4.  Registrace zprostředkovatele deklarací identity Account Microsoft pro cestu uživatele
5.  Odeslání zásady do Azure AD B2C tenanta a testování

## <a name="create-a-microsoft-account-application"></a>Vytvoření aplikace účtu Microsoft
Pokud chcete použít účet Microsoft jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, budete muset vytvořit aplikaci účtu Microsoft a zadejte správné parametry. Budete potřebovat účet Microsoft. Pokud ho nemáte, navštivte [ https://www.live.com/ ](https://www.live.com/).

1.  Přejděte [portál pro registraci aplikací Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) a přihlaste se pomocí přihlašovacích údajů účtu Microsoft.
2.  Klikněte na tlačítko **přidat aplikaci**.

    ![Microsoft account – přidání aplikace](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-new-app.png)

3.  Zadejte **název** pro vaši aplikaci **kontaktní e-mail**, zrušte zaškrtnutí políčka **nám vám pomohli začít** a klikněte na tlačítko **vytvořit**.

    ![Účet Microsoft – registrace aplikace](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-name.png)

4.  Zkopírujte hodnotu **Id aplikace**. Budete potřebovat, a nakonfigurovat účet Microsoft jako zprostředkovatele identity ve vašem tenantovi.

    ![Účet Microsoft - kopírování hodnoty Id aplikace](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-id.png)

5.  Klikněte na **přidat platformu**

    ![Microsoft account - přidat platformu](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-platform.png)

6.  Ze seznamu platformy zvolit **webové**.

    ![Účet Microsoft - ze seznamu platformy zvolit Web](media/active-directory-b2c-custom-setup-ms-account-idp/msa-web.png)

7.  Zadejte `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` v **identifikátory URI přesměrování** pole. Nahraďte **{klient}** s názvem vašeho tenanta (například contosob2c).

    ![Účet Microsoft – sada přesměrovat adresy URL](media/active-directory-b2c-custom-setup-ms-account-idp/msa-redirect-url.png)

8.  Klikněte na **generovat nové heslo** pod **tajných klíčů aplikací** oddílu. Kopírovat nové heslo, zobrazí na obrazovce. Budete potřebovat, a nakonfigurovat účet Microsoft jako zprostředkovatele identity ve vašem tenantovi. Toto heslo je důležitým bezpečnostním pověřením.

    ![Microsoft account – vygenerování nového hesla](media/active-directory-b2c-custom-setup-ms-account-idp/msa-generate-new-password.png)

    ![Účet Microsoft - kopii nové heslo](media/active-directory-b2c-custom-setup-ms-account-idp/msa-new-password.png)

9.  Zaškrtněte políčko s textem **podpora Live SDK** pod **rozšířené možnosti** oddílu. Klikněte na **Uložit**.

    ![Účet Microsoft – podpora Live SDK](media/active-directory-b2c-custom-setup-ms-account-idp/msa-live-sdk-support.png)

## <a name="add-the-microsoft-account-application-key-to-azure-ad-b2c"></a>Přidat klíč aplikace účtu Microsoft Azure AD B2C
Federace s účty Microsoft se vyžaduje tajný kód klienta pro účet Microsoft do vztahu důvěryhodnosti Azure AD B2C jménem aplikace. Potřebujete ukládat vaše secert aplikace účtu Microsoft v tenantovi Azure AD B2C:   

1.  Přejděte do svého tenanta Azure AD B2C a vyberte **nastavení B2C** > **architekturu rozhraní identit**
2.  Vyberte **klíče zásad** zobrazíte klíče, které jsou k dispozici ve vašem tenantovi.
3.  Klikněte na tlačítko **+ přidat**.
4.  Pro **možnosti**, použijte **ruční**.
5.  Pro **název**, použijte `MSASecret`.  
    Předpona, která `B2C_1A_` může být automaticky přidán.
6.  V **tajný kód** zadejte váš tajný klíč aplikace Microsoft z https://apps.dev.microsoft.com
7.  Pro **použití klíče**, použijte **podpis**.
8.  Klikněte na **Vytvořit**
9.  Potvrďte, že jste vytvořili klíč `B2C_1A_MSASecret`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Přidat zprostředkovatele deklarací identity ve svojí zásadě rozšíření
Pokud chcete uživatelům umožní přihlásit pomocí Account Microsoft, budete muset definovat Account Microsoft jako poskytovatele deklarací identity. Jinými slovy budete muset zadat koncový bod, který komunikuje se službou Azure AD B2C. Koncový bod poskytuje sadu deklarací identity, které používají Azure AD B2C k ověření, že se ověřil konkrétního uživatele.

Definujte Account Microsoft jako poskytovatele deklarací identity, tak, že přidáte `<ClaimsProvider>` uzlu v souboru rozšíření zásad:

1.  Otevřete soubor rozšíření zásad (TrustFrameworkExtensions.xml) z pracovního adresáře. Pokud potřebujete XML editor [nabídne Visual Studio Code](https://code.visualstudio.com/download), jednoduchý editor napříč platformami.
2.  Najít `<ClaimsProviders>` oddílu
3.  Přidat následující fragment kódu XML v rámci `ClaimsProviders` element:

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

4.  Nahraďte `client_id` hodnotu s Id klienta aplikace Account Microsoft

5.  Uložte soubor.

## <a name="register-the-microsoft-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Registrace Microsoft Account deklarace zprostředkovatele na znaménko nahoru nebo přihlašovací cestu uživatele

V tomto okamžiku je nastavený zprostředkovatele identity, ale není k dispozici v některém z obrazovky přihlášení-registrace/přihlášení. Nyní je třeba přidat zprostředkovatele identity Account Microsoft pro vaše uživatele `SignUpOrSignIn` cesty uživatele. Chcete-li k dispozici, se nám vytvořit duplikát cesty existující uživatele šablony.  Potom přidáme Account Microsoft zprostředkovatele identity:

> [!NOTE]
>
>Pokud jste dříve zkopírovali `<UserJourneys>` element ze základního souboru zásady do souboru rozšíření `TrustFrameworkExtensions.xml`, můžete přeskočit k této sekci.

1.  Otevřete soubor základní zásady (například TrustFrameworkBase.xml).
2.  Najít `<UserJourneys>` elementu a zkopírujte celý obsah `<UserJourneys>` uzlu.
3.  Otevřete soubor rozšíření (například TrustFrameworkExtensions.xml) a vyhledejte `<UserJourneys>` elementu. Pokud element neexistuje, přidejte jeden.
4.  Vložte celý obsah `<UserJourneys>` uzel, který jste zkopírovali jako podřízený objekt `<UserJourneys>` elementu.

### <a name="display-the-button"></a>Zobrazit tlačítko
`<ClaimsProviderSelections>` Element definuje seznam možnosti výběru zprostředkovatele deklarací identity a jejich pořadí.  `<ClaimsProviderSelection>` Element je obdobou k tlačítku na stránce přihlášení-registrace/přihlášení zprostředkovatele identity. Pokud chcete přidat `<ClaimsProviderSelection>` – element pro účet Microsoft, nové tlačítko se zobrazí při uživatel umístil na stránce. Chcete-li přidat tento element:

1.  Najít `<UserJourney>` uzel, který zahrnuje `Id="SignUpOrSignIn"` v cestě uživatele, který jste zkopírovali.
2.  Vyhledejte `<OrchestrationStep>` uzel, který obsahuje `Order="1"`
3.  Přidat následující fragment kódu XML v rámci `<ClaimsProviderSelections>` uzlu:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
```

### <a name="link-the-button-to-an-action"></a>Tlačítko s odkazem na akci
Teď, když máte tlačítko na místě, budete potřebovat odkázat na akci. Akce v tomto případě je pro Azure AD B2C ke komunikaci s Account Microsoft k získání tokenu. Tlačítko odkazu na akci propojením technický profil pro vašeho poskytovatele deklarací identity Account Microsoft:

1.  Najít `<OrchestrationStep>` , který obsahuje `Order="2"` v `<UserJourney>` uzlu.
2.  Přidat následující fragment kódu XML v rámci `<ClaimsExchanges>` uzlu:

```xml
<ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

> [!NOTE]
>
>   * Zkontrolujte, `Id` má stejnou hodnotu jako `TargetClaimsExchangeId` v předchozí části
>   * Zajištění `TechnicalProfileReferenceId` ID je nastaven na technický profil vytvoříte starší (MSA OIDC).

## <a name="upload-the-policy-to-your-tenant"></a>Odeslání zásady do vašeho tenanta
1.  V [webu Azure portal](https://portal.azure.com), přepněte do [kontextu vašeho tenanta Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)a otevřete **Azure AD B2C** okno.
2.  Vyberte **architekturu rozhraní identit**.
3.  Otevřít **všechny zásady** okno.
4.  Vyberte **nahrát zásady**.
5.  Zkontrolujte **přepsat zásady, pokud existuje** pole.
6.  **Nahrát** TrustFrameworkExtensions.xml a ujistěte se, že se selhání ověření

## <a name="test-the-custom-policy-by-using-run-now"></a>Testování vlastní zásady pomocí možnosti spustit hned

1.  Otevřít **nastavení Azure AD B2C** a přejděte na **architekturu rozhraní identit**.
> [!NOTE]
>
>**Spustit nyní** vyžaduje aspoň jednu aplikaci do být registrované u klienta. Informace o postupu registrace aplikací, najdete v tématu Azure AD B2C [Začínáme](active-directory-b2c-get-started.md) článku nebo [registrace aplikace](active-directory-b2c-app-registration.md) článku.
2.  Otevřít **B2C_1A_signup_signin**, předávající stranu vlastní zásady, které jste nahráli. Vyberte **spustit nyní**.
3.  Byste měli být schopni se přihlásit pomocí účtu Microsoft.

## <a name="optional-register-the-microsoft-account-claims-provider-to-profile-edit-user-journey"></a>[Volitelné] Registrace zprostředkovatele deklarací identity Account Microsoft k úpravě profilu cesty uživatele
Můžete také přidat zprostředkovatele identity Account Microsoft pro vaše uživatele `ProfileEdit` cesty uživatele. Chcete-li k dispozici, jsme zopakujte poslední dva kroky:

### <a name="display-the-button"></a>Zobrazit tlačítko
1.  Otevřete soubor rozšíření zásad (například TrustFrameworkExtensions.xml).
2.  Najít `<UserJourney>` uzel, který zahrnuje `Id="ProfileEdit"` v cestě uživatele, který jste zkopírovali.
3.  Vyhledejte `<OrchestrationStep>` uzel, který obsahuje `Order="1"`
4.  Přidat následující fragment kódu XML v rámci `<ClaimsProviderSelections>` uzlu:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>Tlačítko s odkazem na akci
1.  Najít `<OrchestrationStep>` , který obsahuje `Order="2"` v `<UserJourney>` uzlu.
2.  Přidat následující fragment kódu XML v rámci `<ClaimsExchanges>` uzlu:

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Pomocí možnosti spustit hned otestovat vlastní zásady úprav profilu
1.  Otevřít **nastavení Azure AD B2C** a přejděte na **architekturu rozhraní identit**.
2.  Otevřít **B2C_1A_ProfileEdit**, předávající stranu vlastní zásady, které jste nahráli. Vyberte **spustit nyní**.
3.  Byste měli být schopni se přihlásit pomocí účtu Microsoft.

## <a name="download-the-complete-policy-files"></a>Stažení kompletní zásad souborů
Volitelné: Doporučujeme že vytvořit váš scénář pomocí vlastní vlastní zásady, které provedou soubory po dokončení, jak začít s vlastní zásady, namísto použití těchto ukázkových souborů.  [Ukázkové soubory zásad pro odkaz](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-msa-app)
