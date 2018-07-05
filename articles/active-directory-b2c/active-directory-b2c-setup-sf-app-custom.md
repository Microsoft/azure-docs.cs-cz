---
title: Přidání zprostředkovatele Salesforce SAML pomocí vlastních zásad v Azure Active Directory B2C | Dokumentace Microsoftu
description: Další informace o tom, jak vytvářet a spravovat vlastní zásady pro Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/11/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1307fc455cacde81cb25ad58c5e99df21f126568
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448250"
---
# <a name="azure-active-directory-b2c-sign-in-by-using-salesforce-accounts-via-saml"></a>Azure Active Directory B2C: Přihlaste se pomocí účtů Salesforce přes SAML

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku se dozvíte, jak používat [vlastní zásady](active-directory-b2c-overview-custom.md) nastavení přihlášení pro uživatele z konkrétní organizace Salesforce.

## <a name="prerequisites"></a>Požadavky

### <a name="azure-ad-b2c-setup"></a>Instalace nástroje Azure AD B2C

Ujistěte se, že jste dokončili všechny kroky, které ukazují, jak k [začít pracovat s vlastními zásadami](active-directory-b2c-get-started-custom.md) v Azure Active Directory B2C (Azure AD B2C).

Mezi ně patří:

* Vytvoření tenanta Azure AD B2C.
* Vytvoření aplikace Azure AD B2C.
* Dvě aplikace modul zásad zaregistrujte.
* Nastavení klíče.
* Nastavte si úvodní balíček.

### <a name="salesforce-setup"></a>Salesforce – nastavení

V tomto článku jsme se předpokládá, že jste už provedli následující:

