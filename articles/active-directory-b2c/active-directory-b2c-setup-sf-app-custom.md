---
title: 'Azure Active Directory B2C: Přidání poskytovatele služby Salesforce SAML pomocí vlastních zásad | Microsoft Docs'
description: Další informace o tom, jak vytvořit a spravovat vlastní zásady Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 06/11/2017
ms.author: davidmu
ms.openlocfilehash: 1ccf5c58eab9df9016224a91ddda952a05457e2a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-sign-in-by-using-salesforce-accounts-via-saml"></a>Azure Active Directory B2C: Přihlaste se pomocí účtů služby Salesforce pomocí SAML

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku se dozvíte, jak používat [vlastní zásady](active-directory-b2c-overview-custom.md) nastavení přihlášení pro uživatele z konkrétní organizace služby Salesforce.

## <a name="prerequisites"></a>Požadavky

### <a name="azure-ad-b2c-setup"></a>Instalace nástroje Azure AD B2C

Ujistěte se, že jste dokončili všechny kroky, které ukazují, jak k [začít pracovat s vlastními zásadami](active-directory-b2c-get-started-custom.md) v Azure Active Directory B2C (Azure AD B2C).

Mezi ně patří:

* Vytvoření klienta Azure AD B2C.
* Vytvoření aplikace Azure AD B2C.
* Zaregistrujte dvě aplikace modul zásad.
* Nastavení klíčů.
* Nastavte starter pack.

### <a name="salesforce-setup"></a>Instalační program služby Salesforce

V tomto článku předpokládáme, že jste již dokončili následující:

