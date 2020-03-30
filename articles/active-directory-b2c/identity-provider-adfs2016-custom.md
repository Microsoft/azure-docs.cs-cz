---
title: Přidání služby ADFS jako zprostředkovatele identity SAML pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Nastavení ADFS 2016 pomocí protokolu SAML a vlastních zásad ve službě Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bfe39d9528927f995d14772e07e02b2a0528e5e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188524"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Přidání služby ADFS jako zprostředkovatele identity SAML pomocí vlastních zásad ve službě Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek ukazuje, jak povolit přihlášení k uživatelskému účtu ADFS pomocí [vlastních zásad](custom-policy-overview.md) ve službě Azure Active Directory B2C (Azure AD B2C). Přihlášení povolíte přidáním [technického profilu SAML](saml-technical-profile.md) do vlastní chodnícího pravidla.

## <a name="prerequisites"></a>Požadavky

- Dokončete kroky v [části Začínáme s vlastními zásadami ve službě Azure Active Directory B2C](custom-policy-get-started.md).
- Ujistěte se, že máte přístup k souboru pfx certifikátu se soukromým klíčem. Můžete vygenerovat vlastní podepsaný certifikát a nahrát ho do Azure AD B2C. Azure AD B2C používá tento certifikát k podepsání požadavku SAML odeslaného poskytovateli identity SAML.
- Aby Azure mohl přijmout heslo souboru .pfx, musí být heslo zašifrováno pomocí možnosti TripleDES-SHA1 v nástroji Export úložiště certifikátů Windows na rozdíl od AES256-SHA256.

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Musíte uložit certifikát v tenantovi Azure AD B2C.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C. V horní nabídce vyberte filtr **Directory + odběr** a zvolte adresář, který obsahuje vašeho klienta.
3. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Na stránce Přehled vyberte **rozhraní Identity Experience Framework**.
5. Vyberte **klávesy zásad** a pak vyberte **Přidat**.
6. V části `Upload` **Možnosti**zvolte .
7. Zadejte **název** klíče zásady. Například, `SamlCert`. Předpona `B2C_1A_` se automaticky přidá k názvu klíče.
8. Vyhledejte a vyberte soubor .pfx certifikátu pomocí soukromého klíče.
9. Klikněte na **Vytvořit**.

## <a name="add-a-claims-provider"></a>Přidání poskytovatele deklarací identity

Pokud chcete, aby se uživatelé přihlašovali pomocí účtu ADFS, musíte definovat účet jako poskytovatele deklarací identity, se kterým může Azure AD B2C komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací, které používá Azure AD B2C k ověření, že konkrétní uživatel byl ověřen.

Účet ADFS můžete definovat jako poskytovatele deklarací identity přidáním do prvku **ClaimsProviders** v rozšiřujícím souboru zásad. Další informace naleznete [v tématu definování technického profilu SAML](saml-technical-profile.md).

1. Otevřete *soubor TrustFrameworkExtensions.xml*.
1. Najít **ClaimsProviders** element. Pokud neexistuje, přidejte jej pod kořenový prvek.
1. Přidejte nového **Zprostředkovatele deklarací** identity takto:

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
            <Item Key="XmlSignatureAlgorithm">Sha256</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
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
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Nahraďte `your-ADFS-domain` název domény Služby ADFS a nahraďte hodnotu výstupní deklarace **identityProvider** pomocí dns (libovolná hodnota označující vaši doménu).

