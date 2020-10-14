---
title: Nastavení přihlašování pomocí poskytovatele služby Salesforce SAML pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Pomocí vlastních zásad v Azure Active Directory B2C nastavte přihlášení pomocí zprostředkovatele SAML služby Salesforce.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b709293360dfaae8b7aa694442e44e9e4ab8a019
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92054674"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-custom-policies-in-azure-active-directory-b2c"></a>Pomocí vlastních zásad v Azure Active Directory B2C nastavte přihlášení pomocí zprostředkovatele SAML služby Salesforce.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku se dozvíte, jak povolit přihlášení uživatelů z organizace Salesforce pomocí [vlastních zásad](custom-policy-overview.md) v Azure Active Directory B2C (Azure AD B2C). Přihlášení povolíte přidáním [technického profilu zprostředkovatele identity SAML](saml-identity-provider-technical-profile.md) do vlastních zásad.

## <a name="prerequisites"></a>Požadavky

- Proveďte kroky v části Začínáme [s vlastními zásadami v Azure Active Directory B2C](custom-policy-get-started.md).
- Pokud jste to ještě neudělali, zaregistrujte si [bezplatný účet Developer Edition](https://developer.salesforce.com/signup). Tento článek používá [prostředí Salesforce blesk](https://developer.salesforce.com/page/Lightning_Experience_FAQ).
- [Nastavte doménu](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) pro vaši organizaci Salesforce.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Nastavení Salesforce jako zprostředkovatele identity

1. [Přihlaste se k Salesforce](https://login.salesforce.com/).
2. V nabídce vlevo v části **Nastavení**rozbalte položku **Identita**a potom vyberte možnost **zprostředkovatel identity**.
3. Vyberte možnost **Povolit zprostředkovatele identity**.
4. V části **Vybrat certifikát**vyberte certifikát, který má Salesforce použít ke komunikaci s Azure AD B2C. Můžete použít výchozí certifikát.
5. Klikněte na **Uložit**.

### <a name="create-a-connected-app-in-salesforce"></a>Vytvoření připojené aplikace v Salesforce

1. Na stránce **zprostředkovatel identity** **se teď v části připojené aplikace vytvoří poskytovatelé služeb. Klikněte sem.**
2. V části **základní informace**zadejte požadované hodnoty pro připojenou aplikaci.
3. V části **nastavení webové aplikace**zaškrtněte políčko **Povolit SAML** .
4. Do pole **ID entity** zadejte následující adresu URL. Ujistěte se, že jste nahradili hodnotu pro `your-tenant` s názvem vašeho tenanta Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. Do pole **Adresa URL služby ACS** zadejte následující adresu URL. Ujistěte se, že jste nahradili hodnotu pro `your-tenant` s názvem vašeho tenanta Azure AD B2C.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Posuňte se do dolní části seznamu a klikněte na **Uložit**.

### <a name="get-the-metadata-url"></a>Získat adresu URL metadat

1. Na stránce Přehled vaší připojené aplikace klikněte na **Spravovat**.
2. Zkopírujte hodnotu pro **koncový bod zjišťování metadat**a pak ho uložte. Použijete ho později v tomto článku.

### <a name="set-up-salesforce-users-to-federate"></a>Nastavení uživatelů Salesforce na federovat

1. Na stránce **Správa** připojené aplikace klikněte na **Spravovat profily**.
2. Vyberte profily (nebo skupiny uživatelů), které chcete federovat s Azure AD B2C. Jako správce systému zaškrtněte políčko **správce systému** , abyste se mohli federovat pomocí svého účtu Salesforce.

## <a name="generate-a-signing-certificate"></a>Vygenerovat podpisový certifikát

Požadavky odeslané službě Salesforce musí být podepsány Azure AD B2C. Chcete-li vygenerovat podpisový certifikát, otevřete Azure PowerShell a spusťte následující příkazy.

> [!NOTE]
> Ujistěte se, že jste aktualizovali název tenanta a heslo v horních dvou řádcích.

```powershell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Je potřeba uložit certifikát, který jste vytvořili v tenantovi Azure AD B2C.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Na stránce Přehled vyberte možnost **Architektura prostředí identity**.
5. Vyberte **klíče zásad** a pak vyberte **Přidat**.
6. Pro **Možnosti**vyberte možnost `Upload` .
7. Zadejte **Název** zásady. Například SAMLSigningCert. Předpona `B2C_1A_` se automaticky přidá do názvu vašeho klíče.
8. Vyhledejte a vyberte certifikát B2CSigningCert. pfx, který jste vytvořili.
9. Zadejte **heslo** pro certifikát.
3. Klikněte na **Vytvořit**.

## <a name="add-a-claims-provider"></a>Přidat zprostředkovatele deklarací identity

Pokud chcete, aby se uživatelé přihlásili pomocí účtu Salesforce, musíte účet definovat jako zprostředkovatele deklarací identity, se kterým Azure AD B2C můžou komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací, které používá Azure AD B2C k ověření, že konkrétní uživatel byl ověřen.

Účet Salesforce můžete definovat jako zprostředkovatele deklarací tak, že ho přidáte do prvku **ClaimsProviders** v souboru rozšíření zásady. Další informace najdete v tématu [definice technického profilu zprostředkovatele identity SAML](saml-identity-provider-technical-profile.md).

1. Otevřete *TrustFrameworkExtensions.xml*.
1. Vyhledejte element **ClaimsProviders** . Pokud neexistuje, přidejte jej pod kořenový element.
1. Přidejte nový **ClaimsProvider** následujícím způsobem:

    ```xml
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Aktualizujte hodnotu **PartnerEntity** pomocí adresy URL metadat Salesforce, kterou jste zkopírovali dříve.
1. Aktualizujte hodnotu obou instancí **StorageReferenceId** na název klíče podpisového certifikátu. Například B2C_1A_SAMLSigningCert.
1. Vyhledejte `<ClaimsProviders>` část a přidejte následující fragment kódu XML. Pokud vaše zásada již obsahuje `SM-Saml-idp` technický profil, přejděte k dalšímu kroku. Další informace najdete v tématu [Správa relace jednotného přihlašování](custom-policy-reference-sso.md).

    ```xml
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```
1. Soubor uložte.

### <a name="upload-the-extension-file-for-verification"></a>Nahrajte soubor rozšíření pro ověření.

Teď jste nakonfigurovali zásady tak, aby Azure AD B2C vědět, jak komunikovat s vaším účtem Salesforce. Zkuste nahrát soubor s příponou zásady jenom tak, aby se ověřilo, že zatím nemá žádné problémy.

1. Na stránce **vlastní zásady** ve vašem tenantovi Azure AD B2C vyberte **Odeslat zásadu**.
2. Pokud existuje, zapněte **zásadu přepsat**a pak vyhledejte a vyberte soubor *TrustFrameworkExtensions.xml* .
3. Klikněte na **Odeslat**.

## <a name="register-the-claims-provider"></a>Registrace zprostředkovatele deklarací identity

V tuto chvíli je poskytovatel identity nastavený, ale není k dispozici na žádném z přihlašovacích obrazovek pro registraci nebo přihlášení. Aby byl k dispozici, vytvořte duplikát existující cesty uživatele šablony a pak ji upravte, aby měl také poskytovatele identity Salesforce.

1. Otevřete soubor *TrustFrameworkBase.xml* z úvodní sady.
2. Vyhledejte a zkopírujte celý obsah prvku **UserJourney** , který obsahuje `Id="SignUpOrSignIn"` .
3. Otevřete *TrustFrameworkExtensions.xml* a vyhledejte element **userjourney** . Pokud element neexistuje, přidejte jej.
4. Vložte celý obsah elementu **UserJourney** , který jste zkopírovali jako podřízený prvek **userjourney** elementu.
5. Přejmenujte ID cesty pro uživatele. Například, `SignUpSignInSalesforce`.

### <a name="display-the-button"></a>Zobrazit tlačítko

Element **claimsproviderselection.** se podobá tlačítku poskytovatele identity na obrazovce pro registraci nebo přihlášení. Pokud přidáte pro účet LinkedIn element **claimsproviderselection.** , zobrazí se nové tlačítko, když se uživatel na stránce zařadí.

1. Vyhledejte element **OrchestrationStep** , který obsahuje `Order="1"` cestu k uživateli, kterou jste právě vytvořili.
2. Pod **ClaimsProviderSelects**přidejte následující element. Nastavte hodnotu **TargetClaimsExchangeId** na odpovídající hodnotu, například `SalesforceExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Propojit tlačítko s akcí

Teď, když máte tlačítko na místě, musíte ho propojit s akcí. Tato akce je v tomto případě určena pro Azure AD B2C ke komunikaci s účtem Salesforce pro příjem tokenu.

1. Najděte **OrchestrationStep** , který obsahuje `Order="2"` cestu k uživateli.
2. Přidejte následující prvek **ClaimsExchange** a ujistěte se, že používáte stejnou hodnotu pro **ID** , které jste použili pro **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="salesforce" />
    ```

    Aktualizujte hodnotu **TechnicalProfileReferenceId** na **ID** technického profilu, který jste vytvořili dříve. Příkladem je `salesforce` nebo `LinkedIn-OAUTH`.

3. Uložte soubor *TrustFrameworkExtensions.xml* a znovu ho nahrajte pro účely ověření.

## <a name="create-an-azure-ad-b2c-application"></a>Vytvoření aplikace Azure AD B2C

Komunikace s Azure AD B2C probíhá prostřednictvím aplikace, kterou zaregistrujete v tenantovi B2C. V této části jsou uvedeny volitelné kroky, které můžete provést, chcete-li vytvořit testovací aplikaci, pokud jste tak již neučinili.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aktualizace a testování souboru předávající strany

Aktualizujte soubor předávající strany (RP), který iniciuje cestu uživatele, kterou jste právě vytvořili:

1. Vytvořte kopii *SignUpOrSignIn.xml* v pracovním adresáři a přejmenujte ji. Přejmenujte ho například na *SignUpSignInSalesforce.xml*.
2. Otevřete nový soubor a aktualizujte hodnotu atributu **PolicyId** pro **TrustFrameworkPolicy** s jedinečnou hodnotou. Například, `SignUpSignInSalesforce`.
3. Aktualizujte hodnotu **PUBLICPOLICYURI** identifikátorem URI pro zásadu. Například`http://contoso.com/B2C_1A_signup_signin_salesforce`
4. Aktualizujte hodnotu atributu **ReferenceId** v **DefaultUserJourney** tak, aby odpovídala ID nové cesty uživatele, kterou jste vytvořili (SignUpSignInSalesforce).
5. Uložte změny, nahrajte soubor a pak v seznamu vyberte novou zásadu.
6. Ujistěte se, že je vybrána možnost Azure AD B2C aplikace, kterou jste vytvořili v poli **Vybrat aplikaci** , a poté ji otestujte kliknutím na tlačítko **Spustit nyní**.