* Registraci účtu Salesforce. Můžete si zaregistrovat [bezplatný účet Developer Edition](https://developer.salesforce.com/signup).
* [Nastavit Moje domény](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) pro vaši organizaci Salesforce.

## <a name="set-up-salesforce-so-users-can-federate"></a>Nastavení Salesforce, aby uživatelé mohli Federovat

Pomoc při komunikaci s Salesforce Azure AD B2C, musíte získat adresu URL metadat služby Salesforce.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Nastavení Salesforce jako zprostředkovatel identity

> [!NOTE]
> V tomto článku předpokládáme, že používáte [Salesforce Lightning prostředí](https://developer.salesforce.com/page/Lightning_Experience_FAQ).

1. [Přihlaste se k Salesforce](https://login.salesforce.com/). 
2. V nabídce vlevo pod **nastavení**, rozbalte položku **Identity**a potom klikněte na **zprostředkovatele Identity**.
3. Klikněte na tlačítko **povolit zprostředkovatele Identity**.
4. V části **vyberte certifikát**, vyberte certifikát, který chcete podporu Salesforce se používají ke komunikaci s Azure AD B2C. (Můžete použít výchozí certifikát.) Klikněte na **Uložit**. 

### <a name="create-a-connected-app-in-salesforce"></a>Vytvoření připojené aplikaci v Salesforce

1. Na **zprostředkovatele Identity** stránky, přejděte na **poskytovatelé služeb**.
2. Klikněte na tlačítko **poskytovatelé služeb jsou teď vytvořené prostřednictvím připojené aplikace. Kliknutím sem.**
3. V části **základní informace**, zadejte požadované hodnoty pro připojené aplikaci.
4. V části **nastavení webové aplikace**, vyberte **povolit SAML** zaškrtávací políčko.
5. V **Entity ID** pole, zadejte následující adresu URL. Ujistěte se, že nahradíte hodnotu `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```
6. V **adresa URL služby ACS** pole, zadejte následující adresu URL. Ujistěte se, že nahradíte hodnotu `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Ponechte výchozí hodnoty pro všechna ostatní nastavení.
8. Přejděte do dolní části seznamu a pak klikněte na tlačítko **Uložit**.

### <a name="get-the-metadata-url"></a>Získat adresu URL metadat

1. Na stránce Přehled připojené aplikaci klikněte na **spravovat**.
2. Zkopírujte hodnotu pro **koncový bod zjišťování metadat**a pak ho uložte. Použijete ho později v tomto článku.

### <a name="set-up-salesforce-users-to-federate"></a>Nastavení Salesforce uživatelů pro vytvoření federace

1. Na **spravovat** stránku vaší připojené aplikaci, přejděte na **profily**.
2. Klikněte na tlačítko **spravovat profily**.
3. Vyberte profily (nebo skupiny uživatelů), kterou chcete vytvořit federaci s Azure AD B2C. Jako správce systému, vyberte **správce systému** zaškrtnutí políčka, takže můžete vytvořit federaci s použitím účtu Salesforce.

## <a name="generate-a-signing-certificate-for-azure-ad-b2c"></a>Generovat podpisový certifikát pro Azure AD B2C

Požadavky odeslané do služby Salesforce musí být podepsány pomocí Azure AD B2C. Pokud chcete vygenerovat podpisový certifikát, otevřete prostředí Azure PowerShell a spusťte následující příkazy.

> [!NOTE]
> Zajistěte, aby aktualizovat název klienta a heslo v horních dvou řádcích.

```PowerShell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="add-the-saml-signing-certificate-to-azure-ad-b2c"></a>Přidání podpisového certifikátu SAML do Azure AD B2C

Podpisový certifikát odešlete ke klientovi Azure AD B2C: 

1. Přejděte ke klientovi Azure AD B2C. Klikněte na tlačítko **nastavení** > **Identity rozhraní Framework** > **zásad klíče**.
2. Klikněte na tlačítko **+ přidat**a pak:
    1. Klikněte na tlačítko **možnosti** > **nahrát**.
    2. Zadejte **název** (například SAMLSigningCert). Předpona *B2C_1A_* se automaticky přidá k názvu klíče.
    3. Chcete-li vybrat certifikát, vyberte **nahrát ovládacího prvku se souborovým**. 
    4. Zadejte heslo k certifikátu, který nastavíte ve skriptu prostředí PowerShell.
3. Klikněte na možnost **Vytvořit**.
4. Ověřte, že jste vytvořili klíč (například B2C_1A_SAMLSigningCert). Poznamenejte si úplný název (včetně *B2C_1A_*). Je bude odkazovat na tento klíč později v zásadách.

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>Vytvoření zprostředkovatele deklarací identity Salesforce SAML v základní zásady

Je třeba definovat Salesforce jako poskytovatele deklarací identity, takže uživatelům se můžete přihlásit pomocí služby Salesforce. Jinými slovy budete muset zadat koncový bod, který Azure AD B2C bude komunikovat se službou. Koncový bod se *poskytují* sadu *deklarace identity* využívající Azure AD B2C, chcete-li ověřit, že byl ověřen konkrétního uživatele. Chcete-li to provést, přidejte `<ClaimsProvider>` pro služby Salesforce v souboru rozšíření zásad:

1. V pracovním adresáři otevřete soubor rozšíření (TrustFrameworkExtensions.xml).
2. Najít `<ClaimsProviders>` části. Pokud neexistuje, vytvořte ho do kořenového uzlu.
3. Přidejte nový `<ClaimsProvider>`:

    ```XML
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="externalIdp"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
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

V části `<ClaimsProvider>` uzlu:

1. Změňte hodnotu `<Domain>` na jedinečnou hodnotu, která rozlišuje `<ClaimsProvider>` od jiných poskytovatelů identit.
2. Aktualizujte hodnotu pro `<DisplayName>` na název zobrazení pro zprostředkovatele deklarací identity. Tato hodnota není v současné době používá.

### <a name="update-the-technical-profile"></a>Technické profil aktualizovat.

Chcete-li získat token SAML ze služby Salesforce, definujte protokolů, které Azure AD B2C bude používat pro komunikaci se službou Azure Active Directory (Azure AD). K tomu `<TechnicalProfile>` element `<ClaimsProvider>`:

1. Aktualizovat ID `<TechnicalProfile>` uzlu. Toto ID použité k odkazování na tento profil technické z dalších částí zásad.
2. Aktualizujte hodnotu pro `<DisplayName>`. Tato hodnota se zobrazí na tlačítko přihlásit na přihlašovací stránku.
3. Aktualizujte hodnotu pro `<Description>`.
4. Salesforce používá protokol SAML 2.0. Ujistěte se, že hodnota `<Protocol>` je **typu SAML2**.

Aktualizace `<Metadata>` část v předchozím XML tak, aby odrážela nastavení pro konkrétní účtu Salesforce. V souboru XML aktualizujte metadata hodnoty:

1. Aktualizujte hodnotu `<Item Key="PartnerEntity">` s adresou URL metadat Salesforce jste zkopírovali dříve. Má následující formát: 

    `https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp/connectedapp.xml`

2. V `<CryptographicKeys>` část, aktualizujte hodnotu v obou instancí `StorageReferenceId` k názvu klíče podpisového certifikátu (například B2C_1A_SAMLSigningCert).

### <a name="upload-the-extension-file-for-verification"></a>Nahrát soubor rozšíření pro ověření

Vaše zásada je nyní nakonfigurován tak, aby Azure AD B2C umí ke komunikaci s Salesforce. Zkuste odeslat soubor rozšíření zásad, chcete-li ověřit, že nejsou k dispozici všechny problémy, pokud. Nahrát soubor rozšíření zásad:

1. Ve vašem klientu Azure AD B2C, přejděte na **všechny zásady** okno.
2. Vyberte **přepsat zásady, pokud existuje** zaškrtávací políčko.
3. Nahrajte soubor rozšíření (TrustFrameworkExtensions.xml). Ujistěte se, že neselže ověření.

## <a name="register-the-salesforce-saml-claims-provider-to-a-user-journey"></a>Zaregistrujte zprostředkovatele deklarací identity Salesforce SAML k cesty uživatele

Dál přidejte poskytovatele identity Salesforce SAML na jednu z vaší uživatelské cesty. V tomto okamžiku byla nastavena zprostředkovatele identity, ale není k dispozici na všech stránkách registrace nebo přihlášení uživatele. Pro přidání poskytovatele identity na přihlašovací stránku, nejprve vytvořte duplicitní existující uživatele cesty šablony. Potom upravte šablonu tak, aby je také poskytovatele identit Azure AD.

1. Otevřete soubor základní zásad (například TrustFrameworkBase.xml).
2. Najít `<UserJourneys>` elementu a zkopírujte celou `<UserJourney>` hodnoty, včetně Id = "SignUpOrSignIn".
3. Otevřete soubor rozšíření (například TrustFrameworkExtensions.xml). Najít `<UserJourneys>` elementu. Pokud element neexistuje, vytvořte ji.
4. Vložte zkopírovali celý `<UserJourney>` jako podřízenou `<UserJourneys>` elementu.
5. Přejmenujte ID nové `<UserJourney>` (například SignUpOrSignUsingContoso).

### <a name="display-the-identity-provider-button"></a>Zobrazení tlačítka zprostředkovatele identity

`<ClaimsProviderSelection>` Element je obdobou tlačítko zprostředkovatele identity na stránce registrace nebo přihlášení. Přidáním `<ClaimsProviderSelection>` element pro Salesforce nové tlačítko se zobrazí, když uživatel přejde na této stránce. Chcete-li zobrazit tlačítko zprostředkovatele identity:

1. V `<UserJourney>` , kterou jste vytvořili, vyhledejte `<OrchestrationStep>` s `Order="1"`.
2. Přidejte následující kód XML:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

3. Nastavit `TargetClaimsExchangeId` na logickou hodnotu. Doporučujeme následující stejné konvence jako jiné (například  *\[ClaimProviderName\]Exchange*).

### <a name="link-the-identity-provider-button-to-an-action"></a>Tlačítko zprostředkovatele identity propojit akce

Nyní když máte tlačítko zprostředkovatele identity na místě, propojte akce. V takovém případě je akce pro Azure AD B2C ke komunikaci s Salesforce přijímat tokenu SAML. To provedete pomocí propojení technické profil pro vaše Salesforce SAML poskytovatele deklarací identity:

1. V `<UserJourney>` uzlu najít `<OrchestrationStep>` s `Order="2"`.
2. Přidejte následující kód XML:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

3. Aktualizace `Id` na stejnou hodnotu, který jste použili předtím pro `TargetClaimsExchangeId`.
4. Aktualizace `TechnicalProfileReferenceId` k `Id` technických profilu, kterou jste vytvořili dříve (například ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>Nahrát soubor aktualizované rozšíření

Dokončení úpravy souboru rozšíření. Uložte a nahrajte tento soubor. Ujistěte se, že všechny ověření úspěšné.

### <a name="update-the-relying-party-file"></a>Aktualizace souboru předávající strany

Potom aktualizujte soubor předávající stranu, který iniciuje cesty uživatele, který jste vytvořili:

1. Vytvořte kopii SignUpOrSignIn.xml v pracovní adresář. Potom přejmenujte ji (například SignUpOrSignInWithAAD.xml).
2. Otevřete nový soubor a aktualizace `PolicyId` atribut pro `<TrustFrameworkPolicy>` s jedinečnou hodnotu. Toto je název vaší zásady (například SignUpOrSignInWithAAD).
3. Změnit `ReferenceId` atribut `<DefaultUserJourney>` tak, aby odpovídala `Id` nové cesty uživatele, který jste vytvořili (například SignUpOrSignUsingContoso).
4. Uložte změny a potom soubor odešlete.

## <a name="test-and-troubleshoot"></a>Testování a řešení potíží

K testování vlastních zásad, které jste právě nahráli, na portálu Azure, přejděte do okna zásady a pak klikněte na tlačítko **spustit nyní**. Pokud se nezdaří, najdete v části [řešení potíží se zásadami vlastní](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Další postup

Poskytnutí zpětné vazby k [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
