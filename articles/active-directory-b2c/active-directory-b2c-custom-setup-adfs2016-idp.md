---
title: Přidání služby AD FS jako zprostředkovatele identity SAML pomocí vlastních zásad v Azure Active Directory B2C | Dokumentace Microsoftu
description: Článek o nastavení služby AD FS 2016 pomocí protokolu SAML a vlastních zásad
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 4380d21eded3f97e3b3107e78c9544a09d1b0bb2
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338526"
---
# <a name="azure-active-directory-b2c-add-adfs-as-a-saml-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: Přidání služby AD FS jako zprostředkovatele identity SAML pomocí vlastních zásad

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku se dozvíte, jak povolit přihlášení pro uživatele z účtu služby AD FS prostřednictvím [vlastní zásady](active-directory-b2c-overview-custom.md).

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v [Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md) článku.

Tyto kroky zahrnují:

1.  Vytvoření AD FS vztah důvěryhodnosti předávající strany.
2.  Přidání certifikátu do služby AD FS vztah důvěryhodnosti přijímající strany Azure AD B2C.
3.  Přidání poskytovatele deklarací identity do zásady.
4.  Registrace služby AD FS deklarace účet zprostředkovatele pro cestu uživatele.
5.  Odeslání zásady do Azure AD B2C tenanta a otestovat ho.

## <a name="to-create-a-claims-aware-relying-party-trust"></a>Chcete-li vytvořit s deklaracemi identity vztah důvěryhodnosti předávající strany

Použití služby AD FS jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, budete muset vytvořit AD FS vztah důvěryhodnosti předávající strany a zadejte správné parametry.

Přidat nový vztah důvěryhodnosti předávající strany pomocí modulu snap-in Správa služby AD FS a ručně nakonfigurovat nastavení, proveďte následující postup na federačním serveru.

