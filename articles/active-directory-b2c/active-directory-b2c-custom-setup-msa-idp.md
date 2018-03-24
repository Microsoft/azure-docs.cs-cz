---
title: 'Azure Active Directory B2C: Přidáte účet Microsoft (MSA) jako zprostředkovatele identity pomocí vlastních zásad'
description: Ukázka použití Microsoft jako zprostředkovatele identity pomocí protokolu OpenID Connect (OIDC)
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/04/2017
ms.author: davidmu
ms.openlocfilehash: a49e9589322eeb90a713321b4fbe4c4820609f7a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-add-microsoft-account-msa-as-an-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: Přidáte účet Microsoft (MSA) jako zprostředkovatele identity pomocí vlastních zásad

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek ukazuje, jak povolit přihlášení pro uživatele z účtu Microsoft (MSA) prostřednictvím [vlastní zásady](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Požadavky
Proveďte kroky v [Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md) článku.

K těmto krokům patří:

1.  Vytvoření aplikace pro účet Microsoft.
2.  Přidání klíče aplikace účtu Microsoft k Azure AD B2C
3.  Přidání poskytovatele deklarací identity k zásadě
4.  Registrace zprostředkovatele deklarací identity Account Microsoft k cesty uživatele
5.  Odeslání zásady do Azure AD B2C klienta a otestovat ji

## <a name="create-a-microsoft-account-application"></a>Vytvoření aplikace účtu Microsoft
Pokud chcete používat účet Microsoft jako poskytovatel identit v Azure Active Directory (Azure AD) B2C, musíte vytvořit aplikaci účtu Microsoft a zadejte se správné parametry. Potřebujete účet Microsoft. Pokud nemáte, navštivte [ https://www.live.com/ ](https://www.live.com/).

1.  Přejděte na [portálu pro registraci aplikace Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) a přihlaste se pomocí přihlašovacích údajů účtu Microsoft.
2.  Klikněte na tlačítko **přidat aplikaci**.

    ![Microsoft účet – přidat aplikaci](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-new-app.png)

3.  Zadejte **název** pro vaši aplikaci **kontaktovat e-mailu**, zrušte zaškrtnutí políčka **dejte nám vám pomohli začít** a klikněte na tlačítko **vytvořit**.

    ![Účet Microsoft - registrace vaší aplikace](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-name.png)

4.  Zkopírujte hodnotu **Id aplikace**. Je třeba ji nakonfigurovat účet Microsoft jako zprostředkovatel identity ve vašem klientovi.

    ![Účet Microsoft - kopírování hodnoty ID aplikace](media/active-directory-b2c-custom-setup-ms-account-idp/msa-app-id.png)

5.  Klikněte na **přidat platformy**

    ![Microsoft účet – přidejte platformu](media/active-directory-b2c-custom-setup-ms-account-idp/msa-add-platform.png)

6.  Vyberte ze seznamu platformy **webové**.

    ![Účet Microsoft - ze seznamu platformy vyberte Web](media/active-directory-b2c-custom-setup-ms-account-idp/msa-web.png)

7.  Zadejte `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` v **identifikátory URI přesměrování** pole. Nahraďte **{klient}** s názvem vašeho klienta (například contosob2c.onmicrosoft.com).

    ![Účet Microsoft - Set přesměrování adresy URL](media/active-directory-b2c-custom-setup-ms-account-idp/msa-redirect-url.png)

8.  Klikněte na **generovat nové heslo** pod **tajné klíče aplikace** části. Zkopírujte nové heslo, které jsou zobrazené na obrazovce. Je třeba ji nakonfigurovat účet Microsoft jako zprostředkovatel identity ve vašem klientovi. Toto heslo je důležitým bezpečnostním pověřením.

    ![Microsoft účet - vytvořit nové heslo](media/active-directory-b2c-custom-setup-ms-account-idp/msa-generate-new-password.png)

    ![Účet Microsoft - kopie nové heslo](media/active-directory-b2c-custom-setup-ms-account-idp/msa-new-password.png)

9.  Zaškrtněte políčko, která uvádí, že **Live SDK podporu** pod **pokročilé možnosti** části. Klikněte na **Uložit**.

    ![Účet Microsoft - Live SDK podpory](media/active-directory-b2c-custom-setup-ms-account-idp/msa-live-sdk-support.png)

## <a name="add-the-microsoft-account-application-key-to-azure-ad-b2c"></a>Přidejte klíč aplikace účtu Microsoft k Azure AD B2C
Federace s účty Microsoft vyžaduje tajný klíč klienta pro účet Microsoft do vztahu důvěryhodnosti Azure AD B2C jménem aplikace. Je třeba uložit vaše secert aplikace účtu Microsoft v klientovi Azure AD B2C:   

1.  Přejděte ke klientovi Azure AD B2C a vyberte **nastavení B2C** > **Identity rozhraní Framework**
2.  Vyberte **zásad klíče** zobrazíte klíče, které jsou k dispozici ve vašem klientovi.
3.  Klikněte na tlačítko **+ přidat**.
4.  Pro **možnosti**, použijte **ruční**.
5.  Pro **název**, použijte `MSASecret`.  
    Předpona `B2C_1A_` může automaticky přidat.
6.  V **tajný klíč** zadejte váš tajný klíč aplikace Microsoft z https://apps.dev.microsoft.com
7.  Pro **použití klíče**, použijte **podpis**.
8.  Klikněte na **Vytvořit**
9.  Potvrďte, že jste vytvořili klíč `B2C_1A_MSASecret`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Přidání poskytovatele deklarací identity v rozšíření zásady
Pokud chcete uživatelům se přihlásit pomocí Account Microsoft, budete muset definovat Account Microsoft jako poskytovatele deklarací identity. Jinými slovy budete muset zadat koncový bod, který komunikuje se službou Azure AD B2C. Koncový bod poskytuje sadu deklarací identity, které používají Azure AD B2C k ověření, že byl ověřen konkrétního uživatele.

Zadejte Microsoft Account jako poskytovatele deklarací identity, přidáním `<ClaimsProvider>` uzlu v souboru rozšíření zásad:

1.  Otevřete soubor rozšíření zásad (TrustFrameworkExtensions.xml) z pracovního adresáře. Pokud potřebujete editoru XML [zkuste Visual Studio Code](https://code.visualstudio.com/download), lightweight editor napříč platformami.
2.  Najít `<ClaimsProviders>` části
3.  Přidejte následující fragment kódu XML v části `ClaimsProviders` element:

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

4.  Nahraďte `client_id` hodnota se váš klient aplikace Microsoft Account Id

5.  Uložte soubor.

## <a name="register-the-microsoft-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Registrace Microsoft Account deklarace zprostředkovatele podepsání nahoru nebo přihlášení uživatele cesty

V tomto okamžiku byla nastavena zprostředkovatele identity, ale není k dispozici v žádném z obrazovky registrace-množství nebo přihlášení. Nyní je nutné přidat zprostředkovatele identity Account Microsoft pro vaše uživatele `SignUpOrSignIn` cesty uživatele. Chcete-li k dispozici, vytvoříme duplicitní existující uživatele cesty šablony.  Potom přidáme zprostředkovatele identity Account Microsoft:

> [!NOTE]
>
>Pokud jste dříve zkopírovali `<UserJourneys>` element ze základního souboru zásad k souboru rozšíření `TrustFrameworkExtensions.xml`, můžete přeskočit k této části.

1.  Otevřete soubor základní zásad (například TrustFrameworkBase.xml).
2.  Najít `<UserJourneys>` elementu a zkopírujte celý obsah `<UserJourneys>` uzlu.
3.  Otevřete soubor rozšíření (například TrustFrameworkExtensions.xml) a najděte `<UserJourneys>` elementu. Pokud element neexistuje, přidejte jedno.
4.  Vložte celý obsah `<UserJournesy>` uzlu, který jste zkopírovali jako podřízenou `<UserJourneys>` elementu.

### <a name="display-the-button"></a>Zobrazení tlačítka
`<ClaimsProviderSelections>` Element definuje seznam možnosti výběru poskytovatele deklarací identity a jejich pořadí.  `<ClaimsProviderSelection>` Element je obdobou tlačítko zprostředkovatele identity na stránce registrace-množství nebo přihlášení. Pokud přidáte `<ClaimsProviderSelection>` element pro účet Microsoft, nové tlačítko se zobrazí při pojmenováváme uživatele na stránce. Chcete-li přidat tento element:

1.  Najít `<UserJourney>` uzlu, který zahrnuje `Id="SignUpOrSignIn"` v cesty uživatele, který jste zkopírovali.
2.  Vyhledejte `<OrchestrationStep>` uzlu, který obsahuje `Order="1"`
3.  Přidejte následující fragment kódu XML v části `<ClaimsProviderSelections>` uzlu:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>Tlačítko propojit akce
Nyní když máte tlačítka na místě, budete muset propojit akce. Akce, v takovém případě je pro Azure AD B2C ke komunikaci s Account Microsoft přijmout token. Pomocí propojení technické profil pro poskytovatele deklarací identity Microsoft Account odkazu na tlačítko akce:

1.  Najít `<OrchestrationStep>` , který obsahuje `Order="2"` v `<UserJourney>` uzlu.
2.  Přidejte následující fragment kódu XML v části `<ClaimsExchanges>` uzlu:

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

> [!NOTE]
>
>   * Ujistěte se, `Id` mají stejnou hodnotu jako `TargetClaimsExchangeId` v předchozí části
>   * Ujistěte se, `TechnicalProfileReferenceId` ID je nastaveno na technické profil vytvoříte starší (MSA OIDC).

## <a name="upload-the-policy-to-your-tenant"></a>Nahrát zásady klienta
1.  V [portál Azure](https://portal.azure.com), přepněte do [kontextu klienta služby Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)a otevřete **Azure AD B2C** okno.
2.  Vyberte **Identity rozhraní Framework**.
3.  Otevřete **všechny zásady** okno.
4.  Vyberte **nahrát zásady**.
5.  Zkontrolujte **přepsat zásady, pokud existuje** pole.
6.  **Nahrát** TrustFrameworkExtensions.xml a ujistěte se, že neselže ověření

## <a name="test-the-custom-policy-by-using-run-now"></a>Otestovat vlastní zásady pomocí spustit nyní

1.  Otevřete **nastavení Azure AD B2C** a přejděte na **Identity rozhraní Framework**.
> [!NOTE]
>
>**Spustit nyní** vyžaduje alespoň jedné aplikace do být preregistered u klienta. Další postup registrace aplikace najdete v tématu Azure AD B2C [Začínáme](active-directory-b2c-get-started.md) článek nebo [registrace aplikace](active-directory-b2c-app-registration.md) článku.
2.  Otevřete **B2C_1A_signup_signin**, předávající stranu vlastních zásad, který jste nahráli. Vyberte **spustit nyní**.
3.  Byste měli být schopní se přihlásit pomocí účtu Microsoft.

## <a name="optional-register-the-microsoft-account-claims-provider-to-profile-edit-user-journey"></a>[Nepovinné] Zaregistrujte poskytovatele deklarací identity Account Microsoft pro úpravy profilu uživatele cesty
Můžete také přidat zprostředkovatele identity Account Microsoft pro vaše uživatele `ProfileEdit` cesty uživatele. Chcete-li k dispozici, jsme zopakujte poslední dva kroky:

### <a name="display-the-button"></a>Zobrazení tlačítka
1.  Otevřete soubor rozšíření zásad (například TrustFrameworkExtensions.xml).
2.  Najít `<UserJourney>` uzlu, který zahrnuje `Id="ProfileEdit"` v cesty uživatele, který jste zkopírovali.
3.  Vyhledejte `<OrchestrationStep>` uzlu, který obsahuje `Order="1"`
4.  Přidejte následující fragment kódu XML v části `<ClaimsProviderSelections>` uzlu:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="MSAExchange" />
```

### <a name="link-the-button-to-an-action"></a>Tlačítko propojit akce
1.  Najít `<OrchestrationStep>` , který obsahuje `Order="2"` v `<UserJourney>` uzlu.
2.  Přidejte následující fragment kódu XML v části `<ClaimsExchanges>` uzlu:

```xml
<ClaimsExchange Id="MSAExchange" TechnicalProfileReferenceId="MSA-OIDC" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Otestovat vlastní úpravy profilu zásady pomocí spustit nyní
1.  Otevřete **nastavení Azure AD B2C** a přejděte na **Identity rozhraní Framework**.
2.  Otevřete **B2C_1A_ProfileEdit**, předávající stranu vlastních zásad, který jste nahráli. Vyberte **spustit nyní**.
3.  Byste měli být schopní se přihlásit pomocí účtu Microsoft.

## <a name="download-the-complete-policy-files"></a>Stáhnout soubory dokončení zásad
Volitelné: Doporučujeme že vytvořit váš scénář pomocí vlastní vlastní zásady, které soubory po dokončení Začínáme se zásadami vlastní provede namísto použití tyto ukázkové soubory.  [Ukázkové soubory zásad pro – referenční informace](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-msa-app)
