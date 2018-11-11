---
title: Přidání služby AD FS jako zprostředkovatele identity SAML pomocí vlastních zásad v Azure Active Directory B2C | Dokumentace Microsoftu
description: Nastavení služby AD FS 2016 pomocí vlastních zásad a protokol SAML v Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/05/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1def5686933a971b1192ec58bc72d64cbc5e8931
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219320"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Přidání služby AD FS jako zprostředkovatele identity SAML pomocí vlastních zásad v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku se dozvíte, jak povolit přihlášení pro uživatelský účet služby AD FS pomocí [vlastní zásady](active-directory-b2c-overview-custom.md) v Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Požadavky

- Proveďte kroky v [začít pracovat s vlastními zásadami v Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).
- Ujistěte se, že máte přístup k souboru PFX certifikátu s privátním klíčem, který byl vydán služby AD FS.

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Musíte se uloží váš certifikát služby AD FS ve vašem tenantovi Azure AD B2C.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
4. Na stránce s přehledem, vyberte **architekturu rozhraní identit - PREVIEW**.
5. Vyberte **klíče zásad** a pak vyberte **přidat**.
6. Pro **možnosti**, zvolte `Upload`.
7. Zadejte **název** klíče zásad. Například, `ADFSSamlCert`. Předpona, která `B2C_1A_` je automaticky přidán do názvu klíče.
8. Vyhledejte a vyberte váš soubor PFX certifikátu s privátním klíčem.
9. Klikněte na možnost **Vytvořit**.

## <a name="add-a-claims-provider"></a>Přidat zprostředkovatele deklarací identity

Pokud chcete uživatelům umožní přihlásit pomocí účtu služby AD FS, musíte definovat účtu jako zprostředkovatele deklarací identity, který Azure AD B2C můžou klienti komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací identity, které používají Azure AD B2C k ověření, že se ověřil konkrétního uživatele. 

Účet služby AD FS můžete definovat jako poskytovatele deklarací identity tak, že ji přidáte **ClaimsProviders** prvku v souboru rozšíření vašich zásad.

1. Otevřít *TrustFrameworkExtensions.xml*.
2. Najít **ClaimsProviders** elementu. Pokud neexistuje, přidejte jej pod kořenovým elementem.
3. Přidat nový **ClaimsProvider** následujícím způsobem:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso ADFS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso ADFS</DisplayName>
          <Description>Login with your ADFS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
            <Item Key=" XmlSignatureAlgorithm">Sha256</Item>
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

4. Nahraďte `your-ADFS-domain` s názvem domény služby AD FS a nahraďte hodnotu **identityProvider** výstupní deklaraci identity serveru DNS (libovolného hodnotu, která označuje, vaše doména).
5. Uložte soubor.

### <a name="upload-the-extension-file-for-verification"></a>Nahrát soubor rozšíření pro ověřování

Nyní jste nakonfigurovali zásady tak, aby Azure AD B2C ví, jak komunikovat s účtem služby AD FS. Zkuste nahrát soubor rozšíření zásady jenom k potvrzení, že všechny problémy nemusí zatím.

1. Na **vlastní zásady** stránky ve vašem tenantovi Azure AD B2C, vyberte **nahrát zásady**.
2. Povolit **přepsat zásady, pokud existuje**a poté vyhledejte a vyberte *TrustFrameworkExtensions.xml* souboru.
3. Klikněte na **Odeslat**.

## <a name="register-the-claims-provider"></a>Registrace zprostředkovatele deklarací identity

V tuto chvíli je nastavený zprostředkovatele identity, ale není k dispozici v některém z obrazovky registrace nebo přihlášení. Chcete-li k dispozici, vytvoření duplicitní cesty existující uživatele šablony a upravte ho tak, aby má také zprostředkovatele identity služby AD FS.

1. Otevřít *TrustFrameworkBase.xml* soubor z starter pack.
2. Vyhledejte a zkopírujte celý obsah **UserJourney** element, který zahrnuje `Id="SignUpOrSignIn"`.
3. Otevřít *TrustFrameworkExtensions.xml* a najít **Userjourney** elementu. Pokud element neexistuje, přidejte jeden.
4. Vložte celý obsah **UserJourney** element, který jste zkopírovali jako podřízený objekt **Userjourney** elementu.
5. Přejmenujte ID cesty uživatele. Například, `SignUpSignInADFS`.

### <a name="display-the-button"></a>Zobrazit tlačítko

**ClaimsProviderSelection** element je obdobou k tlačítku na obrazovce registrace nebo přihlášení zprostředkovatele identity. Pokud chcete přidat **ClaimsProviderSelection** – element pro účet služby AD FS, nové tlačítko se zobrazí při uživatel umístil na stránce.

