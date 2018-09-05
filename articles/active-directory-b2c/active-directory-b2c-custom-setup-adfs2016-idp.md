---
title: Přidání služby AD FS jako zprostředkovatele identity SAML pomocí vlastních zásad v Azure Active Directory B2C | Dokumentace Microsoftu
description: Nastavení služby AD FS 2016 pomocí vlastních zásad a protokol SAML v Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 2c2e6861fda42a9e8c1aabcba303bfede47ac3c1
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669222"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Přidání služby AD FS jako zprostředkovatele identity SAML pomocí vlastních zásad v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku se dozvíte, jak povolit přihlášení pro uživatelský účet služby AD FS pomocí [vlastní zásady](active-directory-b2c-overview-custom.md) v Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v [Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md) článku.

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>Přidat klíč aplikace účet služby AD FS do Azure AD B2C

Federace s účtem služby AD FS vyžaduje tajný kód klienta pro účet na vztah důvěryhodnosti Azure AD B2C jménem aplikace. Musíte se uloží váš certifikát služby AD FS ve vašem tenantovi Azure AD B2C. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Přepněte v pravém horním rohu portálu Azure Portal na adresář, který obsahuje tenanta Azure AD B2C, a ujistěte se tak, že používáte správný adresář. Vyberte **přepnout adresář**a potom vyberte adresář, který obsahuje tenanta, který jste vytvořili. V tomto kurzu *contoso* directory se používá, který obsahuje klienta s názvem *contoso0522Tenant.onmicrosoft.com*.

    ![Přepnutí adresáře](./media/active-directory-b2c-custom-setup-adfs2016-idp/switch-directories.png)

3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**. Měli byste nyní použít vašeho tenanta.
4. Na stránce s přehledem, vyberte **architekturu rozhraní identit**.
5. Vyberte **klíče zásad** zobrazit klíče, které jsou k dispozici ve vašem tenantovi, a potom klikněte na **přidat**.
6. Zvolte **nahrát** jako možnost.
7. Zadejte `ADFSSamlCert` pro název. Předpona, která `B2C_1A_` může být automaticky přidán.
8. Vyhledejte a vyberte váš soubor PFX certifikátu s privátním klíčem. Tento certifikát s privátním klíčem by měl být stejný jako ten, který byl vydán a použít pro přijímající strany AD FS.
9. Klikněte na tlačítko **vytvořit** a potvrďte, že jste vytvořili `B2C_1A_ADFSSamlCert` klíč.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Přidat zprostředkovatele deklarací identity ve svojí zásadě rozšíření

Pokud chcete uživatelům umožní přihlásit pomocí účtu služby AD FS, musíte definovat účtu jako zprostředkovatele deklarací identity. To provedete tak, že zadáte koncový bod, který komunikuje se službou Azure AD B2C. Koncový bod poskytuje sadu deklarací identity, které používají Azure AD B2C k ověření, že se ověřil konkrétního uživatele.

Definujte služby AD FS jako poskytovatele deklarací identity, tak, že přidáte **ClaimsProvider** element v souboru rozšíření zásad.

