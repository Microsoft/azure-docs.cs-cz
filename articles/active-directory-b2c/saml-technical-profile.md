---
title: Definování technického profilu SAML ve vlastních zásadách v Azure Active Directory B2C
description: Definujte technický profil SAML ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 11/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b75367a90ce557f055ff4a9b1ff85f5b1f8f9637
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603062"
---
# <a name="define-a-saml-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu SAML v Azure Active Directory B2C vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) poskytuje podporu pro poskytovatele identity SAML 2,0. Tento článek popisuje konkrétní technické profily pro interakci se zprostředkovatelem deklarací, který podporuje tento standardizovaný protokol. S technickým profilem SAML můžete federovat poskytovatelem identity založeného na SAML, jako je [ADFS](active-directory-b2c-custom-setup-adfs2016-idp.md) nebo [Salesforce](active-directory-b2c-setup-sf-app-custom.md). Tato federace umožňuje vašim uživatelům přihlašovat se svými stávajícími sociálními nebo podnikovými identitami.

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
3. Nastavte **WantsEncryptedAssertions** metadata technického profilu na `true`.
4. Aktualizujte zprostředkovatele identity pomocí nových metadat profilu Azure AD B2C Technical Profile. Měl by se zobrazit **popisovač** klíče s vlastností **Use** nastavenou na `encryption` obsahující veřejný klíč certifikátu.

Následující příklad ukazuje část Azure AD B2C šifrování profilu technického profilu v metadatech:

```XML
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

## <a name="protocol"></a>Protocol (Protokol)

Atribut **Name** elementu Protocol musí být nastaven na `SAML2`.

## <a name="output-claims"></a>Deklarace výstupů

Element **OutputClaims** obsahuje seznam deklarací identity vrácených zprostředkovatelem identity SAML v části `AttributeStatement`. Možná budete muset namapovat název deklarace identity definované v zásadě na název definovaný v poskytovateli identity. Můžete také zahrnout deklarace identity, které nevrací poskytovatel identity, pokud nastavíte atribut `DefaultValue`.

Pokud chcete číst kontrolní výraz SAML **NamedId** v **předmětu** jako normalizovanou deklaraci identity, nastavte **PartnerClaimType** deklarace identity na `assertionSubjectName`. Ujistěte se, že **NameId** je první hodnotou v XML kontrolního výrazu. Při definování více než jednoho kontrolního výrazu Azure AD B2C vybere hodnotu předmětu z posledního kontrolního výrazu.

Element **OutputClaimsTransformations** může obsahovat kolekci prvků **OutputClaimsTransformation** , které se používají k úpravě výstupních deklarací identity nebo k vygenerování nových.

Následující příklad ukazuje deklarace identity, které vrátil poskytovatel identity Facebooku:

- Deklarace identity **issuerUserId** je namapovaná na deklaraci identity **assertionSubjectName** .
- Deklarace **křestní_jméno křestní_jméno** je namapovaná na **danou** deklaraci identity.
- Deklarace **příjmení** je namapovaná na **přidanou** deklaraci.
- Parametr **DisplayName** deklarace identity bez mapování názvu.
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

## <a name="metadata"></a>Metadata

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| PartnerEntity | Ano | Adresa URL metadat zprostředkovatele identity SAML. Zkopírujte metadata zprostředkovatele identity a přidejte je do elementu CDATA `<![CDATA[Your IDP metadata]]>` |
| WantsSignedRequests | Ne | Určuje, jestli technický profil vyžaduje, aby všechny odchozí požadavky na ověřování byly podepsané. Možné hodnoty: `true` nebo `false`. Výchozí hodnota je `true`. Pokud je hodnota nastavena na `true`, je nutné zadat kryptografický klíč **SamlMessageSigning** a všechny odchozí žádosti o ověření budou podepsány. Pokud je hodnota nastavena na `false`, jsou z požadavku vynechány parametry **SigAlg** a **signatury** (řetězec dotazu nebo parametr post). Tato metadata také řídí atribut **AuthnRequestsSigned** metadat, který je výstupem v metadatech Azure AD B2C Technical profil, který je sdílen s poskytovatelem identity. Azure AD B2C nepodepisuje požadavek, pokud je hodnota **WantsSignedRequests** v metadatech technického profilu nastavená na `false` a metadata poskytovatele identity **WantAuthnRequestsSigned** je nastavená na `false` nebo není zadaná. |
| XmlSignatureAlgorithm | Ne | Metoda, kterou Azure AD B2C používá k podepsání požadavku SAML. Tato metadata řídí hodnotu parametru **SigAlg** (řetězec dotazu nebo parametr post) v požadavku SAML. Možné hodnoty: `Sha256`, `Sha384`, `Sha512`nebo `Sha1`. Nezapomeňte nakonfigurovat algoritmus podpisu na obou stranách se stejnou hodnotou. Používejte jenom algoritmus, který podporuje váš certifikát. |
| WantsSignedAssertions | Ne | Určuje, zda technický profil vyžaduje, aby byly všechny příchozí kontrolní výrazy podepsány. Možné hodnoty: `true` nebo `false`. Výchozí hodnota je `true`. Pokud je hodnota nastavená na `true`, musí být podepsány všechny části kontrolní výrazy `saml:Assertion` odesílané zprostředkovatelem identity do Azure AD B2C. Pokud je hodnota nastavena na `false`, zprostředkovatel identity by neměl podepsat kontrolní výrazy, ale i v případě, Azure AD B2C signaturu neověřuje. Tato metadata také řídí příznak **WantsAssertionsSigned**(metadata), který je výstupem v metadatech Azure AD B2C Technical profil, který je sdílen s poskytovatelem identity. Pokud zakážete ověřování kontrolních výrazů, můžete také chtít zakázat ověřování podpisů odpovědí (Další informace najdete v tématu **ResponsesSigned**). |
| ResponsesSigned | Ne | Možné hodnoty: `true` nebo `false`. Výchozí hodnota je `true`. Pokud je hodnota nastavená na `false`, zprostředkovatel identity by neměl podepsat odpověď SAML, ale i v případě, že to dělá, Azure AD B2C signaturu neověří. Pokud je hodnota nastavená na `true`, odpověď SAML, kterou poskytovatel identity pošle do Azure AD B2C, je podepsaná a musí se ověřit. Pokud zakážete ověřování odpovědí SAML, můžete také chtít zakázat ověřování signatury kontrolního výrazu (Další informace najdete v tématu **WantsSignedAssertions**). |
| WantsEncryptedAssertions | Ne | Určuje, jestli technický profil vyžaduje, aby všechny příchozí kontrolní výrazy byly šifrované. Možné hodnoty: `true` nebo `false`. Výchozí hodnota je `false`. Pokud je hodnota nastavená na `true`, musí být pro Azure AD B2C podepsané kontrolní výrazy odeslané zprostředkovatelem identity a musí se zadat **SamlAssertionDecryption** kryptografický klíč. Pokud je hodnota nastavená na `true`, metadata Azure AD B2Cho technického profilu obsahují oddíl **šifrování** . Zprostředkovatel identity přečte metadata a zašifruje kontrolní výraz odpovědi SAML pomocí veřejného klíče, který je k dispozici v metadatech Azure AD B2C Technical Profile. Pokud povolíte šifrování kontrolních výrazů, může být také nutné zakázat ověřování podpisů odpovědí (Další informace najdete v tématu **ResponsesSigned**). |
| IdpInitiatedProfileEnabled | Ne | Uvádí, zda je povolen profil relace jednotného přihlašování, který byl iniciován profilem zprostředkovatele identity SAML. Možné hodnoty: `true` nebo `false`. Výchozí formát je `false`. V toku iniciované poskytovatelem identity je uživatel ověřen externě a pošle se nevyžádaná odpověď do Azure AD B2C, která pak tento token využívá, spouští kroky Orchestrace a poté pošle odpověď na aplikaci předávající strany. |
| NameIdPolicyFormat | Ne | Určuje omezení pro identifikátor názvu, který se má použít k reprezentaci požadovaného předmětu. Je-li tento parametr vynechán, lze použít jakýkoli typ identifikátoru podporovaný zprostředkovatelem identity pro požadovaný předmět. Například `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. **NameIdPolicyFormat** se dá použít s **NameIdPolicyAllowCreate**. Pokyny k tomu, které zásady identifikátoru ID jsou podporované, najdete v dokumentaci poskytovatele identity. |
| NameIdPolicyAllowCreate | Ne | Při použití **NameIdPolicyFormat**můžete také zadat vlastnost `AllowCreate` **NameIDPolicy**. Hodnota těchto metadat je `true` nebo `false` k označení, zda může zprostředkovatel identity během přihlašování vytvořit nový účet. Pokyny k tomu, jak to udělat, najdete v dokumentaci poskytovatele identity. |
| AuthenticationRequestExtensions | Ne | Volitelné prvky rozšíření zprávy protokolu, které jsou dohodnuté mezi Azure AD BC a poskytovatelem identity. Přípona je prezentována ve formátu XML. Data XML přidáte do elementu CDATA `<![CDATA[Your IDP metadata]]>`. Zkontrolujte dokumentaci poskytovatele identity a zjistěte, jestli je element Extensions podporovaný. |
| IncludeAuthnContextClassReferences | Ne | Určuje jeden nebo více odkazů identifikátorů URI identifikujících třídy kontextu ověřování. Chcete-li například uživateli dovolit, aby se přihlásili pouze pomocí uživatelského jména a hesla, nastavte hodnotu na `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`. Pokud chcete dovolit přihlášení pomocí uživatelského jména a hesla přes chráněnou relaci (SSL/TLS), zadejte `PasswordProtectedTransport`. Pokyny k **AuthnContextClassRef** identifikátorům URI, které jsou podporované, najdete v dokumentaci poskytovatele identity. Zadejte více identifikátorů URI jako seznam oddělených čárkami. |
| IncludeKeyInfo | Ne | Určuje, jestli žádost o ověření SAML obsahuje veřejný klíč certifikátu, když je vazba nastavená na `HTTP-POST`. Možné hodnoty: `true` nebo `false`. |