1. Vyhledejte `<ClaimsProviders>` oddíl a přidejte následující úryvek XML. Pokud vaše zásady `SM-Saml-idp` již technicképrofil obsahují, přejděte k dalšímu kroku. Další informace naleznete v [tématu správa relace jednotného přihlášení](custom-policy-reference-sso.md).

    ```XML
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

1. Uložte soubor.

### <a name="upload-the-extension-file-for-verification"></a>Nahrát soubor rozšíření pro ověření

Teď jste nakonfigurovali zásady tak, aby Azure AD B2C ví, jak komunikovat s účtem ADFS. Zkuste nahrát soubor rozšíření zásad, abyste potvrdili, že zatím nemá žádné problémy.

1. Na stránce **Vlastní zásady** v tenantovi Azure AD B2C vyberte **Zásady nahrávání**.
2. Povolte **Přepište zásadu, pokud existuje**, a potom vyhledejte soubor *TrustFrameworkExtensions.xml* a vyberte jej.
3. Klikněte na **Odeslat**.

> [!NOTE]
> Rozšíření kódu Visual Studio B2C používá "socialIdpUserId." Sociální politika je vyžadována také pro ADFS.
>

## <a name="register-the-claims-provider"></a>Registrace zprostředkovatele deklarací identity

V tomto okamžiku byl nastaven zprostředkovatel identity, ale není k dispozici v žádné z přihlašovacích nebo přihlašovacích obrazovek. Chcete-li ji zpřístupnit, vytvořte duplikát existující cesty uživatele šablony a pak ji upravte tak, aby měla také zprostředkovatele identity služby ADFS.

1. Otevřete soubor *TrustFrameworkBase.xml* ze startovního balíčku.
2. Vyhledejte a zkopírujte celý obsah prvku `Id="SignUpOrSignIn"` **UserJourney,** který obsahuje .
3. Otevřete soubor *TrustFrameworkExtensions.xml* a vyhledejte element **UserJourneys.** Pokud prvek neexistuje, přidejte jeden.
4. Vložte celý obsah **UserJourney** prvek, který jste zkopírovali jako podřízený **UserJourneys** element.
5. Přejmenujte ID cesty uživatele. Například, `SignUpSignInADFS`.

### <a name="display-the-button"></a>Zobrazení tlačítka

**ClaimsProviderSelection** prvek je obdobou tlačítko zprostředkovatele identity na přihlašovací nebo přihlašovací obrazovce. Pokud přidáte **ClaimsProviderSelection** prvek pro účet ADFS, nové tlačítko se zobrazí, když uživatel přistane na stránce.

1. Najděte **orchestrationstep** prvek, který zahrnuje `Order="1"` v cestě uživatele, které jste vytvořili.
2. V části **ClaimsProviderSelections**přidejte následující prvek. Nastavte hodnotu **TargetClaimsExchangeId** na příslušnou hodnotu, například `ContosoExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Propojení tlačítka s akcí

Nyní, když máte tlačítko na místě, musíte jej propojit s akcí. Akce, v tomto případě je pro Azure AD B2C komunikovat s účtem ADFS pro příjem tokenu.

1. Najít **OrchestrationStep,** `Order="2"` který zahrnuje v cestě uživatele.
2. Přidejte následující **claimsexchange** prvek ujistěte se, že používáte stejnou hodnotu pro ID, které jste použili pro **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

    Aktualizujte hodnotu **TechnicalProfileReferenceId** na ID technického profilu, který jste vytvořili dříve. Například, `Contoso-SAML2`.

3. Uložte soubor *TrustFrameworkExtensions.xml* a znovu jej nahrajte k ověření.


## <a name="configure-an-adfs-relying-party-trust"></a>Konfigurace vztahu důvěryhodnosti předávající strany služby ADFS

Chcete-li použít ADFS jako zprostředkovatele identity v Azure AD B2C, musíte vytvořit adfs předávající strany trust s metadaty Azure AD B2C SAML. Následující příklad ukazuje adresu URL metadat SAML technického profilu Azure AD B2C:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Nahraďte následující hodnoty:

- **vašeho tenanta** s vaším jménem tenanta, například your-tenant.onmicrosoft.com.
- **zásady** s vaším názvem zásad. Například B2C_1A_signup_signin_adfs.
- **váš technický profil** s názvem technického profilu poskytovatele identity SAML. Například Contoso-SAML2.

Otevřete prohlížeč a přejděte na adresu URL. Ujistěte se, že jste zadali správnou adresu URL a že máte přístup k souboru metadat XML. Chcete-li přidat nový vztah důvěryhodnosti předávající strany pomocí modulu snap-in Správa služby ADFS a ručně nakonfigurovat nastavení, proveďte na federačním serveru následující postup. K dokončení tohoto postupu je nutné členství v **administrátorech** nebo ekvivalentu v místním počítači.