Členství ve skupině **správci**, nebo měl srovnatelné oprávnění v místním počítači je minimálním předpokladem pro dokončení tohoto postupu. Podrobnosti o používání příslušných účtů a členství ve skupinách v [místní a doménové výchozí skupiny](http://go.microsoft.com/fwlink/?LinkId=83477)

1.  Ve Správci serveru klikněte na tlačítko **nástroje**a pak vyberte **správu služby AD FS**.

2.  Klikněte na **přidat vztah důvěryhodnosti předávající strany**.
    ![Přidat vztah důvěryhodnosti předávající strany](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-1.png)

3.  Na **úvodní** zvolte **deklarací vědět** a klikněte na tlačítko **Start**.
    ![Na úvodní stránce zvolte deklarace identit vědět](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-2.png)
4.  Na **vybrat zdroj dat** klikněte na **zadat data o předávající straně ručně**a potom klikněte na tlačítko **Další**.
    ![Zadat data o předávající straně](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-3.png)

5.  Na **zadat zobrazovaný název** stránky, zadejte název do **zobrazovaný název**v části **poznámky** zadejte popis tohoto vztahu důvěryhodnosti předávající strany a klikněte na **další** .
    ![Zadejte zobrazovaný název a poznámky](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-4.png)
6.  Volitelné. Pokud máte volitelné token šifrovací certifikát, pak na **konfigurace certifikátu** klikněte na **Procházet** vyhledejte soubor certifikátu, a potom klikněte na **Další**.
    ![Konfigurace certifikátu](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-5.png)
7.  Na **konfigurace adresy URL** stránky, vyberte **povolit podporu protokolu SAML 2.0 WebSSO** zaškrtávací políčko. V části **URL služby SAML 2.0 SSO předávající strany**, zadejte adresu URL koncového bodu služby zabezpečení kontrolního výrazu SAML (Markup Language) pro tento vztah důvěryhodnosti předávající strany a klikněte na **Další**.  Pro **URL služby SAML 2.0 SSO předávající strany**, vložte `https://(tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/{policy}`. Nahraďte názvem vašeho tenanta (například contosob2c) {klient} a {zásad} nahraďte názvem zásady pro rozšíření (například B2C_1A_TrustFrameworkExtensions).
    > [!IMPORTANT]
    >Název zásad je ten, který signup_or_signin zásad zdědí v tomto případě je: `B2C_1A_TrustFrameworkExtensions`.
    >Adresa URL může být například: https://**contosob2c**.b2clogin.com/te/**contosob2c**.onmicrosoft.com/**B2C_1A_TrustFrameworkBase**.

    ![Předávající strana adresa URL služby SAML 2.0 SSO](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-6.png)
8. Na **konfigurovat identifikátory** stránky, zadejte stejnou adresu URL jako v předchozím kroku, klikněte na tlačítko **přidat** je přidat do seznamu, a pak klikněte na **Další**.
    ![Předávající straně důvěryhodnosti identifikátory](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-7.png)
9.  Na **zvolte zásady řízení přístupu k** vyberte zásadu a klikněte na tlačítko **Další**.
    ![Zvolte zásady řízení přístupu](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-8.png)
10.  Na **připraven k přidání vztahu důvěryhodnosti** stránky, zkontrolujte nastavení a potom klikněte na **Další** předávající strany uložit informace o vztahu důvěryhodnosti.
    ![Uložit vaše informace o vztahu důvěryhodnosti předávající strany](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-9.png)
11.  Na **Dokončit** klikněte na **Zavřít**, tato akce automaticky zobrazí **upravit pravidla deklarací identity** dialogové okno.
    ![Upravit pravidla deklarace identity](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-10.png)
12. Klikněte na tlačítko **přidat pravidlo**.  
      ![Přidat nové pravidlo](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-1.png)
13.  V **šablony pravidla deklarace identity**vyberte **odesílat atributy LDAP jako deklarace identity**.
    ![Vyberte možnost odesílat atributy LDAP jako deklarace šablony](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-2.png)
14.  Zadejte **název pravidla deklarace identity**. Pro **úložiště atributů** vyberte **služby Active Directory vyberte** přidejte následující deklarace identity a pak klikněte na tlačítko **Dokončit** a **OK**.
    ![Nastavit vlastnosti pravidla](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)
15.  Ve Správci serveru vyberte **vztahy důvěryhodnosti předávající strany** pak vyberte předávající straně důvěryhodnosti jste vytvořili a klikněte na tlačítko **vlastnosti**.
    ![Předávající strana upravit vlastnosti](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-1.png)
16.  Jedním kliknutím na okno Vlastnosti vztahu důvěryhodnosti (ukázka B2C) předávající strany **podpis** kartě a klikněte na tlačítko **přidat**.  
    ![Sada podpis](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-2.png)
17.  Přidáte certifikát podpisu (bez soukromého klíče .cert souboru).  
    ![Přidání certifikátu pro podpis](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-3.png)
18.  V okně Vlastnosti vztahu důvěryhodnosti (ukázka B2C) předávající strany klikněte na tlačítko **Upřesnit** kartu a změnit **Secure hash algorithm** k **SHA-1**, klikněte na tlačítko **Ok**.  
    ![Nastavte zabezpečenou hashovací algoritmus SHA-1](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-4.png)

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>Přidat klíč aplikace účet služby AD FS do Azure AD B2C
Federace s účty služby AD FS vyžaduje tajný kód klienta pro účet služby AD FS do vztahu důvěryhodnosti Azure AD B2C jménem aplikace. Musíte se uloží váš certifikát služby AD FS ve vašem tenantovi Azure AD B2C. 

1.  Přejděte do svého tenanta Azure AD B2C a vyberte **nastavení B2C** > **architekturu rozhraní identit**
2.  Vyberte **klíče zásad** zobrazíte klíče, které jsou k dispozici ve vašem tenantovi.
3.  Klikněte na tlačítko **+ přidat**.
4.  Pro **možnosti**, použijte **nahrát**.
5.  Pro **název**, použijte `ADFSSamlCert`.  
    Předpona, která `B2C_1A_` může být automaticky přidán.
6.  V samotné nahrávání souborů ** vyberte váš soubor PFX certifikátu s privátním klíčem. Poznámka: Tento certifikát (s privátním klíčem) by měl být stejný jako ten, který vydala a použít pro přijímající strany AD FS.
![Nahrát klíče zásad](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-cert.png)
7.  Klikněte na **Vytvořit**
8.  Potvrďte, že jste vytvořili klíč `B2C_1A_ADFSSamlCert`.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Přidat zprostředkovatele deklarací identity ve svojí zásadě rozšíření
Pokud chcete uživatelům umožní přihlásit pomocí účtu služby AD FS, musíte definovat jako poskytovatele deklarací identity účtu služby AD FS. Jinými slovy budete muset zadat koncový bod, který komunikuje se službou Azure AD B2C. Koncový bod poskytuje sadu deklarací identity, které používají Azure AD B2C k ověření, že se ověřil konkrétního uživatele.

Definujte služby AD FS jako poskytovatele deklarací identity, tak, že přidáte `<ClaimsProvider>` uzlu v souboru rozšíření zásad:

1. Otevřete soubor rozšíření zásad (TrustFrameworkExtensions.xml) z pracovního adresáře. Pokud potřebujete XML editor [nabídne Visual Studio Code](https://code.visualstudio.com/download), jednoduchý editor napříč platformami.
2. Najít `<ClaimsProviders>` oddílu
3. Přidejte následující fragment kódu XML v rámci `ClaimsProviders` prvku a nahraďte `identityProvider` s DNS (libovolného určující doménou) a uložte soubor. 

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

## <a name="register-the-adfs-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>Zprostředkovatele deklarací identity účtu služby AD FS na znaménko nahoru nebo zaregistrovat v cestě uživatele
V tomto okamžiku zprostředkovatele identity se nastavila.  To však není k dispozici v některém z obrazovky přihlášení-registrace/přihlášení. Nyní je třeba přidat zprostředkovatele identity účtu služby AD FS pro vaše uživatele `SignUpOrSignIn` cesty uživatele. Chcete-li k dispozici, se nám vytvořit duplikát cesty existující uživatele šablony.  Následně můžeme ho změnit tak, aby zahrnovala zprostředkovatele identity služby AD FS:

>[!NOTE]
>Pokud jste dříve zkopírovali `<UserJourneys>` element ze základního souboru zásady do souboru rozšíření (TrustFrameworkExtensions.xml) můžete tuto část přeskočit.

1.  Otevřete soubor základní zásady (například TrustFrameworkBase.xml).
2.  Najít `<UserJourneys>` elementu a zkopírujte celý obsah `<UserJourneys>` uzlu.
3.  Otevřete soubor rozšíření (například TrustFrameworkExtensions.xml) a vyhledejte `<UserJourneys>` elementu. Pokud element neexistuje, přidejte jeden.
4.  Vložte celý obsah `<UserJournesy>` uzel, který jste zkopírovali jako podřízený objekt `<UserJourneys>` elementu.

### <a name="display-the-button"></a>Zobrazit tlačítko
`<ClaimsProviderSelections>` Element definuje seznam možnosti výběru zprostředkovatele deklarací identity a jejich pořadí.  `<ClaimsProviderSelection>` Element je obdobou k tlačítku na stránce přihlášení-registrace/přihlášení zprostředkovatele identity. Pokud chcete přidat `<ClaimsProviderSelection>` – element pro účet služby AD FS, nové tlačítko se zobrazí při uživatel umístil na stránce. Chcete-li přidat tento element:

1.  Najít `<UserJourney>` uzel, který zahrnuje `Id="SignUpOrSignIn"` v cestě uživatele, který jste zkopírovali.
2.  Vyhledejte `<OrchestrationStep>` uzel, který obsahuje `Order="1"`
3.  Přidat následující fragment kódu XML v rámci `<ClaimsProviderSelections>` uzlu:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```
### <a name="link-the-button-to-an-action"></a>Tlačítko s odkazem na akci

Teď, když máte tlačítko na místě, budete potřebovat odkázat na akci. Akce v tomto případě je pro Azure AD B2C ke komunikaci s účtem služby AD FS k získání tokenu. Tlačítko odkazu na akci propojením technický profil pro poskytovatele deklarací identity účtu služby AD FS:

1.  Najít `<OrchestrationStep>` , který obsahuje `Order="2"` v `<UserJourney>` uzlu.
2.  Přidat následující fragment kódu XML v rámci `<ClaimsExchanges>` uzlu:

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

> [!NOTE]
> * Zkontrolujte, `Id` má stejnou hodnotu jako `TargetClaimsExchangeId` v předchozí části.
> * Zajištění `TechnicalProfileReferenceId` je nastavena na technický profil vytvoříte starší (Contoso-SAML2).

## <a name="upload-the-policy-to-your-tenant"></a>Odeslání zásady do vašeho tenanta
1.  V [webu Azure portal](https://portal.azure.com), přepněte do [kontextu vašeho tenanta Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)a otevřete **Azure AD B2C** okno.
2.  Vyberte **architekturu rozhraní identit**.
3.  Otevřít **všechny zásady** okno.
4.  Vyberte **nahrát zásady**.
5.  Zkontrolujte **přepsat zásady, pokud existuje** pole.
6.  **Nahrát** TrustFrameworkExtensions.xml a ujistěte se, že se selhání ověření

## <a name="test-the-custom-policy-by-using-run-now"></a>Testování vlastní zásady pomocí možnosti spustit hned
1.  Otevřít **nastavení Azure AD B2C** a přejděte na **architekturu rozhraní identit**.
2.  Otevřít **B2C_1A_signup_signin**, předávající stranu vlastní zásady, které jste nahráli. Vyberte **spustit nyní**.
3.  Byste měli být schopni se přihlásit pomocí účtu služby AD FS.

## <a name="optional-register-the-adfs-account-claims-provider-to-profile-edit-user-journey"></a>[Volitelné] Registrace poskytovatele deklarací identity účtu služby AD FS pro cestu uživatele úpravy profilu
Můžete také přidat zprostředkovatele identity účtu služby AD FS pro vaše uživatele `ProfileEdit` cesty uživatele. Chcete-li k dispozici, jsme zopakujte poslední dva kroky:

### <a name="display-the-button"></a>Zobrazit tlačítko
1.  Otevřete soubor rozšíření zásad (například TrustFrameworkExtensions.xml).
2.  Najít `<UserJourney>` uzel, který zahrnuje `Id="ProfileEdit"` v cestě uživatele, který jste zkopírovali.
3.  Vyhledejte `<OrchestrationStep>` uzel, který obsahuje `Order="1"`
4.  Přidat následující fragment kódu XML v rámci `<ClaimsProviderSelections>` uzlu:

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```

### <a name="link-the-button-to-an-action"></a>Tlačítko s odkazem na akci
1.  Najít `<OrchestrationStep>` , který obsahuje `Order="2"` v `<UserJourney>` uzlu.
2.  Přidat následující fragment kódu XML v rámci `<ClaimsExchanges>` uzlu:

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>Pomocí možnosti spustit hned otestovat vlastní zásady úprav profilu
1.  Otevřít **nastavení Azure AD B2C** a přejděte na **architekturu rozhraní identit**.
2.  Otevřít **B2C_1A_ProfileEdit**, předávající stranu vlastní zásady, které jste nahráli. Vyberte **spustit nyní**.
3.  Byste měli být schopni se přihlásit pomocí účtu služby AD FS.

## <a name="download-the-complete-policy-files"></a>Stažení kompletní zásad souborů
Volitelné: Doporučujeme že vytvořit váš scénář s využitím vlastní vlastní zásady, které provedou soubory po dokončení, jak začít s vlastní zásady. [Zásady ukázkových souborů pouze pro referenci](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app)