1. Najít **OrchestrationStep** element, který zahrnuje `Order="1"` v cestě uživatele, který jste vytvořili.
2. V části **ClaimsProviderSelections**, přidejte následující prvek. Nastavte hodnotu **TargetClaimsExchangeId** na odpovídající hodnotu, například `ContosoExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Tlačítko s odkazem na akci

Teď, když máte tlačítko na místě, budete potřebovat odkázat na akci. Akce v tomto případě je pro Azure AD B2C ke komunikaci s účtem služby AD FS k získání tokenu.

1. Najít **OrchestrationStep** , který obsahuje `Order="2"` v cestě uživatele.
2. Přidejte následující **ClaimsExchange** a ujistěte se, že používáte stejnou hodnotu pro element **Id** , který jste použili pro **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```
    
    Aktualizujte hodnotu **TechnicalProfileReferenceId** k **Id** technického profilu, který jste vytvořili dříve. Například, `Contoso-SAML2`.

3. Uložit *TrustFrameworkExtensions.xml* souboru a nahrajte ji znovu pro ověření.


## <a name="configure-an-adfs-relying-party-trust"></a>Konfigurace AD FS vztah důvěryhodnosti předávající strany

Použití služby AD FS jako zprostředkovatele identity v Azure AD B2C, budete muset vytvořit AD FS vztah důvěryhodnosti předávající strany pomocí metadat SAML Azure AD B2C. Následující příklad ukazuje adresu URL metadat SAML technického profilu Azure AD B2C:

```
https://login.microsoftonline.com/te/your-tenant/your-policy/samlp/metadata?idptp=your-technical-profile
```

Nahraďte následující hodnoty:

- **váš tenant** názvem vašeho tenanta, jako je například vaše tenant.onmicrosoft.com.
- **vaše zásady** názvem zásady. Například B2C_1A_signup_signin_adfs.
- **váš technický profil** s název profilu technické poskytovatele identity SAML. Například Contoso-SAML2.
 
Otevřete prohlížeč a přejděte na adresu URL. Ujistěte se, že jste zadali správnou adresu URL a budete mít přístup k souboru XML metadat. Přidat nový vztah důvěryhodnosti předávající strany pomocí modulu snap-in Správa služby AD FS a ručně nakonfigurovat nastavení, proveďte následující postup na federačním serveru. Členství ve skupině **správci** nebo ekvivalent v místním počítači je minimálním předpokladem pro dokončení tohoto postupu.

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

    | LDAP attrubute | Typ odchozí deklarace |
    | -------------- | ------------------- |
    | Uživatel Principal-Name | userPricipalName |
    | příjmení | family_name |
    | Zadaný název | given_name |
    | E-mailovou adresu | e-mail |
    | Zobrazovaný název | jméno |
    
12.  Podle typu certifikátu, budete muset nastavit HASHOVACÍHO algoritmu. Předávající strany vztahu důvěryhodnosti (ukázka B2C) okna vlastnosti, vyberte **Upřesnit** kartu a změnit **Secure hash algorithm** k `SHA-1` nebo `SHA-256`a klikněte na tlačítko **Ok**.  
13. Ve Správci serveru vyberte **nástroje**a pak vyberte **správu služby AD FS**.
14. Vyberte vztah důvěryhodnosti předávající strany jste vytvořili, vyberte **aktualizace z federačních metadat**a potom klikněte na tlačítko **aktualizace**. 

## <a name="create-an-azure-ad-b2c-application"></a>Vytvoření aplikace Azure AD B2C

Probíhá komunikace s Azure AD B2c prostřednictvím aplikace, kterou vytvoříte ve vašem tenantovi. Tato část obsahuje seznam volitelné kroky, které můžete použít k vytvoření aplikace testů, pokud jste tak již neučinili.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **aplikací**a pak vyberte **přidat**.
5. Zadejte název aplikace, například *testapp1*.
6. Pro **webová aplikace / webové rozhraní API**vyberte `Yes`a pak zadejte `https://jwt.ms` pro **adresy URL odpovědi**.
7. Klikněte na možnost **Vytvořit**.

### <a name="update-and-test-the-relying-party-file"></a>Aktualizace a předávající strany soubor testu

Aktualizujte předávající stranu soubor, který iniciuje cesty uživatele, který jste vytvořili.

1. Vytvořte kopii *SignUpOrSignIn.xml* ve svém pracovním adresáři a přejmenujte jej. Například přejmenujte ho na *SignUpSignInADFS.xml*.
2. Otevřete nový soubor a aktualizujte hodnotu **PolicyId** atributu **TrustFrameworkPolicy** s jedinečnou hodnotu. Například, `SignUpSignInADFS`.
3. Aktualizujte hodnotu **PublicPolicyUri** s identifikátorem URI pro zásady. Například "http://contoso.com/B2C_1A_signup_signin_adfs" >
4. Aktualizujte hodnotu **ReferenceId** atribut **DefaultUserJourney** tak, aby odpovídaly ID nové cesty uživatele, který jste vytvořili (SignUpSignInADFS).
5. Uložte provedené změny, nahrajte soubor a pak v seznamu vyberte novou zásadu.
6. Ujistěte se, že je vybraná aplikaci Azure AD B2C, kterou jste vytvořili v **vyberte aplikaci** pole a pak ho otestujte kliknutím **spustit nyní**.

