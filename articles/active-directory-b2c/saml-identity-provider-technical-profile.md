---
title: Definování technického profilu SAML ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Definujte technický profil SAML ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: db99fbdea38dd30401a8aeedb7ebc23c71c5236c
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655182"
---
# <a name="define-a-saml-identity-provider-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu zprostředkovatele identity SAML v Azure Active Directory B2C vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) poskytuje podporu pro poskytovatele identity SAML 2,0. Tento článek popisuje konkrétní technické profily pro interakci se zprostředkovatelem deklarací, který podporuje tento standardizovaný protokol. S technickým profilem SAML můžete federovat poskytovatelem identity založeného na SAML, jako je [ADFS](identity-provider-adfs2016-custom.md) nebo [Salesforce](identity-provider-salesforce-saml.md). Tato federace umožňuje vašim uživatelům přihlašovat se svými stávajícími sociálními nebo podnikovými identitami.

## <a name="metadata-exchange"></a>Výměna metadat

Metadata jsou informace, které se používají v protokolu SAML k vystavení konfigurace večírku SAML, jako je poskytovatel služeb nebo zprostředkovatel identity. Metadata definují umístění služeb, jako je například přihlášení a odhlášení, certifikáty, metoda přihlašování a další. Zprostředkovatel identity používá metadata ke zjištění, jak komunikovat s Azure AD B2C. Metadata jsou nakonfigurovaná ve formátu XML a můžou být podepsaná digitálním podpisem, aby druhá strana mohla ověřit integritu metadat. Když Azure AD B2C federuje s poskytovatelem identity SAML, funguje jako poskytovatel služeb, který iniciuje požadavek SAML a čeká na odpověď SAML. A v některých případech akceptuje nevyžádaný ověřování SAML, které se také označuje jako iniciované zprostředkovatele identity.

Metadata je možné nakonfigurovat v obou stranách jako "statická metadata" nebo "dynamická metadata". Ve statickém režimu můžete kopírovat celá metadata od jedné strany a nastavit ji v druhé straně. V dynamickém režimu nastavíte adresu URL na metadata, zatímco druhá strana dynamicky čte konfiguraci. Zásady jsou stejné, nastavili jste metadata Azure AD B2C Technical Profile v poskytovateli identity a v Azure AD B2C nastavíte metadata poskytovatele identity.

Každý zprostředkovatel identity SAML má různé kroky k vystavení a nastavení poskytovatele služeb, v tomto případě Azure AD B2C a nastavení Azure AD B2C metadata v poskytovateli identity. Pokyny k tomu, jak to udělat, najdete v dokumentaci poskytovatele identity.

Následující příklad ukazuje adresu URL k metadatům SAML Azure AD B2C technického profilu:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Nahraďte následující hodnoty:

- název **vašeho** tenanta, jako je třeba Fabrikam.b2clogin.com.
- **vaše zásady** s názvem zásady. Použijte zásady, ve kterých nakonfigurujete technický profil poskytovatele SAML, nebo zásadu, která dědí z těchto zásad.
- **váš** technický profil s názvem vašeho technického profilu zprostředkovatele identity SAML.

## <a name="digital-signing-certificates-exchange"></a>Výměna certifikátů digitálního podepisování

Pokud chcete vytvořit vztah důvěryhodnosti mezi Azure AD B2C a vaším poskytovatelem identity SAML, musíte zadat platný certifikát x509 s privátním klíčem. Certifikát nahrajete pomocí privátního klíče (soubor. pfx) do úložiště klíčů zásad Azure AD B2C. Azure AD B2C digitálně podepíše žádost o přihlášení SAML pomocí vámi poskytnutého certifikátu.

Certifikát se používá následujícími způsoby:

- Azure AD B2C generuje požadavek SAML a podepíše ho pomocí Azure AD B2C privátního klíče certifikátu. Požadavek SAML se pošle zprostředkovateli identity, který ověří požadavek pomocí Azure AD B2C veřejného klíče certifikátu. Azure AD B2C veřejný certifikát je přístupný prostřednictvím metadat technického profilu. Případně můžete ručně odeslat soubor. cer do poskytovatele identity SAML.
- Poskytovatel identity podepisuje data odesílaná do Azure AD B2C pomocí privátního klíče poskytovatele identity certifikátu. Azure AD B2C ověří data pomocí veřejného certifikátu poskytovatele identity. Každý zprostředkovatel identity má různé kroky pro instalaci. Prohlédněte si dokumentaci poskytovatele identity, kde najdete pokyny k tomu, jak to udělat. V Azure AD B2C vaše zásada potřebuje přístup k veřejnému klíči certifikátu pomocí metadat poskytovatele identity.

Certifikát podepsaný svým držitelem je přijatelný pro většinu scénářů. V produkčních prostředích se doporučuje použít certifikát x509 vydaný certifikační autoritou. Kromě toho, jak je popsáno dále v tomto dokumentu, můžete u neprodukčního prostředí zakázat podepisování SAML na obou stranách.

Následující diagram znázorňuje metadata a výměnu certifikátů:

![metadata a výměna certifikátů](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)

## <a name="digital-encryption"></a>Digitální šifrování

K zašifrování kontrolního výrazu odpovědi SAML zprostředkovatel identity vždycky používá veřejný klíč šifrovacího certifikátu v Azure AD B2C Technical Profile. Pokud Azure AD B2C potřebuje data dešifrovat, používá privátní část certifikátu šifrování.

Postup při šifrování kontrolního výrazu odpovědi SAML:

1. Nahrajte platný certifikát x509 s privátním klíčem (soubor. pfx) do úložiště klíčů zásad Azure AD B2C.
2. Přidejte element **CryptographicKey** s identifikátorem `SamlAssertionDecryption` do kolekce **CryptographicKeys** Technical Profile. Nastavte **StorageReferenceId** na název klíče zásad, který jste vytvořili v kroku 1.
3. Nastavte metadata technického profilu **WantsEncryptedAssertions** na `true` .
4. Aktualizujte zprostředkovatele identity pomocí nových metadat profilu Azure AD B2C Technical Profile. Měl by se zobrazit **popisovač** klíče s nastavenou vlastností **Use** na `encryption` obsahující veřejný klíč certifikátu.

Následující příklad ukazuje oddíl popisovače klíče metadat SAML používaných k šifrování:

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

## <a name="protocol"></a>Protokol

Atribut **Name** elementu Protocol musí být nastaven na hodnotu `SAML2` .

## <a name="input-claims"></a>Vstupní deklarace identity

Element **InputClaims** se používá k odeslání **NameId** v rámci **předmětu** žádosti Authn SAML. K tomuto účelu přidejte vstupní deklaraci s **PartnerClaimType** nastavenou na `subject` , jak je znázorněno níže.

```xml
<InputClaims>
    <InputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="subject" />
</InputClaims>
```

## <a name="output-claims"></a>Deklarace výstupů

Element **OutputClaims** obsahuje seznam deklarací identity vrácených zprostředkovatelem identity SAML v části `AttributeStatement` . Možná budete muset namapovat název deklarace identity definované v zásadě na název definovaný v poskytovateli identity. Můžete také zahrnout deklarace identity, které nevrací poskytovatel identity, pokud nastavíte `DefaultValue` atribut.

### <a name="subject-name-output-claim"></a>Deklarace výstupu názvu subjektu

Pokud chcete v **předmětu** načíst kontrolní výraz SAML **NameId** jako normalizovanou deklaraci identity, nastavte na hodnotu atributu deklaraci identity **PartnerClaimType** `SPNameQualifier` . Pokud `SPNameQualifier` atribut není zobrazen, nastavte deklaraci identity **PartnerClaimType** na hodnotu `NameQualifier` atributu. 


