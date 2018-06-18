---
title: Přidat Google + jako zprostředkovatele identity OAuth2 pomocí vlastních zásad v Azure Active Directory B2C | Microsoft Docs
description: Ukázka použití Google + jako zprostředkovatele identity pomocí protokolu OAuth2.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 3be17ca815910b7472a8760529971bd6c2016792
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34709270"
---
# <a name="azure-active-directory-b2c-add-google-as-an-oauth2-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: Přidejte Google + jako zprostředkovatele identity OAuth2 pomocí vlastních zásad

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento průvodce vám ukáže, jak povolit přihlášení pro uživatele z účtu Google + prostřednictvím [vlastní zásady](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v [Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md) článku.

K těmto krokům patří:

1.  Vytvoření aplikace pro účet Google +.
2.  Přidání aplikace klíč účtu Google + do Azure AD B2C
3.  Přidání poskytovatele deklarací identity k zásadě
4.  Registrace Google + účet zprostředkovatele deklarací identity pro uživatele cesty
5.  Odeslání zásady do Azure AD B2C klienta a otestovat ji

## <a name="create-a-google-account-application"></a>Vytvoření aplikace účet Google +
Pokud chcete používat Google + jako poskytovatel identit v Azure Active Directory (Azure AD) B2C, musíte vytvořit aplikaci Google + a zadat se správné parametry. Můžete zaregistrovat Google aplikace + tady: [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)

1.  Přejděte na [konzole pro vývojáře Google](https://console.developers.google.com/) a přihlaste se pomocí přihlašovací údaje účtu Google +.
2.  Klikněte na tlačítko **vytvořit projekt**, zadejte **název projektu**a potom klikněte na **vytvořit**.

3.  Klikněte na **projekty nabídky**.

    ![Google + účet – vyberte projektu](media/active-directory-b2c-custom-setup-goog-idp/goog-add-new-app1.png)

4.  Klikněte na **+** tlačítko.

    ![Google + účet – vytvoření nového projektu](media/active-directory-b2c-custom-setup-goog-idp//goog-add-new-app2.png)

5.  Zadejte **název projektu**a potom klikněte na **vytvořit**.

    ![Google + účet – nový projekt](media/active-directory-b2c-custom-setup-goog-idp//goog-app-name.png)

6.  Počkejte, dokud není připraven na projekt a klikněte na **projekty nabídky**.

    ![Google + účet – Počkejte, až je připravený k použití nového projektu](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app1.png)

7.  Klikněte na název projektu.

    ![Google + účet – vyberte nový projekt](media/active-directory-b2c-custom-setup-goog-idp//goog-select-app2.png)

8.  Klikněte na tlačítko **rozhraní API Správce** a pak klikněte na **pověření** v levém navigačním panelu.
9.  Klikněte **obrazovky souhlas OAuth** v horní části.

    ![Google + účet – nastavte OAuth souhlasu obrazovky](media/active-directory-b2c-custom-setup-goog-idp/goog-add-cred.png)

10.  Vyberte nebo zadejte platný **e-mailová adresa**, poskytovat **název produktu**a klikněte na tlačítko **Uložit**.

    ![Google + - přihlašovací údaje aplikací](media/active-directory-b2c-custom-setup-goog-idp/goog-consent-screen.png)

11.  Klikněte na tlačítko **nové přihlašovací údaje** a potom zvolte **ID klienta OAuth**.

    ![Google + - vytvořit nové přihlašovací údaje aplikací](media/active-directory-b2c-custom-setup-goog-idp/goog-add-oauth2-client-id.png)

12.  V části **typ aplikace**, vyberte **webové aplikace**.

    ![Google + - výběr typu aplikace](media/active-directory-b2c-custom-setup-goog-idp/goog-web-app.png)

13.  Zadejte **název** pro vaši aplikaci, zadejte `https://login.microsoftonline.com` v **zdroje oprávnění JavaScript** pole, a `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` v **autorizováno přesměrování identifikátory URI** pole. Nahraďte **{klient}** s názvem vašeho klienta (například contosob2c.onmicrosoft.com). **{Klient}** hodnota je malá a velká písmena. Klikněte na možnost **Vytvořit**.

    ![Google + - zadejte oprávnění JavaScript zdroje a identifikátory URI pro přesměrování](media/active-directory-b2c-custom-setup-goog-idp/goog-create-client-id.png)

14.  Zkopírujte hodnoty z **Id klienta** a **tajný klíč klienta**. Je nutné obě konfigurace Google + jako zprostředkovatele identity ve vašem klientovi. **Tajný klíč klienta** je důležitým bezpečnostním pověřením.

    ![Google + - zkopírujte hodnoty Id a klienta sdílený tajný klíč klienta](media/active-directory-b2c-custom-setup-goog-idp/goog-client-secret.png)

## <a name="add-the-google-account-application-key-to-azure-ad-b2c"></a>Klíč aplikace účet Google + přidat do Azure AD B2C
Federace se Google + účty vyžaduje tajný klíč klienta pro účet Google + do vztahu důvěryhodnosti Azure AD B2C jménem aplikace. Je třeba uložit vaše Google + tajný klíč aplikace v Azure AD B2C klienta:  

1.  Přejděte ke klientovi Azure AD B2C a vyberte **nastavení B2C** > **Identity rozhraní Framework**
2.  Vyberte **zásad klíče** zobrazíte klíče, které jsou k dispozici ve vašem klientovi.
3.  Klikněte na tlačítko **+ přidat**.
4.  Pro **možnosti**, použijte **ruční**.
5.  Pro **název**, použijte `GoogleSecret`.  
    Předpona `B2C_1A_` může automaticky přidat.
6.  V **tajný klíč** zadejte tajný klíč Google vaší aplikace z [konzole pro vývojáře Google](https://console.developers.google.com/) zkopírovaného výše.
7.  Pro **použití klíče**, použijte **podpis**.
8.  Klikněte na **Vytvořit**
9.  Potvrďte, že jste vytvořili klíč `B2C_1A_GoogleSecret`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Přidání poskytovatele deklarací identity v rozšíření zásady

Pokud chcete uživatelům přihlášení pomocí účtu Google +, musíte zadat účet Google + jako poskytovatele deklarací identity. Jinými slovy budete muset zadat koncový bod, který komunikuje se službou Azure AD B2C. Koncový bod poskytuje sadu deklarací identity, které používají Azure AD B2C k ověření, že byl ověřen konkrétního uživatele.

Zadejte účet Google + jako poskytovatele deklarací identity, přidáním `<ClaimsProvider>` uzlu v souboru rozšíření zásad:

1.  Otevřete soubor rozšíření zásad (TrustFrameworkExtensions.xml) z pracovního adresáře. Pokud potřebujete editoru XML [zkuste Visual Studio Code](https://code.visualstudio.com/download), lightweight editor napříč platformami.
2.  Najít `<ClaimsProviders>` části
3.  Přidejte následující fragment kódu XML v části `ClaimsProviders` elementu a nahraďte `client_id` hodnotu s vaší Google + účet ID klienta aplikace před uložením souboru.  

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

## <a name="register-the-google-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Zaregistrovat Google + účet zprostředkovatele deklarací identity registrace nebo přihlášení uživatele cesty

Poskytovatel identity nastavit.  Však není k dispozici v žádném z obrazovky registrace-množství nebo přihlášení. Přidejte zprostředkovatele identity účet Google + pro vaše uživatele `SignUpOrSignIn` cesty uživatele. Chcete-li k dispozici, vytvoříme duplicitní existující uživatele cesty šablony.  Potom přidáme zprostředkovatele identity účet Google +:

>[!NOTE]
>
>Pokud jste zkopírovali `<UserJourneys>` element ze základního souboru zásad k rozšíření souboru (TrustFrameworkExtensions.xml), můžete přeskočit k této části.

1.  Otevřete soubor základní zásad (například TrustFrameworkBase.xml).
2.  Najít `<UserJourneys>` elementu a zkopírujte celý obsah `<UserJourneys>` uzlu.
3.  Otevřete soubor rozšíření (například TrustFrameworkExtensions.xml) a najděte `<UserJourneys>` elementu. Pokud element neexistuje, přidejte jedno.
4.  Vložte celý obsah `<UserJourney>` uzlu, který jste zkopírovali jako podřízenou `<UserJourneys>` elementu.

### <a name="display-the-button"></a>Zobrazení tlačítka
`<ClaimsProviderSelections>` Element definuje seznam možnosti výběru poskytovatele deklarací identity a jejich pořadí.  `<ClaimsProviderSelection>` Element je obdobou tlačítko zprostředkovatele identity na stránce registrace-množství nebo přihlášení. Pokud přidáte `<ClaimsProviderSelection>` element pro účet Google + nové tlačítko se zobrazí při pojmenováváme uživatele na stránce. Chcete-li přidat tento element:

1.  Najít `<UserJourney>` uzlu, který zahrnuje `Id="SignUpOrSignIn"` v cesty uživatele, který jste zkopírovali.
2.  Vyhledejte `<OrchestrationStep>` uzlu, který obsahuje `Order="1"`
3.  Přidejte následující fragment kódu XML v části `<ClaimsProviderSelections>` uzlu:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>Tlačítko propojit akce
Nyní když máte tlačítka na místě, budete muset propojit akce. Akce, v takovém případě je pro Azure AD B2C ke komunikaci s účet Google + přijmout token.

1.  Najít `<OrchestrationStep>` , který obsahuje `Order="2"` v `<UserJourney>` uzlu.
2.  Přidejte následující fragment kódu XML v části `<ClaimsExchanges>` uzlu:

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

>[!NOTE]
>
> * Ujistěte se, `Id` mají stejnou hodnotu jako `TargetClaimsExchangeId` v předchozí části
> * Ujistěte se, `TechnicalProfileReferenceId` ID je nastaveno na technické profil vytvoříte starší (Google OAUTH).

## <a name="upload-the-policy-to-your-tenant"></a>Nahrát zásady klienta
1.  V [portál Azure](https://portal.azure.com), přepněte do [kontextu klienta služby Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)a otevřete **Azure AD B2C** okno.
2.  Vyberte **Identity rozhraní Framework**.
3.  Otevřete **všechny zásady** okno.
4.  Vyberte **nahrát zásady**.
5.  Zkontrolujte **přepsat zásady, pokud existuje** pole.
6.  **Nahrát** TrustFrameworkExtensions.xml a ujistěte se, že neselže ověření

## <a name="test-the-custom-policy-by-using-run-now"></a>Otestovat vlastní zásady pomocí spustit nyní
1.  Otevřete **nastavení Azure AD B2C** a přejděte na **Identity rozhraní Framework**.

    >[!NOTE]
    >
    >    **Spustit nyní** vyžaduje alespoň jedné aplikace do být preregistered u klienta. 
    >    Další postup registrace aplikace najdete v tématu Azure AD B2C [Začínáme](active-directory-b2c-get-started.md) článek nebo [registrace aplikace](active-directory-b2c-app-registration.md) článku.


2.  Otevřete **B2C_1A_signup_signin**, předávající stranu vlastních zásad, který jste nahráli. Vyberte **spustit nyní**.
3.  Nyní byste měli mít k přihlášení pomocí účtu Google +.

## <a name="optional-register-the-google-account-claims-provider-to-profile-edit-user-journey"></a>[Nepovinné] Zaregistrovat Google + účet zprostředkovatele deklarací identity pro úpravy profilu uživatele cesty
Můžete také přidat zprostředkovatele identity účet Google + pro vaše uživatele `ProfileEdit` cesty uživatele. Chcete-li k dispozici, jsme zopakujte poslední dva kroky:

### <a name="display-the-button"></a>Zobrazení tlačítka
1.  Otevřete soubor rozšíření zásad (například TrustFrameworkExtensions.xml).
2.  Najít `<UserJourney>` uzlu, který zahrnuje `Id="ProfileEdit"` v cesty uživatele, který jste zkopírovali.
3.  Vyhledejte `<OrchestrationStep>` uzlu, který obsahuje `Order="1"`
4.  Přidejte následující fragment kódu XML v části `<ClaimsProviderSelections>` uzlu:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
```

### <a name="link-the-button-to-an-action"></a>Tlačítko propojit akce
1.  Najít `<OrchestrationStep>` , který obsahuje `Order="2"` v `<UserJourney>` uzlu.
2.  Přidejte následující fragment kódu XML v části `<ClaimsExchanges>` uzlu:

```xml
<ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
```

### <a name="upload-the-policy-to-your-tenant"></a>Nahrát zásady klienta
1.  V [portál Azure](https://portal.azure.com), přepněte do [kontextu klienta služby Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)a otevřete **Azure AD B2C** okno.
2.  Vyberte **Identity rozhraní Framework**.
3.  Otevřete **všechny zásady** okno.
4.  Vyberte **nahrát zásady**.
5.  Zkontrolujte **přepsat zásady, pokud existuje** pole.
6.  **Nahrát** TrustFrameworkExtensions.xml a ujistěte se, že ověření neselže.

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Otestovat vlastní úpravy profilu zásady pomocí spustit nyní

1.  Otevřete **nastavení Azure AD B2C** a přejděte na **Identity rozhraní Framework**.
2.  Otevřete **B2C_1A_ProfileEdit**, předávající stranu vlastních zásad, který jste nahráli. Vyberte **spustit nyní**.
3.  Nyní byste měli mít k přihlášení pomocí účtu Google +.

## <a name="download-the-complete-policy-files"></a>Stáhnout soubory dokončení zásad
Volitelné: Doporučujeme že vytvořit váš scénář pomocí vlastní vlastní zásady, které soubory po dokončení Začínáme se zásadami vlastní provede namísto použití tyto ukázkové soubory.  [Ukázkové soubory zásad pro – referenční informace](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-goog-app)