* Registraci účtu Salesforce. Můžete si zaregistrovat [bezplatný účet Developer Edition](https://developer.salesforce.com/signup).
* [Nastavit Moje doména](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) pro vaši organizaci Salesforce.

## <a name="set-up-salesforce-so-users-can-federate"></a>Nastavení Salesforce tak může provést federaci uživatelů

Pokud chcete pomoci při komunikaci se Salesforce Azure AD B2C, budete muset získat adresu URL metadat služby Salesforce.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Nastavení Salesforce jako zprostředkovatele identity

> [!NOTE]
> V tomto článku, předpokládáme, že používáte [Salesforce bleskově prostředí](https://developer.salesforce.com/page/Lightning_Experience_FAQ).

1. [Přihlaste se k Salesforce](https://login.salesforce.com/). 
2. V nabídce vlevo v části **nastavení**, rozbalte **Identity**a potom klikněte na tlačítko **zprostředkovatele Identity**.
3. Klikněte na tlačítko **povolit zprostředkovatele Identity**.
4. V části **vyberte certifikát, který**, vyberte certifikát, který chcete, aby Salesforce ke komunikaci s Azure AD B2C. (Můžete použít výchozí certifikát.) Klikněte na **Uložit**. 

### <a name="create-a-connected-app-in-salesforce"></a>Vytvoření propojené aplikace v Salesforce.

1. Na **zprostředkovatele Identity** stránky, přejděte na **poskytovatelů služeb**.
2. Klikněte na tlačítko **poskytovatelů služeb jsou teď vytvořených prostřednictvím aplikace připojené. Kliknutím sem.**
3. V části **základní informace**, zadejte požadované hodnoty pro připojené aplikace.
4. V části **nastavení webové aplikace**, vyberte **povolit SAML** zaškrtávací políčko.
5. V **Entity ID** pole, zadejte následující adresu URL. Ujistěte se, že nahradíte hodnotu pro `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```
6. V **ACS URL** pole, zadejte následující adresu URL. Ujistěte se, že nahradíte hodnotu pro `tenantName`.
      ```
      https://login.microsoftonline.com/te/tenantName.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Ponechte výchozí hodnoty pro všechna ostatní nastavení.
8. Posunout na konec seznamu a potom klikněte na tlačítko **Uložit**.

### <a name="get-the-metadata-url"></a>Získat adresu URL metadat

1. Na stránce přehled připojených aplikací, klikněte na tlačítko **spravovat**.
2. Zkopírujte hodnotu **koncový bod zjišťování metadat**a pak ho uložte. Použijete ho později v tomto článku.

### <a name="set-up-salesforce-users-to-federate"></a>Nastavení uživatelů Salesforce pro vytvoření federace

1. Na **spravovat** stránce vašich připojených aplikací, přejděte na **profily**.
2. Klikněte na tlačítko **spravovat profily**.
3. Vyberte profily (nebo skupiny uživatelů), kterou chcete Federovat s Azure AD B2C. Jako správce systému, vyberte **správce systému** zaškrtnutí políčka, takže může provést federaci s použitím účtu Salesforce.

## <a name="generate-a-signing-certificate-for-azure-ad-b2c"></a>Generování podpisového certifikátu pro Azure AD B2C

Požadavky odeslané na Salesforce, musíte být přihlášení pomocí Azure AD B2C. Generování podpisového certifikátu, otevřete prostředí Azure PowerShell a spusťte následující příkazy.

> [!NOTE]
> Ujistěte se, že aktualizujete název tenanta a heslo v horní dva řádky.

```PowerShell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="add-the-saml-signing-certificate-to-azure-ad-b2c"></a>Přidat podpisový certifikát SAML do Azure AD B2C

Nahrání podpisového certifikátu do vašeho tenanta Azure AD B2C: 

1. Přejděte do svého tenanta Azure AD B2C. Klikněte na tlačítko **nastavení** > **architekturu rozhraní identit** > **klíče zásad**.
2. Klikněte na tlačítko **+ přidat**a pak:
    1. Klikněte na tlačítko **možnosti** > **nahrát**.
    2. Zadejte **název** (například SAMLSigningCert). Předpona, která *B2C_1A_* se automaticky přidá k názvu klíče.
    3. Chcete-li vybrat certifikát, vyberte **nahrát ovládací prvek souboru**. 
    4. Zadejte heslo k certifikátu, kterou jste nastavili v skriptu prostředí PowerShell.
3. Klikněte na možnost **Vytvořit**.
4. Ověřte, že jste vytvořili klíč (například B2C_1A_SAMLSigningCert). Poznamenejte si úplný název (včetně *B2C_1A_*). Bude odkazovat tento klíč později v zásadách.

## <a name="create-the-salesforce-saml-claims-provider-in-your-base-policy"></a>Vytvoření zprostředkovatele deklarací identity Salesforce SAML v základních zásadách

Budete muset definovat Salesforce jako poskytovatele deklarací identity, takže uživatel může přihlásit pomocí služby Salesforce. Jinými slovy budete muset zadat koncový bod, který Azure AD B2C bude komunikovat s. Koncový bod bude *poskytují* sadu *deklarace identity* , který Azure AD B2C používá k ověření, že se ověřil konkrétního uživatele. Chcete-li to provést, přidejte `<ClaimsProvider>` pro Salesforce v souboru rozšíření zásady:

1. V pracovním adresáři otevřete soubor rozšíření (TrustFrameworkExtensions.xml).
2. Najít `<ClaimsProviders>` oddílu. Pokud neexistuje, vytvořte ho do kořenového uzlu.
3. Přidat nový `<ClaimsProvider>`:

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

1. Změňte hodnotu `<Domain>` na jedinečnou hodnotu, která odlišuje `<ClaimsProvider>` od jiných zprostředkovatelů identity.
2. Aktualizujte hodnotu `<DisplayName>` na zobrazovaný název zprostředkovatele deklarací identity. Tato hodnota se v současné době nepoužívá.

### <a name="update-the-technical-profile"></a>Aktualizace technický profil

Chcete-li získat token SAML ze služby Salesforce, definujte protokoly, které Azure AD B2C bude používat pro komunikaci se službou Azure Active Directory (Azure AD). K tomu `<TechnicalProfile>` prvek `<ClaimsProvider>`:

1. Aktualizace ID `<TechnicalProfile>` uzlu. Toto ID se používá k odkazování na tento technický profil z jiných částí zásad.
2. Aktualizujte hodnotu pro `<DisplayName>`. Tato hodnota se zobrazí na tlačítko přihlásit na přihlašovací stránku.
3. Aktualizujte hodnotu pro `<Description>`.
4. Salesforce používá protokol SAML 2.0. Ujistěte se, že hodnota `<Protocol>` je **SAML2**.

Aktualizace `<Metadata>` části v předcházející XML tak, aby odrážely nastavení pro konkrétní účtu Salesforce. V souboru XML aktualizujte hodnoty metadat:

1. Aktualizujte hodnotu `<Item Key="PartnerEntity">` s adresou URL metadat Salesforce jste si zkopírovali dříve. Má následující formát: 

    `https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp/connectedapp.xml`

2. V `<CryptographicKeys>` části, aktualizujte hodnotu pro oba výskyty `StorageReferenceId` k názvu klíče (například B2C_1A_SAMLSigningCert) podpisového certifikátu.

### <a name="upload-the-extension-file-for-verification"></a>Nahrát soubor rozšíření pro ověřování

Vaše zásada je nyní nakonfigurován tak, aby Azure AD B2C ví, jak komunikaci se Salesforce. Zkuste nahrát soubor rozšíření zásady, chcete-li ověřit, že nejsou k dispozici nějaké problémy, pokud. Nahrání souboru rozšíření zásady:

1. Ve vašem tenantovi Azure AD B2C, přejděte **všechny zásady** okno.
2. Vyberte **přepsat zásady, pokud existuje** zaškrtávací políčko.
3. Nahrajte soubor rozšíření (TrustFrameworkExtensions.xml). Ujistěte se, že nebude neúspěšné ověření.

## <a name="register-the-salesforce-saml-claims-provider-to-a-user-journey"></a>Registrace poskytovatele deklarací identity Salesforce SAML do cesty uživatele

V dalším kroku přidáte zprostředkovatele identity Salesforce SAML do jednoho z vaší cesty uživatele. V tomto okamžiku je nastavený zprostředkovatele identity, ale není k dispozici na všech stránkách registrace / přihlášení uživatele. Přidat zprostředkovatele identity na přihlašovací stránku, nejprve vytvořte duplicitní existující cestu uživatele šablony. Potom upravte šablonu tak, aby byly také zprostředkovatele identity Azure AD.

1. Otevřete soubor základní zásady (například TrustFrameworkBase.xml).
2. Najít `<UserJourneys>` elementu a zkopírujte celý `<UserJourney>` hodnotu, včetně Id = "SignUpOrSignIn".
3. Otevřete soubor rozšíření (například TrustFrameworkExtensions.xml). Najít `<UserJourneys>` elementu. Pokud element neexistuje, vytvořte ji.
4. Vložte zkopírovat celý `<UserJourney>` jako podřízený objekt `<UserJourneys>` elementu.
5. Přejmenovat Identifikátor nového `<UserJourney>` (například SignUpOrSignUsingContoso).

### <a name="display-the-identity-provider-button"></a>Zobrazit tlačítko zprostředkovatele identity

`<ClaimsProviderSelection>` Element je obdobou k tlačítku na stránce registrace nebo přihlášení zprostředkovatele identity. Tak, že přidáte `<ClaimsProviderSelection>` – element pro Salesforce, nové tlačítko se zobrazí, když uživatel přejde na tuto stránku. Chcete-li zobrazit tlačítko zprostředkovatele identity:

1. V `<UserJourney>` , kterou jste vytvořili, vyhledejte `<OrchestrationStep>` s `Order="1"`.
2. Přidejte následující kód XML:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

3. Nastavte `TargetClaimsExchangeId` na logickou hodnotu. Doporučujeme stejnou konvencí jako jiné (například  *\[ClaimProviderName\]Exchange*).

### <a name="link-the-identity-provider-button-to-an-action"></a>Odkaz na akci tlačítko zprostředkovatele identity

Teď, když máte tlačítko zprostředkovatele identity na místě, propojte akci. Akce v tomto případě je pro Azure AD B2C ke komunikaci se Salesforce pro získání tokenu SAML. Uděláte to tak propojování technický profil SAML vaše Salesforce zprostředkovatele deklarací identity:

1. V `<UserJourney>` uzlu, vyhledejte `<OrchestrationStep>` s `Order="2"`.
2. Přidejte následující kód XML:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

3. Aktualizace `Id` na stejnou hodnotu, která jste předtím použili pro `TargetClaimsExchangeId`.
4. Aktualizace `TechnicalProfileReferenceId` k `Id` technických profilu, kterou jste vytvořili dříve (například ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>Nahrát soubor aktualizované rozšíření

Dokončení úpravy souboru rozšíření. Uložte a odešlete tento soubor. Ujistěte se, že všechny ověření úspěšné.

### <a name="update-the-relying-party-file"></a>Aktualizace souboru předávající strany

V dalším kroku aktualizujte předávající stranu soubor, který iniciuje cesty uživatele, který jste vytvořili:

1. Vytvořte kopii SignUpOrSignIn.xml ve svém pracovním adresáři. Přejmenujte (například SignUpOrSignInWithAAD.xml).
2. Otevřete nový soubor a aktualizace `PolicyId` atributu `<TrustFrameworkPolicy>` s jedinečnou hodnotu. Toto je název zásady (například SignUpOrSignInWithAAD).
3. Upravit `ReferenceId` atribut `<DefaultUserJourney>` tak, aby odpovídaly `Id` nové cesty uživatele, který jste vytvořili (například SignUpOrSignUsingContoso).
4. Uložte změny a pak nahrajte soubor.

## <a name="test-and-troubleshoot"></a>Testování a odstraňování potíží

Vlastní zásady, které jste právě nahráli, otestovat na webu Azure Portal, přejděte na okno zásad a pak klikněte na tlačítko **spustit nyní**. Pokud selže, přečtěte si téma [řešení potíží s vlastními zásadami](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Další postup

Poskytnout zpětnou vazbu k [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