Kontrolní výraz SAML: 

```xml
<saml:Subject>
  <saml:NameID SPNameQualifier="http://your-idp.com/unique-identifier" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient">david@contoso.com</saml:NameID>
  <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
    <SubjectConfirmationData InResponseTo="_cd37c3f2-6875-4308-a9db-ce2cf187f4d1" NotOnOrAfter="2020-02-15T16:23:23.137Z" Recipient="https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" />
    </SubjectConfirmation>
  </saml:SubjectConfirmation>
</saml:Subject>
```

Výstupní deklarace identity:

```xml
<OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="http://your-idp.com/unique-identifier" />
```

Pokud se `SPNameQualifier` `NameQualifier` v kontrolním výrazu SAML neprezentují oba atributy nebo, nastavte **PartnerClaimType** deklarací na `assertionSubjectName` . Ujistěte se, že **NameId** je první hodnotou v XML kontrolního výrazu. Při definování více než jednoho kontrolního výrazu Azure AD B2C vybere hodnotu předmětu z posledního kontrolního výrazu.

Následující příklad ukazuje deklarace identity vrácené zprostředkovatelem identity SAML:

- Deklarace identity **issuerUserId** je namapovaná na deklaraci identity **assertionSubjectName** .
- Deklarace **first_name** je namapovaná na **danou** deklaraci identity.
- Deklarace **last_name** je namapována na deklaraci identity na **příjmení** .
- Deklarace **DisplayName** je namapovaná na deklaraci identity **názvu** .
- Deklarace **e-mailu** bez mapování názvů

Technický profil také vrací deklarace identity, které nejsou vráceny zprostředkovatelem identity:

- Deklarace identity **identityProvider** , která obsahuje název poskytovatele identity.
- Deklarace identity **authenticationSource** s výchozí hodnotou **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email"  />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.