1. Ve Správci serveru vyberte **Nástroje**a potom vyberte **položku ADFS Management**.
2. Vyberte **Přidat důvěryhodný certifikát předávající strany**.
3. Na **úvodní** stránce zvolte **Deklarace identity**a potom klepněte na tlačítko **Start**.
4. Na stránce **Vybrat zdroj dat** vyberte **importdat o publikování předávající strany online nebo v místní síti**, zadejte adresu URL metadat Azure AD B2C a klikněte na **další**.
5. Na stránce **Zadat zobrazovaný název** zadejte **zobrazovaný název**, v části **Poznámky**zadejte popis tohoto vztahu důvěryhodnosti předávající strany a klepněte na tlačítko **Další**.
6. Na stránce **Zvolit zásady řízení přístupu** vyberte zásadu a klikněte na **Další**.
7. Na stránce **Připraveno k přidání vztahu důvěryhodnosti** zkontrolujte nastavení a kliknutím na **tlačítko Další** uložte informace o důvěryhodnosti předávající strany.
8. Na stránce **Dokončení** klikněte na **Zavřít**, tato akce automaticky zobrazí dialogové okno **Upravit pravidla deklarací.**
9. Vyberte **Přidat pravidlo**.
10. V **šabloně pravidla deklarace**vyberte **Odeslat atributy LDAP jako deklarace identity**.
11. Zadejte **název pravidla deklarace**. V **úložišti atributů**vyberte **vybrat službu Active Directory**, přidejte následující deklarace identity a klepněte na tlačítko **Dokončit** a **OK**.

    | Ldap, atribut | Typ odchozí deklarace |
    | -------------- | ------------------- |
    | Hlavní název uživatele | userPrincipalName (Hlavní název uživatele) |
    | příjmení | family_name |
    | Křestní jméno | given_name |
    | E-mailová adresa | e-mail |
    | Zobrazovaný název | jméno |

    Všimněte si, že tyto názvy se nezobrazí v rozevírací části typu odchozí deklarace. Musíte je zadat ručně. (Rozevírací možnost je ve skutečnosti upravitelná).

12.  Na základě typu certifikátu může být nutné nastavit algoritmus HASH. V okně vlastností vztahu důvěryhodnosti předávající strany (B2C Demo) vyberte kartu **Upřesnit** a změňte **algoritmus algoritmu hash secure** na `SHA-256`a klepněte na tlačítko **Ok**.
13. Ve Správci serveru vyberte **Nástroje**a potom vyberte **položku ADFS Management**.
14. Vyberte vytvoření důvěryhodného vztahu předávající strany, vyberte **aktualizovat z metadat federace**a klepněte na tlačítko **Aktualizovat**.

## <a name="create-an-azure-ad-b2c-application"></a>Vytvoření aplikace Azure AD B2C

Komunikace s Azure AD B2C probíhá prostřednictvím aplikace, kterou zaregistrujete ve vašem tenantovi B2C. V této části jsou uvedeny volitelné kroky, které můžete provést a vytvořit testovací aplikaci, pokud jste tak ještě neučinili.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

### <a name="update-and-test-the-relying-party-file"></a>Aktualizace a testování souboru předávající strany

Aktualizujte soubor předávající strany (RP), který iniciuje cestu uživatele, kterou jste vytvořili.

1. Vytvořte kopii *souboru SignUpOrSignIn.xml* ve svém pracovním adresáři a přejmenujte ji. Můžete jej například přejmenovat na *SignUpSignInADFS.xml*.
2. Otevřete nový soubor a aktualizujte hodnotu atributu **PolicyId** pro **TrustFrameworkPolicy** s jedinečnou hodnotou. Například, `SignUpSignInADFS`.
3. Aktualizujte hodnotu **PublicPolicyUri** pomocí identifikátoru URI pro zásadu. Například`http://contoso.com/B2C_1A_signup_signin_adfs`
4. Aktualizujte hodnotu atributu **ReferenceId** v **DefaultUserJourney** tak, aby odpovídalo ID nové cesty uživatele, kterou jste vytvořili (SignUpSignInADFS).
5. Uložte změny, nahrajte soubor a vyberte novou zásadu v seznamu.
6. Ujistěte se, že aplikace Azure AD B2C, kterou jste vytvořili, je vybraná v poli **Vybrat aplikaci** a pak ji otestujte kliknutím na **spustit nyní**.