## <a name="cryptographic-keys"></a>Kryptografické klíče

Element **CryptographicKeys** obsahuje následující atributy:

| Atribut |Požaduje se | Popis |
| --------- | ----------- | ----------- |
| SamlMessageSigning |Ano | Certifikát x509 (sada klíčů RSA), který se použije k podepisování zpráv SAML. Azure AD B2C používá tento klíč k podepsání žádostí a jejich odeslání poskytovateli identity. |
| SamlAssertionDecryption |Ano | Certifikát x509 (sada klíčů RSA), který se použije k dešifrování zpráv SAML. Tento certifikát by měl poskytovat poskytovatel identity. Azure AD B2C používá tento certifikát k dešifrování dat odesílaných poskytovatelem identity. |
| MetadataSigning |Ne | Certifikát x509 (sada klíčů RSA), který se použije k podepisování metadat SAML. Azure AD B2C používá tento klíč k podepsání metadat.  |

## <a name="next-steps"></a>Další kroky

Příklady práce se zprostředkovateli identity SAML v Azure AD B2C najdete v následujících článcích:

- [Přidání AD FS jako zprostředkovatele identity SAML pomocí vlastních zásad](active-directory-b2c-custom-setup-adfs2016-idp.md)
- [Přihlaste se pomocí účtů Salesforce přes SAML.](active-directory-b2c-setup-sf-app-custom.md)