## <a name="metadata"></a>Metadata

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| PartnerEntity | Ano | Adresa URL metadat zprostředkovatele identity SAML. Kopírovat metadata zprostředkovatele identity a přidat je dovnitř elementu CDATA `<![CDATA[Your IDP metadata]]>` |
| WantsSignedRequests | Ne | Určuje, jestli technický profil vyžaduje, aby všechny odchozí požadavky na ověřování byly podepsané. Možné hodnoty: `true` nebo `false` . Výchozí hodnota je `true`. Pokud je hodnota nastavena na `true` , je nutné zadat kryptografický klíč **SamlMessageSigning** a všechny odchozí žádosti o ověření budou podepsány. Pokud je hodnota nastavena na `false` , jsou z požadavku vynechány parametry **SigAlg** a **signatury** (řetězec dotazu nebo parametr post). Tato metadata také řídí atribut **AuthnRequestsSigned** metadat, který je výstupem v metadatech Azure AD B2C Technical profil, který je sdílen s poskytovatelem identity. Azure AD B2C nepodepisuje požadavek, pokud je hodnota **WantsSignedRequests** v metadatech technického profilu nastavená na `false` a **WantAuthnRequestsSigned** metadata poskytovatele identity je nastavená na `false` nebo není zadaná. |
| XmlSignatureAlgorithm | Ne | Metoda, kterou Azure AD B2C používá k podepsání požadavku SAML. Tato metadata řídí hodnotu parametru  **SigAlg** (řetězec dotazu nebo parametr post) v požadavku SAML. Možné hodnoty: `Sha256` , `Sha384` , `Sha512` nebo `Sha1` (výchozí). Nezapomeňte nakonfigurovat algoritmus podpisu na obou stranách se stejnou hodnotou. Používejte jenom algoritmus, který podporuje váš certifikát. |
| WantsSignedAssertions | Ne | Určuje, zda technický profil vyžaduje, aby byly všechny příchozí kontrolní výrazy podepsány. Možné hodnoty: `true` nebo `false` . Výchozí hodnota je `true`. Pokud je hodnota nastavena na `true` , `saml:Assertion` musí být podepsána část všech kontrolních výrazů odeslaných zprostředkovatelem identity Azure AD B2C. Pokud je hodnota nastavena na `false` , zprostředkovatel identity by neměl podepsat kontrolní výrazy, ale i v případě, Azure AD B2C podpis neověřuje. Tato metadata také řídí příznak **WantsAssertionsSigned**(metadata), který je výstupem v metadatech Azure AD B2C Technical profil, který je sdílen s poskytovatelem identity. Pokud zakážete ověřování kontrolních výrazů, můžete také chtít zakázat ověřování podpisů odpovědí (Další informace najdete v tématu **ResponsesSigned**). |
| ResponsesSigned | Ne | Možné hodnoty: `true` nebo `false` . Výchozí hodnota je `true`. Pokud je hodnota nastavená na `false` , zprostředkovatel identity by neměl podepsat odpověď SAML, ale i v případě, Azure AD B2C neověřuje signaturu. Pokud je hodnota nastavena na `true` , je odpověď SAML poslaná zprostředkovatelem identity Azure AD B2C podepsaná a musí být ověřena. Pokud zakážete ověřování odpovědí SAML, můžete také chtít zakázat ověřování signatury kontrolního výrazu (Další informace najdete v tématu **WantsSignedAssertions**). |
| WantsEncryptedAssertions | Ne | Určuje, jestli technický profil vyžaduje, aby všechny příchozí kontrolní výrazy byly šifrované. Možné hodnoty: `true` nebo `false` . Výchozí hodnota je `false`. Je-li hodnota nastavena na hodnotu `true` , musí být pozměněny kontrolní výrazy odeslané zprostředkovatelem identity na Azure AD B2C a musí být zadán kryptografický klíč **SamlAssertionDecryption** . Pokud je hodnota nastavena na `true` , metadata Azure AD B2Cho technického profilu obsahují oddíl **šifrování** . Zprostředkovatel identity přečte metadata a zašifruje kontrolní výraz odpovědi SAML pomocí veřejného klíče, který je k dispozici v metadatech Azure AD B2C Technical Profile. Pokud povolíte šifrování kontrolních výrazů, může být také nutné zakázat ověřování podpisů odpovědí (Další informace najdete v tématu **ResponsesSigned**). |
| NameIdPolicyFormat | Ne | Určuje omezení pro identifikátor názvu, který se má použít k reprezentaci požadovaného předmětu. Je-li tento parametr vynechán, lze použít jakýkoli typ identifikátoru podporovaný zprostředkovatelem identity pro požadovaný předmět. Například `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. **NameIdPolicyFormat** se dá použít s **NameIdPolicyAllowCreate**. Pokyny k tomu, které zásady identifikátoru ID jsou podporované, najdete v dokumentaci poskytovatele identity. |
| NameIdPolicyAllowCreate | Ne | Při použití **NameIdPolicyFormat** můžete také zadat `AllowCreate` vlastnost **NameIDPolicy**. Hodnota těchto metadat je `true` nebo `false` označuje, zda může zprostředkovatel identity během přihlašování vytvořit nový účet. Pokyny k tomu, jak to udělat, najdete v dokumentaci poskytovatele identity. |
| AuthenticationRequestExtensions | Ne | Volitelné prvky rozšíření zprávy protokolu, které jsou dohodnuté mezi Azure AD BC a poskytovatelem identity. Přípona je prezentována ve formátu XML. Data XML přidáte do elementu CDATA `<![CDATA[Your IDP metadata]]>` . Zkontrolujte dokumentaci poskytovatele identity a zjistěte, jestli je element Extensions podporovaný. |
| IncludeAuthnContextClassReferences | Ne | Určuje jeden nebo více odkazů identifikátorů URI identifikujících třídy kontextu ověřování. Chcete-li například uživateli dovolit, aby se přihlásili pouze pomocí uživatelského jména a hesla, nastavte hodnotu na `urn:oasis:names:tc:SAML:2.0:ac:classes:Password` . Pokud chcete dovolit přihlášení prostřednictvím uživatelského jména a hesla přes chráněnou relaci (SSL/TLS), zadejte `PasswordProtectedTransport` . Pokyny k **AuthnContextClassRef** identifikátorům URI, které jsou podporované, najdete v dokumentaci poskytovatele identity. Zadejte více identifikátorů URI jako seznam oddělených čárkami. |
| IncludeKeyInfo | Ne | Určuje, jestli žádost o ověření SAML obsahuje veřejný klíč certifikátu, když je vazba nastavená na `HTTP-POST` . Možné hodnoty: `true` nebo `false` . |
| IncludeClaimResolvingInClaimsHandling  | Ne | Pro vstupní a výstupní deklarace identity určuje, jestli je [řešení deklarací identity](claim-resolver-overview.md) zahrnuté v technickém profilu. Možné hodnoty: `true` , nebo `false` (výchozí). Pokud chcete použít překladač deklarací identity v technickém profilu, nastavte tuto hodnotu na `true` . |
|SingleLogoutEnabled| Ne| Určuje, jestli se během přihlašování technického profilu pokusí odhlásit od zprostředkovatelů federovaných identit. Další informace najdete v tématu věnovaném [odhlášení Azure AD B2C relace](session-behavior.md#sign-out).  Možné hodnoty: `true` (výchozí), nebo `false` .|

## <a name="cryptographic-keys"></a>Kryptografické klíče

Element **CryptographicKeys** obsahuje následující atributy:

| Atribut |Povinné | Popis |
| --------- | ----------- | ----------- |
| SamlMessageSigning |Ano | Certifikát x509 (sada klíčů RSA), který se použije k podepisování zpráv SAML. Azure AD B2C používá tento klíč k podepsání žádostí a jejich odeslání poskytovateli identity. |
| SamlAssertionDecryption |Ne | Certifikát x509 (sada klíčů RSA) Zprostředkovatel identity SAML používá veřejnou část certifikátu k zašifrování kontrolního výrazu odpovědi SAML. Azure AD B2C používá soukromou část certifikátu k dešifrování kontrolního výrazu. |
| MetadataSigning |Ne | Certifikát x509 (sada klíčů RSA), který se použije k podepisování metadat SAML. Azure AD B2C používá tento klíč k podepsání metadat.  |

## <a name="saml-entityid-customization"></a>Přizpůsobení entityID SAML

Pokud máte více aplikací SAML závislých na různých hodnotách entityID, můžete přepsat `issueruri` hodnotu v souboru předávající strany. Provedete to tak, že zkopírujete technický profil s ID "Saml2AssertionIssuer" ze základního souboru a přepíšete `issueruri` hodnotu.

> [!TIP]
> Zkopírujte `<ClaimsProviders>` část ze základní třídy a zachovejte tyto prvky v rámci zprostředkovatele deklarací: `<DisplayName>Token Issuer</DisplayName>` , `<TechnicalProfile Id="Saml2AssertionIssuer">` a `<DisplayName>Token Issuer</DisplayName>` .
 
Příklad:

```xml
   <ClaimsProviders>   
    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Saml2AssertionIssuer">
          <DisplayName>Token Issuer</DisplayName>
          <Metadata>
            <Item Key="IssuerUri">customURI</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  </ClaimsProviders>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpInSAML" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2" />
      <Metadata>
     …
```

## <a name="next-steps"></a>Další kroky

Příklady práce se zprostředkovateli identity SAML v Azure AD B2C najdete v následujících článcích:

- [Přidání AD FS jako zprostředkovatele identity SAML pomocí vlastních zásad](identity-provider-adfs.md)
- [Přihlaste se pomocí účtů Salesforce přes SAML.](identity-provider-salesforce-saml.md)