1. Otevřít *TrustFrameworkExtensions.xml* soubor zásad ve svém pracovním adresáři. Pokud potřebujete XML editor [nabídne Visual Studio Code](https://code.visualstudio.com/download), což je jednoduchý editor napříč platformami.
2. Přidejte následující kód XML v rámci **ClaimsProviders** prvku a nahraďte **vaše doména služby AD FS** v doméně služby AD FS pojmenujte a nahraďte hodnotu **identityProvider** Výstup se deklarace identity s DNS (libovolného hodnotu, která označuje, vaše doména) a soubor uložte. 

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
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
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

## <a name="register-the-claims-provider-for-sign-up-and-sign-in"></a>Registrace zprostředkovatele deklarací identity pro registraci a přihlašování

Chcete-li k dispozici na stránkách registrace a přihlášení zprostředkovatele identity účtu služby AD FS, musíte ho přidat do vaší **SignUpOrSignIn** cesty uživatele. 

Vytvořte kopii existující cestu uživatele šablony a potom ho změnit tak, že obsahují zprostředkovatele identity služby AD FS:

>[!NOTE]
>Pokud jste dříve zkopírovali **Userjourney** element ze základního souboru zásady do souboru rozšíření (*TrustFrameworkExtensions.xml*) můžete tuto část přeskočit.

1. Otevřete soubor základní zásady. Například *TrustFrameworkBase.xml*.
2. Zkopírujte celý obsah **Userjourney** elementu.
3. Otevřete soubor rozšíření (*TrustFrameworkExtensions.xml*) a vložte celý obsah **Userjourney** element, který jste zkopírovali v souboru rozšíření.

### <a name="display-the-button"></a>Zobrazit tlačítko

**ClaimsProviderSelections** element definuje seznam výběru zprostředkovatele deklarací identity a jejich pořadí.  **ClaimsProviderSelection** element je obdobou k tlačítku na stránce registrace a přihlášení zprostředkovatele identity. Pokud chcete přidat **ClaimsProviderSelection** – element pro účet služby AD FS, nové tlačítko se zobrazí, když se uživateli zobrazí na stránce. Chcete-li přidat tento element:

1. V **UserJourney** element s identifikátorem `SignUpOrSignIn` v centrech uživatele, které jste zkopírovali, vyhledejte **OrchestrationStep** prvek `Order="1"`.
2. Přidejte následující **ClaimsProviderSelection** element v rámci **ClaimsProviderSelections** element:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Tlačítko s odkazem na akci

Teď, když máte tlačítko na místě, budete potřebovat odkázat na akci. Akce v tomto případě je pro Azure AD B2C ke komunikaci s účtem služby AD FS k získání tokenu. Tlačítko odkazu na akci propojením technický profil pro poskytovatele deklarací identity účtu služby AD FS:

1. Najít **OrchestrationStep** z `Order="2"` pod **UserJourney** elementu.
2. Přidejte následující **ClaimsExchange** element v rámci **ClaimsExchanges** element:

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

> [!NOTE]
> * Ujistěte se, že `Id` má stejnou hodnotu jako `TargetClaimsExchangeId` v předchozí části.
> * Ujistěte se, že `TechnicalProfileReferenceId` je nastavena na technický profil vytvoříte starší (Contoso-SAML2).


## <a name="optional-register-the-claims-provider-for-profile-edit"></a>[Volitelné] Registrace poskytovatele deklarací identity pro úpravy profilu

Můžete také přidat zprostředkovatele identity účtu služby AD FS na vaší cestě uživatelské úpravy profilu.

### <a name="display-the-button"></a>Zobrazit tlačítko

1. Otevřete soubor rozšíření vašich zásad. Například *TrustFrameworkExtensions.xml*.
2. V **UserJourney** element s identifikátorem `ProfileEdit` v centrech uživatele, které jste zkopírovali, vyhledejte **OrchestrationStep** prvek `Order="1"`.
3. Přidejte následující **ClaimsProviderSelection** element v rámci **ClaimsProviderSelections** element:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Tlačítko s odkazem na akci

1. Najít **OrchestrationStep** z `Order="2"` pod **UserJourney** elementu.
2. Přidejte následující **ClaimsExchange** element v rámci **ClaimsExchanges** element:

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

## <a name="upload-the-policy-to-your-tenant"></a>Odeslání zásady do vašeho tenanta

1. Na webu Azure Portal, vyberte **všechny zásady**.
2. Vyberte **nahrát zásady**.
3. Povolit **přepsat zásady, pokud existuje**.
4. Vyhledejte a vyberte váš *TrustFrameworkExtensions.xml* soubor zásad a pak vyberte **nahrát**. Ujistěte se, že je ověřování úspěšné.


## <a name="configure-an-adfs-relying-party-trust"></a>Konfigurace AD FS vztah důvěryhodnosti předávající strany

Použití služby AD FS jako zprostředkovatele identity v Azure AD B2C, budete muset vytvořit AD FS vztah důvěryhodnosti předávající strany pomocí metadat SAML Azure AD B2C. Následující příklad ukazuje adresu URL metadat SAML technického profilu Azure AD B2C:

```
https://login.microsoftonline.com/te/your-tenant/your-policy/samlp/metadata?idptp=your-technical-profile
```

Nahraďte následující hodnoty:

- **váš tenant** názvem vašeho tenanta, jako je například vaše tenant.onmicrosoft.com.
- **vaše zásady** názvem zásady. Pomocí zásad, které nakonfigurujete technický profil SAML zprostředkovatele nebo zásad, která dědí z této zásadě.
- **váš technický profil** s název profilu technické poskytovatele identity SAML.
 
Otevřete prohlížeč a přejděte na adresu URL. Ujistěte se, že jste zadali správnou adresu URL a budete mít přístup k souboru XML metadat.

Přidat nový vztah důvěryhodnosti předávající strany pomocí modulu snap-in Správa služby AD FS a ručně nakonfigurovat nastavení, proveďte následující postup na federačním serveru. Členství ve skupině **správci** nebo ekvivalent v místním počítači je minimálním předpokladem pro dokončení tohoto postupu. Podrobnosti o používání příslušných účtů a členství ve skupinách v [místní a doménové výchozí skupiny](http://go.microsoft.com/fwlink/?LinkId=83477).

1. Ve Správci serveru vyberte **nástroje**a pak vyberte **správu služby AD FS**.
2. Vyberte **přidat vztah důvěryhodnosti předávající strany**.
3. Na **úvodní** zvolte **deklarací vědět**a potom klikněte na tlačítko **Start**.
4. Na **vybrat zdroj dat** stránce **importovat data o předávající straně publikování online nebo v místní síti**, zadejte adresu URL svého Azure AD B2C metadata a klikněte na **Další**.
5. Na **zadat zobrazovaný název** stránky, zadejte **zobrazovaný název**v části **poznámky**, zadejte popis tohoto vztahu důvěryhodnosti předávající strany a potom klikněte na tlačítko **Další**.
6. Na **zvolte zásady řízení přístupu k** stránky, vyberte zásadu a pak klikněte na tlačítko **Další**.
7. Na **připraven k přidání vztahu důvěryhodnosti** stránky, zkontrolujte nastavení a potom klikněte na **Další** předávající strany uložit informace o vztahu důvěryhodnosti.
8. Na **Dokončit** klikněte na **Zavřít**, tato akce automaticky zobrazí **upravit pravidla deklarací identity** dialogové okno.
9. Vyberte **přidat pravidlo**.  
10. V **šablony pravidla deklarace identity**vyberte **odesílat atributy LDAP jako deklarace identity**.
11. Zadejte **název pravidla deklarace identity**. Pro **úložiště atributů**vyberte **služby Active Directory vyberte**, přidejte následující deklarace identity a pak klikněte na tlačítko **Dokončit** a **OK**.

    ![Nastavit vlastnosti pravidla](./media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)

12.  Podle typu certifikátu, budete muset nastavit HASHOVACÍHO algoritmu. Předávající strany vztahu důvěryhodnosti (ukázka B2C) okna vlastnosti, vyberte **Upřesnit** kartu a změnit **Secure hash algorithm** k `SHA-1` nebo `SHA-256`a klikněte na tlačítko **Ok**.  

### <a name="update-the-relying-party-metadata"></a>Aktualizujte metadata předávající strany

Změna technický profil SAML vyžaduje aktualizace služby AD FS s aktualizovanými metadaty verzí. Není nutné aktualizovat metadata, když vytváříte aplikaci předávající strany, ale v případě, že změníte, aktualizace metadat služby AD FS.

1. Ve Správci serveru vyberte **nástroje**a pak vyberte **správu služby AD FS**.
2. Vyberte vztah důvěryhodnosti předávající strany jste vytvořili, vyberte **aktualizace z federačních metadat**a potom klikněte na tlačítko **aktualizace**. 

### <a name="test-the-policy-by-using-run-now"></a>Testování zásady pomocí možnosti spustit hned

1.  Otevřít **nastavení Azure AD B2C** a přejděte na **architekturu rozhraní identit**.
2.  Otevřít **B2C_1A_ProfileEdit**, předávající stranu vlastní zásady, které jste nahráli. Vyberte **spustit nyní**. Byste měli být schopni se přihlásit pomocí účtu služby AD FS.

## <a name="download-the-complete-policy-files"></a>Stažení kompletní zásad souborů

Volitelné: Můžete vytvořit váš scénář s využitím vlastních zásad pro soubory po dokončení kroků v [Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md). Například soubory, naleznete v tématu [zásad ukázkových souborů pouze pro referenci](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app).
