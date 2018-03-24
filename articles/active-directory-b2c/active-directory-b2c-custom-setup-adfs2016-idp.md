---
title: 'Azure Active Directory B2C: Přidáte jako poskytovatele identity SAML, používat vlastní zásady služby AD FS'
description: 'Postupy: článek o nastavení služby AD FS 2016 pomocí protokolu SAML a vlastní zásady'
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
ms.openlocfilehash: af102bbc3bc7608fe641db19f4af8c760907a564
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-add-adfs-as-a-saml-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: Přidáte jako poskytovatele identity SAML, používat vlastní zásady služby AD FS

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek ukazuje, jak povolit přihlášení pro uživatele z účtu služby AD FS prostřednictvím [vlastní zásady](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v [Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md) článku.

K těmto krokům patří:

1.  Vytvoření AD FS vztah důvěryhodnosti předávající strany.
2.  Certifikát služby AD FS vztah důvěryhodnosti předávající strany se přidává do Azure AD B2C.
3.  Přidání poskytovatele deklarací identity k zásadě.
4.  Registrace služby AD FS účet deklarací zprostředkovatele, který se cesty uživatele.
5.  Odeslání zásady do Azure AD B2C klienta a otestovat ji.

## <a name="to-create-a-claims-aware-relying-party-trust"></a>Chcete-li vytvořit deklaracemi identity vztah důvěryhodnosti předávající strany

Použití služby AD FS jako poskytovatel identit v Azure Active Directory (Azure AD) B2C, musíte k vytvoření AD FS vztah důvěryhodnosti předávající strany a poskytne mu se správné parametry.

Chcete-li přidat nový vztah důvěryhodnosti předávající strany pomocí modulu snap-in Správa služby AD FS a ručně nakonfigurovat nastavení, proveďte následující postup na federačním serveru.

Členství ve skupině **správci**, nebo ekvivalentní v místním počítači je minimálním předpokladem pro dokončení tohoto postupu. Podrobnosti o používání příslušných účtů a členství ve skupinách v [místní a doménové výchozí skupiny](http://go.microsoft.com/fwlink/?LinkId=83477)

1.  Ve Správci serveru klikněte na tlačítko **nástroje**a potom vyberte **správu služby AD FS**.

2.  Klikněte na **přidat vztah důvěryhodnosti předávající strany**.
    ![Přidat vztah důvěryhodnosti předávající strany](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-1.png)

3.  Na **úvodní** vyberte **deklarace identity vědět** a klikněte na tlačítko **spustit**.
    ![Na úvodní stránce zvolte vědět deklarace identity](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-2.png)
4.  Na **vybrat zdroj dat** klikněte na tlačítko **zadat data o předávající straně ručně**a pak klikněte na **Další**.
    ![Zadat data o předávající strany](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-3.png)

5.  Na **zadat zobrazovaný název** stránky, zadejte název do **zobrazovaný název**v části **poznámky** zadejte popis tohoto vztahu důvěryhodnosti předávající strany a pak klikněte na tlačítko **další** .
    ![Zadejte zobrazovaný název a poznámky](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-4.png)
6.  Volitelné. Pokud máte volitelné tokenu šifrovací certifikát, pak na **konfigurace certifikátu** klikněte na tlačítko **Procházet** vyhledejte soubor certifikátu, a pak klikněte na **Další**.
    ![Konfigurace certifikátu](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-5.png)
7.  Na **konfigurace adresy URL** vyberte **povolit podporu protokolu SAML 2.0 WebSSO** zaškrtněte políčko. V části **URL služby SAML 2.0 SSO předávající strany**, zadejte adresu URL koncového bodu služby Security (Assertion Markup Language SAML) pro tento vztah důvěryhodnosti předávající strany a pak klikněte na tlačítko **Další**.  Pro **URL služby SAML 2.0 SSO předávající strany**, vložte `https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/{policy}`. Nahraďte názvem vašeho klienta (například contosob2c.onmicrosoft.com) {klient} a {zásady} nahraďte název zásady přípony (například B2C_1A_TrustFrameworkExtensions).
    > [!IMPORTANT]
    >Název zásady je ten, který signup_or_signin zásad zdědí v tomto případě je: `B2C_1A_TrustFrameworkExtensions`.
    >Adresa URL může být například: https://login.microsoftonline.com/te/ **contosob2c**.onmicrosoft.com/**B2C_1A_TrustFrameworkBase**.

    ![Předávající strany adresa URL služby SAML 2.0 SSO](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-6.png)
8. Na **konfigurovat identifikátory** stránky, zadejte stejnou adresu URL jako v předchozím kroku, klikněte na **přidat** je přidejte do seznamu, a pak klikněte na **Další**.
    ![Předávající strany důvěryhodnosti identifikátory](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-7.png)
9.  Na **zvolte zásad řízení přístupu** vyberte zásadu a klikněte na tlačítko **Další**.
    ![Zvolte zásad řízení přístupu](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-8.png)
10.  Na **připravené k přidání vztahu důvěryhodnosti** stránka, zkontrolujte nastavení a pak klikněte na tlačítko **Další** předávající strany uložit informace o vztahu důvěryhodnosti.
    ![Uložit informace o vztazích důvěryhodnosti předávající strany](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-9.png)
11.  Na **Dokončit** klikněte na tlačítko **Zavřít**, tato akce se automaticky zobrazí **upravit pravidla deklarací identity** dialogové okno.
    ![Upravit pravidla deklarace identity](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-10.png)
12. Klikněte na tlačítko **přidat pravidlo**.  
      ![Přidat nové pravidlo](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-1.png)
13.  V **šablony pravidla deklarace identity**, vyberte **odesílat atributy LDAP jako deklarace identity**.
    ![Vyberte možnost odesílat atributy LDAP jako deklarace šablony](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-2.png)
14.  Zadejte **název pravidla deklarací**. Pro **úložiště atributů** vyberte **služby Active Directory vyberte** přidejte následující deklarace identity a pak klikněte na tlačítko **Dokončit** a **OK**.
    ![Umožňuje nastavit vlastnosti pravidla](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)
15.  Ve Správci serveru vyberte **vztahy důvěryhodnosti předávající strany** pak vyberte předávající straně důvěryhodnosti jste vytvořili a klikněte na tlačítko **vlastnosti**.
    ![Předávající strany upravit vlastnosti](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-1.png)
16.  Jeden okno Vlastnosti vztahu důvěryhodnosti (B2C ukázku) předávající strany, klikněte na tlačítko **podpis** a klikněte na **přidat**.  
    ![Sada podpis](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-2.png)
17.  Přidání vaší podpis certifikátu (bez soukromého klíče soubor .cert).  
    ![Přidejte svůj certifikát podpisu](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-3.png)
18.  V okně Vlastnosti vztahu důvěryhodnosti (B2C ukázku) předávající strany klikněte na tlačítko **Upřesnit** kartě a změňte **zabezpečený algoritmus hash** k **SHA-1**, klikněte na tlačítko **Ok**.  
    ![Nastavit zabezpečený algoritmus hash SHA-1](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-4.png)

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>Přidat klíč služby AD FS účet aplikace do Azure AD B2C
Federace s účty služby AD FS vyžaduje tajný klíč klienta pro účet služby AD FS do vztahu důvěryhodnosti Azure AD B2C jménem aplikace. Potřebujete k uložení certifikátu služby AD FS v klientovi služby Azure AD B2C. 

1.  Přejděte ke klientovi Azure AD B2C a vyberte **nastavení B2C** > **Identity rozhraní Framework**
2.  Vyberte **zásad klíče** zobrazíte klíče, které jsou k dispozici ve vašem klientovi.
3.  Klikněte na tlačítko **+ přidat**.
4.  Pro **možnosti**, použijte **nahrát**.
5.  Pro **název**, použijte `ADFSSamlCert`.  
    Předpona `B2C_1A_` může automaticky přidat.
6.  V samotné nahrávání souborů ** vyberte soubor .pfx certifikátu s privátním klíčem. Poznámka: Tento certifikát (s privátním klíčem) by měl být stejný jako ten, který vydala a používat pro předávající strany služby AD FS.
![Odešlete klíč zásad](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-cert.png)
7.  Klikněte na **Vytvořit**
8.  Potvrďte, že jste vytvořili klíč `B2C_1A_ADFSSamlCert`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Přidání poskytovatele deklarací identity v rozšíření zásady
Pokud chcete uživatelům přihlášení pomocí účtu služby AD FS, musíte zadat účet služby AD FS jako poskytovatele deklarací identity. Jinými slovy budete muset zadat koncový bod, který komunikuje se službou Azure AD B2C. Koncový bod poskytuje sadu deklarací identity, které používají Azure AD B2C k ověření, že byl ověřen konkrétního uživatele.

Definování služby AD FS jako poskytovatele deklarací identity, přidáním `<ClaimsProvider>` uzlu v souboru rozšíření zásad:

1. Otevřete soubor rozšíření zásad (TrustFrameworkExtensions.xml) z pracovního adresáře. Pokud potřebujete editoru XML [zkuste Visual Studio Code](https://code.visualstudio.com/download), lightweight editor napříč platformami.
2. Najít `<ClaimsProviders>` části
3. Přidejte následující fragment kódu XML v části `ClaimsProviders` elementu a nahraďte `identityProvider` s DNS (libovolná hodnota, kterou určuje doménu) a uložte soubor. 

```xml
<ClaimsProvider>
    <Domain>contoso.com</Domain>
    <DisplayName>Contoso ADFS</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="Contoso-SAML2">
        <DisplayName>Contoso ADFS</DisplayName>
        <Description>Login with your Contoso account</Description>
        <Protocol Name="SAML2"/>
        <Metadata>
        <Item Key="RequestsSigned">false</Item>
        <Item Key="WantsEncryptedAssertions">false</Item>
        <Item Key="PartnerEntity">https://{your_ADFS_domain}/federationmetadata/2007-06/federationmetadata.xml</Item>
        </Metadata>
        <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
        </CryptographicKeys>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userPrincipalName" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
        <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
        </OutputClaims>
        <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
        <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
        <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="register-the-adfs-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Zaregistrujte zprostředkovatele deklarací identity účtu služby AD FS k přihlášení až nebo přihlášení uživatele cesty
V tomto okamžiku zprostředkovatele identity má nastaven.  Však není k dispozici v žádném z obrazovky registrace-množství nebo přihlášení. Nyní je nutné přidat zprostředkovatele identity účtu služby AD FS pro vaše uživatele `SignUpOrSignIn` cesty uživatele. Chcete-li k dispozici, vytvoříme duplicitní existující uživatele cesty šablony.  Potom jsme upravit ji tak, aby zahrnovala zprostředkovatele identity služby AD FS:

>[!NOTE]
>Pokud jste dříve zkopírovali `<UserJourneys>` element ze základního souboru zásad k rozšíření souboru (TrustFrameworkExtensions.xml), můžete tuto část přeskočit.

1.  Otevřete soubor základní zásad (například TrustFrameworkBase.xml).
2.  Najít `<UserJourneys>` elementu a zkopírujte celý obsah `<UserJourneys>` uzlu.
3.  Otevřete soubor rozšíření (například TrustFrameworkExtensions.xml) a najděte `<UserJourneys>` elementu. Pokud element neexistuje, přidejte jedno.
4.  Vložte celý obsah `<UserJournesy>` uzlu, který jste zkopírovali jako podřízenou `<UserJourneys>` elementu.

### <a name="display-the-button"></a>Zobrazení tlačítka
`<ClaimsProviderSelections>` Element definuje seznam možnosti výběru poskytovatele deklarací identity a jejich pořadí.  `<ClaimsProviderSelection>` Element je obdobou tlačítko zprostředkovatele identity na stránce registrace-množství nebo přihlášení. Pokud přidáte `<ClaimsProviderSelection>` element pro účet služby AD FS, nové tlačítko se zobrazí při pojmenováváme uživatele na stránce. Chcete-li přidat tento element:

1.  Najít `<UserJourney>` uzlu, který zahrnuje `Id="SignUpOrSignIn"` v cesty uživatele, který jste zkopírovali.
2.  Vyhledejte `<OrchestrationStep>` uzlu, který obsahuje `Order="1"`
3.  Přidejte následující fragment kódu XML v části `<ClaimsProviderSelections>` uzlu:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```
### <a name="link-the-button-to-an-action"></a>Tlačítko propojit akce

Nyní když máte tlačítka na místě, budete muset propojit akce. Akce, v takovém případě je pro Azure AD B2C ke komunikaci s účtem služby AD FS přijmout token. Odkaz na tlačítko akce pomocí propojení technické profil pro poskytovatele deklarací identity účtu služby AD FS:

1.  Najít `<OrchestrationStep>` , který obsahuje `Order="2"` v `<UserJourney>` uzlu.
2.  Přidejte následující fragment kódu XML v části `<ClaimsExchanges>` uzlu:

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

> [!NOTE]
> * Ujistěte se, `Id` mají stejnou hodnotu jako `TargetClaimsExchangeId` v předchozí části.
> * Ujistěte se, `TechnicalProfileReferenceId` je nastaven na technické profil vytvoříte starší (Contoso-typu SAML2).

## <a name="upload-the-policy-to-your-tenant"></a>Nahrát zásady klienta
1.  V [portál Azure](https://portal.azure.com), přepněte do [kontextu klienta služby Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)a otevřete **Azure AD B2C** okno.
2.  Vyberte **Identity rozhraní Framework**.
3.  Otevřete **všechny zásady** okno.
4.  Vyberte **nahrát zásady**.
5.  Zkontrolujte **přepsat zásady, pokud existuje** pole.
6.  **Nahrát** TrustFrameworkExtensions.xml a ujistěte se, že neselže ověření

## <a name="test-the-custom-policy-by-using-run-now"></a>Otestovat vlastní zásady pomocí spustit nyní
1.  Otevřete **nastavení Azure AD B2C** a přejděte na **Identity rozhraní Framework**.
2.  Otevřete **B2C_1A_signup_signin**, předávající stranu vlastních zásad, který jste nahráli. Vyberte **spustit nyní**.
3.  Nyní byste měli mít k přihlášení pomocí účtu služby AD FS.

## <a name="optional-register-the-adfs-account-claims-provider-to-profile-edit-user-journey"></a>[Nepovinné] Zaregistrujte poskytovatele deklarací identity účtu služby AD FS pro úpravy profilu uživatele cesty
Můžete také přidat zprostředkovatele identity účtu služby AD FS pro vaše uživatele `ProfileEdit` cesty uživatele. Chcete-li k dispozici, jsme zopakujte poslední dva kroky:

### <a name="display-the-button"></a>Zobrazení tlačítka
1.  Otevřete soubor rozšíření zásad (například TrustFrameworkExtensions.xml).
2.  Najít `<UserJourney>` uzlu, který zahrnuje `Id="ProfileEdit"` v cesty uživatele, který jste zkopírovali.
3.  Vyhledejte `<OrchestrationStep>` uzlu, který obsahuje `Order="1"`
4.  Přidejte následující fragment kódu XML v části `<ClaimsProviderSelections>` uzlu:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```

### <a name="link-the-button-to-an-action"></a>Tlačítko propojit akce
1.  Najít `<OrchestrationStep>` , který obsahuje `Order="2"` v `<UserJourney>` uzlu.
2.  Přidejte následující fragment kódu XML v části `<ClaimsExchanges>` uzlu:

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Otestovat vlastní úpravy profilu zásady pomocí spustit nyní
1.  Otevřete **nastavení Azure AD B2C** a přejděte na **Identity rozhraní Framework**.
2.  Otevřete **B2C_1A_ProfileEdit**, předávající stranu vlastních zásad, který jste nahráli. Vyberte **spustit nyní**.
3.  Nyní byste měli mít k přihlášení pomocí účtu služby AD FS.

## <a name="download-the-complete-policy-files"></a>Stáhnout soubory dokončení zásad
Volitelné: Doporučujeme že vytvořit váš scénář pomocí vlastní vlastní zásady, které provede soubory po dokončení Začínáme s vlastní zásady. [Ukázkové soubory zásad jenom jako reference](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app)
