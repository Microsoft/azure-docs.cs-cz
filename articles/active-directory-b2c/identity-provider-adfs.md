---
title: Přidání AD FS jako zprostředkovatele identity SAML pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Nastavení AD FS 2016 pomocí protokolu SAML a vlastních zásad v Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/07/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 767f60cae2f74f7e2a928253d45011bb6ceb5d0e
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653839"
---
# <a name="add-ad-fs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Přidání AD FS jako zprostředkovatele identity SAML pomocí vlastních zásad v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku se dozvíte, jak povolit přihlášení AD FS uživatelského účtu pomocí [vlastních zásad](custom-policy-overview.md) v Azure Active Directory B2C (Azure AD B2C). Přihlášení povolíte přidáním [technického profilu zprostředkovatele identity SAML](saml-identity-provider-technical-profile.md) do vlastních zásad.

## <a name="prerequisites"></a>Požadavky

- Proveďte kroky v části Začínáme [s vlastními zásadami v Azure Active Directory B2C](custom-policy-get-started.md).
- Ujistěte se, že máte přístup k souboru certifikátu. pfx s privátním klíčem. Můžete vygenerovat vlastní podepsaný certifikát a odeslat ho do Azure AD B2C. Azure AD B2C používá tento certifikát k podepsání žádosti SAML odeslané poskytovateli identity SAML. Další informace o tom, jak vygenerovat certifikát, najdete v tématu [vygenerování podpisového certifikátu](identity-provider-salesforce-saml.md#generate-a-signing-certificate).
- Aby mohl Azure přijmout heslo k souboru. pfx, musí být heslo zašifrované s možností TripleDES-SHA1 v nástroji pro export certifikátů Windows, a to na rozdíl od AES256-SHA256.

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Certifikát musíte uložit do svého tenanta Azure AD B2C.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Na stránce Přehled vyberte možnost **Architektura prostředí identity**.
5. Vyberte **klíče zásad** a pak vyberte **Přidat**.
6. Pro **Možnosti** vyberte možnost `Upload` .
7. Zadejte **název** klíče zásad. Například `ADFSSamlCert`. Předpona `B2C_1A_` se automaticky přidá do názvu vašeho klíče.
8. Vyhledejte a vyberte soubor Certificate. pfx s privátním klíčem.
9. Klikněte na **Vytvořit**.

## <a name="add-a-claims-provider"></a>Přidat zprostředkovatele deklarací identity

Pokud chcete, aby se uživatelé přihlásili pomocí účtu AD FS, musíte účet definovat jako zprostředkovatele deklarací identity, se kterým Azure AD B2C může komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací, které používá Azure AD B2C k ověření, že konkrétní uživatel byl ověřen.

Účet AD FS můžete definovat jako zprostředkovatele deklarací tak, že ho přidáte do prvku **ClaimsProviders** v souboru rozšíření zásady. Další informace najdete v tématu [definice technického profilu zprostředkovatele identity SAML](saml-identity-provider-technical-profile.md).

1. Otevřete *TrustFrameworkExtensions.xml*.
1. Vyhledejte element **ClaimsProviders** . Pokud neexistuje, přidejte jej pod kořenový element.
1. Přidejte nový **ClaimsProvider** následujícím způsobem:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso AD FS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso AD FS</DisplayName>
          <Description>Login with your AD FS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlCert"/>
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

1. Nahraďte `your-AD-FS-domain` názvem vaší domény AD FS a nahraďte hodnotu výstupní deklarace **IDENTITYPROVIDER** svým DNS (libovolná hodnota, která označuje vaši doménu).

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

1. Uložte soubor.

### <a name="upload-the-extension-file-for-verification"></a>Nahrajte soubor rozšíření pro ověření.

Teď jste nakonfigurovali zásady tak, aby Azure AD B2C vědět, jak komunikovat s účtem AD FS. Zkuste nahrát soubor s příponou zásady jenom tak, aby se ověřilo, že zatím nemá žádné problémy.

1. Na stránce **vlastní zásady** ve vašem tenantovi Azure AD B2C vyberte **Odeslat zásadu**.
2. Pokud existuje, zapněte **zásadu přepsat** a pak vyhledejte a vyberte soubor *TrustFrameworkExtensions.xml* .
3. Klikněte na **Odeslat**.

> [!NOTE]
> Rozšíření Visual Studio Code B2C Extension používá "socialIdpUserId". Pro AD FS se taky vyžadují zásady pro sociální sítě.
>

## <a name="register-the-claims-provider"></a>Registrace zprostředkovatele deklarací identity

V tuto chvíli je poskytovatel identity nastavený, ale není k dispozici na žádném z přihlašovacích obrazovek pro registraci nebo přihlášení. Aby byl k dispozici, vytvořte duplikát existující cesty uživatele šablony a pak ji upravte, aby měl také AD FS zprostředkovatele identity.

1. Otevřete soubor *TrustFrameworkBase.xml* z úvodní sady.
2. Vyhledejte a zkopírujte celý obsah prvku **UserJourney** , který obsahuje `Id="SignUpOrSignIn"` .
3. Otevřete *TrustFrameworkExtensions.xml* a vyhledejte element **userjourney** . Pokud element neexistuje, přidejte jej.
4. Vložte celý obsah elementu **UserJourney** , který jste zkopírovali jako podřízený prvek **userjourney** elementu.
5. Přejmenujte ID cesty pro uživatele. Například `SignUpSignInADFS`.

### <a name="display-the-button"></a>Zobrazit tlačítko

Element **claimsproviderselection.** se podobá tlačítku poskytovatele identity na obrazovce pro registraci nebo přihlášení. Pokud přidáte prvek **claimsproviderselection.** pro účet AD FS, zobrazí se nové tlačítko, když se uživatel na stránce zařadí.

1. Vyhledejte element **OrchestrationStep** , který obsahuje `Order="1"` cestu k uživateli, kterou jste vytvořili.
2. Pod **ClaimsProviderSelections** přidejte následující element. Nastavte hodnotu **TargetClaimsExchangeId** na odpovídající hodnotu, například `ContosoExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Propojit tlačítko s akcí

Teď, když máte tlačítko na místě, musíte ho propojit s akcí. Tato akce je v tomto případě určena pro Azure AD B2C ke komunikaci s účtem AD FS k získání tokenu.

1. Najděte **OrchestrationStep** , který obsahuje `Order="2"` cestu k uživateli.
2. Přidejte následující prvek **ClaimsExchange** a ujistěte se, že používáte stejnou hodnotu pro ID, které jste použili pro **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

    Aktualizujte hodnotu **TechnicalProfileReferenceId** na ID technického profilu, který jste vytvořili dříve. Například `Contoso-SAML2`.

3. Uložte soubor *TrustFrameworkExtensions.xml* a znovu ho nahrajte pro účely ověření.


## <a name="configure-an-ad-fs-relying-party-trust"></a>Konfigurace vztahu důvěryhodnosti předávající strany AD FS

Pokud chcete použít AD FS jako poskytovatele identity v Azure AD B2C, musíte vytvořit AD FS vztahu důvěryhodnosti předávající strany s Azure AD B2C metadaty SAML. Následující příklad ukazuje adresu URL k metadatům SAML Azure AD B2C technického profilu:

```
https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

Nahraďte následující hodnoty:

- **váš tenant** s názvem vašeho tenanta, například Your-tenant.onmicrosoft.com.
- **vaše zásady** s názvem zásady. Například B2C_1A_signup_signin_adfs.
- **váš** technický profil s názvem vašeho technického profilu zprostředkovatele identity SAML. Například contoso-typu Saml2.

Otevřete prohlížeč a přejděte na adresu URL. Ujistěte se, že jste zadali správnou adresu URL a máte přístup k souboru metadat XML. Chcete-li přidat nový vztah důvěryhodnosti předávající strany pomocí modulu snap-in Správa AD FS a ručně nakonfigurovat nastavení, proveďte následující postup na federačním serveru. Tento postup **mohou provést pouze členové skupiny Administrators nebo uživatelé** s ekvivalentními oprávněními na místním počítači.

1. V Správce serveru vyberte **nástroje** a pak vyberte **AD FS Správa**.
2. Vyberte **Přidat vztah důvěryhodnosti předávající strany**.
3. Na **úvodní** stránce Zvolte možnost **vědomosti deklarací identity** a potom klikněte na tlačítko **Spustit**.
4. Na stránce **Vybrat zdroj dat** vyberte možnost **importovat data o předávající straně publikování online nebo v místní síti**, zadejte adresu URL metadat Azure AD B2C a potom klikněte na **Další**.
5. Na stránce **zadat zobrazovaný název** zadejte **Zobrazovaný název** v části **poznámky**, zadejte popis pro tento vztah důvěryhodnosti předávající strany a klikněte na **Další**.
6. Na stránce **Zvolte zásady Access Control** vyberte zásadu a pak klikněte na **Další**.
7. Na stránce **připraveno k přidání vztahu důvěryhodnosti** zkontrolujte nastavení a potom kliknutím na tlačítko **Další** uložte informace o vztahu důvěryhodnosti předávající strany.
8. Na stránce **Dokončit** klikněte na **Zavřít**. Tato akce automaticky zobrazí dialogové okno **upravit pravidla deklarací identity** .
9. Vyberte **Přidat pravidlo**.
10. V **šabloně pravidla deklarace identity** vyberte **Odeslat atributy LDAP jako deklarace identity**.
11. Zadejte **název pravidla deklarace identity**. V případě **úložiště atributů** vyberte **možnost vybrat službu Active Directory**, přidejte následující deklarace identity a klikněte na tlačítko **Dokončit** a **OK**.

    | LDAP – atribut | Typ odchozí deklarace |
    | -------------- | ------------------- |
    | Hlavní název uživatele | userPrincipalName (Hlavní název uživatele) |
    | příjmení | family_name |
    | Given-Name | given_name |
    | E-mailová adresa | e-mail |
    | Display-Name | name |

    Všimněte si, že tyto názvy se nezobrazí v rozevíracím seznamu typ odchozí deklarace. Je nutné je ručně zadat v. (Rozevírací seznam je skutečně upravitelný).

12.  V závislosti na typu certifikátu možná budete muset nastavit algoritmus HASH. V okně Vlastnosti vztahu důvěryhodnosti předávající strany (B2C demo) vyberte kartu **Upřesnit** a změňte **zabezpečený algoritmus hash** na a `SHA-256` klikněte na tlačítko **OK**.
13. V Správce serveru vyberte **nástroje** a pak vyberte **AD FS Správa**.
14. Vyberte vztah důvěryhodnosti předávající strany, který jste vytvořili, vyberte **aktualizovat z federačních metadat** a pak klikněte na **aktualizovat**.

### <a name="update-and-test-the-relying-party-file"></a>Aktualizace a testování souboru předávající strany

Aktualizujte soubor předávající strany (RP), který iniciuje cestu uživatele, kterou jste vytvořili.

1. Vytvořte kopii *SignUpOrSignIn.xml* v pracovním adresáři a přejmenujte ji. Přejmenujte ho například na *SignUpSignInADFS.xml*.
2. Otevřete nový soubor a aktualizujte hodnotu atributu **PolicyId** pro **TrustFrameworkPolicy** s jedinečnou hodnotou. Například `SignUpSignInADFS`.
3. Aktualizujte hodnotu **PUBLICPOLICYURI** identifikátorem URI pro zásadu. Například`http://contoso.com/B2C_1A_signup_signin_adfs`
4. Aktualizujte hodnotu atributu **ReferenceId** v **DefaultUserJourney** tak, aby odpovídala ID nové cesty uživatele, kterou jste vytvořili (SignUpSignInADFS).
5. Uložte změny, nahrajte soubor a pak v seznamu vyberte novou zásadu.
6. Ujistěte se, že je vybrána možnost Azure AD B2C aplikace, kterou jste vytvořili v poli **Vybrat aplikaci** , a poté ji otestujte kliknutím na tlačítko **Spustit nyní**.

## <a name="troubleshooting-ad-fs-service"></a>Řešení potíží se službou AD FS  

AD FS je nakonfigurován pro použití protokolu aplikace systému Windows. Pokud se setkáte s problémy s nastavením AD FS jako zprostředkovatele identity SAML pomocí vlastních zásad v Azure AD B2C, možná budete chtít ověřit protokol událostí AD FS:

1. Na **panelu hledání** ve Windows zadejte **Prohlížeč událostí** a pak vyberte aplikaci **Prohlížeč událostí** Desktop.
1. Chcete-li zobrazit protokol jiného počítače, klikněte pravým tlačítkem na **Prohlížeč událostí (místní)**. Vyberte **připojit k jinému počítači** a vyplňte pole a dokončete dialog **Vybrat počítač** .
1. V **Prohlížeč událostí** otevřete **protokoly aplikací a služeb**.
1. Vyberte **AD FS** a pak vyberte **správce**. 
1. Chcete-li zobrazit další informace o události, dvakrát klikněte na událost.  

### <a name="saml-request-is-not-signed-with-expected-signature-algorithm-event"></a>Požadavek SAML není podepsán s očekávanou událostí algoritmu podpisu.

Tato chyba označuje, že požadavek SAML odeslaný pomocí Azure AD B2C není podepsán pomocí očekávaného algoritmu podpisu nakonfigurovaného v AD FS. Například požadavek SAML je podepsaný algoritmem signatury `rsa-sha256` , ale očekával se algoritmus signatury `rsa-sha1` . Chcete-li tento problém vyřešit, zajistěte, aby byly nakonfigurovány Azure AD B2C i AD FS se stejným algoritmem signatury.

#### <a name="option-1-set-the-signature-algorithm-in-azure-ad-b2c"></a>Možnost 1: nastavte algoritmus podpisu v Azure AD B2C  

Můžete nakonfigurovat, jak podepsat požadavek SAML v Azure AD B2C. Metadata [XmlSignatureAlgorithm](saml-identity-provider-technical-profile.md#metadata) řídí hodnotu `SigAlg` parametru (řetězec dotazu nebo parametr post) v požadavku SAML. Následující příklad nakonfiguruje Azure AD B2C k použití `rsa-sha256` algoritmu podpisu.

```xml
<Metadata>
  <Item Key="WantsEncryptedAssertions">false</Item>
  <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
  <Item Key="XmlSignatureAlgorithm">Sha256</Item>
</Metadata>
```

#### <a name="option-2-set-the-signature-algorithm-in-ad-fs"></a>Možnost 2: nastavte algoritmus podpisu v AD FS 

Případně můžete v AD FS nakonfigurovat očekávaný algoritmus podpisu žádosti SAML.

1. V Správce serveru vyberte **nástroje** a pak vyberte **AD FS Správa**.
1. Vyberte **vztah důvěryhodnosti předávající strany** , který jste vytvořili dříve.
1. Vyberte **vlastnosti** a pak vyberte **pokračovat** .
1. Nakonfigurujte **zabezpečený algoritmus hash** a kliknutím na **tlačítko OK** změny uložte.

::: zone-end