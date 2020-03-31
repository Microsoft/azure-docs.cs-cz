---
title: Definování technického profilu SAML ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Ve vlastní chvilce služby Azure Active Directory B2C definujte technický profil SAML ve vlastních zásadách.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8c81d2bc499c3d9cae262ef62be2dac2d7280be7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183835"
---
# <a name="define-a-saml-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu SAML ve vlastních zásadách služby Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) poskytuje podporu pro poskytovatele identity SAML 2.0. Tento článek popisuje specifika technického profilu pro interakci s poskytovatelem deklarací identity, který podporuje tento standardizovaný protokol. S technickým profilem SAML můžete federate s poskytovatelem identity na bázi SAML, jako jsou [ADFS](identity-provider-adfs2016-custom.md) a [Salesforce](identity-provider-salesforce-custom.md). Tato federace umožňuje uživatelům přihlásit se pomocí svých stávajících sociálních nebo podnikových identit.

## <a name="metadata-exchange"></a>Výměna metadat

Metadata jsou informace používané v protokolu SAML k vystavení konfigurace strany SAML, například poskytovatele služeb nebo zprostředkovatele identity. Metadata definují umístění služeb, jako je například přihlášení a odhlášení, certifikáty, způsob přihlášení a další. Zprostředkovatel identity používá metadata vědět, jak komunikovat s Azure AD B2C. Metadata jsou konfigurována ve formátu XML a mohou být podepsána digitálním podpisem, aby druhá strana mohla ověřit integritu metadat. Když Azure AD B2C federates s poskytovatelem identity SAML, funguje jako poskytovatel služeb, který iniciuje požadavek SAML a čeká na odpověď SAML. A v některých případech přijímá nevyžádané ověřování SAML, které je také známé jako iniciovaný zprostředkovatel identity.

Metadata lze nakonfigurovat v obou stranách jako "Statická metadata" nebo "Dynamická metadata". Ve statickém režimu zkopírujete celá metadata z jedné strany a nastavte je v druhé straně. V dynamickém režimu nastavíte adresu URL na metadata, zatímco druhá strana dynamicky čte konfiguraci. Principy jsou stejné, nastavíte metadata technického profilu Azure AD B2C ve vašem zprostředkovateli identity a nastavíte metadata poskytovatele identity v Azure AD B2C.

Každý poskytovatel identity SAML má různé kroky k vystavení a nastavení poskytovatele služeb, v tomto případě Azure AD B2C a nastavit metadata Azure AD B2C u poskytovatele identity. Pokyny k tomu, jak to provést, naleznete v dokumentaci poskytovatele identity.

Následující příklad ukazuje adresu URL metadat SAML technického profilu Azure AD B2C:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Nahraďte následující hodnoty:

- **jméno vašeho tenanta** s názvem vašeho tenanta, například fabrikam.b2clogin.com.
- **zásady** s vaším názvem zásad. Použijte zásadu, kde nakonfigurujete technický profil zprostředkovatele SAML, nebo zásadu, která dědí z této zásady.
- **váš technický profil** s názvem technického profilu poskytovatele identity SAML.

## <a name="digital-signing-certificates-exchange"></a>Výměna digitálních podpisových certifikátů

Chcete-li vytvořit vztah důvěryhodnosti mezi Azure AD B2C a poskytovatelem identity SAML, musíte poskytnout platný certifikát X509 s privátním klíčem. Nahrajete certifikát s privátním klíčem (soubor.pfx) do úložiště klíčů zásad Azure AD B2C. Azure AD B2C digitálně podepíše požadavek na přihlášení SAML pomocí certifikátu, který zadáte.

Certifikát se používá následujícími způsoby:

- Azure AD B2C generuje a podepisuje požadavek SAML pomocí privátního klíče Azure AD B2C certifikátu. Požadavek SAML je odeslána poskytovateli identity, který ověří požadavek pomocí veřejného klíče Azure AD B2C certifikátu. Veřejný certifikát Azure AD B2C je přístupný prostřednictvím metadat technického profilu. Případně můžete ručně nahrát soubor CER do svého zprostředkovatele identity SAML.
- Zprostředkovatel identity podepíše data odeslaná do Azure AD B2C pomocí soukromého klíče poskytovatele identity certifikátu. Azure AD B2C ověřuje data pomocí veřejného certifikátu poskytovatele identity. Každý poskytovatel identity má různé kroky pro nastavení, podívejte se na dokumentaci poskytovatele identity, kde najak to udělat. Ve službě Azure AD B2C vyžaduje vaše zásady přístup k veřejnému klíči certifikátu pomocí metadat poskytovatele identity.

Certifikát podepsaný svým držitelem je přijatelný pro většinu scénářů. Pro produkční prostředí se doporučuje použít certifikát X509 vydaný certifikační autoritou. Také, jak je popsáno dále v tomto dokumentu, pro neprodukční prostředí můžete zakázat podepisování SAML na obou stranách.

Následující diagram znázorňuje metadata a výměnu certifikátů:

![metadata a výměna certifikátů](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)

## <a name="digital-encryption"></a>Digitální šifrování

Chcete-li zašifrovat kontrolní výraz odpovědi SAML, poskytovatel identity vždy používá veřejný klíč šifrovacího certifikátu v technickém profilu Azure AD B2C. Když Azure AD B2C potřebuje dešifrovat data, používá privátní část šifrovacího certifikátu.

Chcete-li zašifrovat kontrolní výraz odpovědi SAML:

1. Nahrajte platný certifikát X509 s privátním klíčem (soubor.pfx) do úložiště klíčů zásad Azure AD B2C.
2. Přidejte prvek **CryptographicKey** s `SamlAssertionDecryption` identifikátorem do kolekce **kryptografických klíčů** technického profilu. Nastavte **StorageReferenceId** na název klíče zásad, který jste vytvořili v kroku 1.
3. Nastavte metadata technického profilu **WantsEncryptedAssertions** na `true`.
4. Aktualizujte poskytovatele identity pomocí nových metadat technického profilu Azure AD B2C. Měli byste vidět **KeyDescriptor** s **vlastností use** nastavenou tak, aby `encryption` obsahovala veřejný klíč certifikátu.

Následující příklad ukazuje část šifrování technického profilu Azure AD B2C v metadatech:

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

**Atribut Name** elementu Protocol musí být `SAML2`nastaven na .

## <a name="output-claims"></a>Výstupní pohledávky

**OutputClaims** Element obsahuje seznam deklarací vrácených poskytovatelem identity SAML v části. `AttributeStatement` Možná budete muset namapovat název deklarace identity definované v zásadách na název definovaný v poskytovateli identity. Můžete také zahrnout deklarace identity, které nejsou vráceny zprostředkovatelem identity tak dlouho, dokud nastavíte `DefaultValue` atribut.

Chcete-li číst kontrolní výraz SAML **NamedId** v **předmětu** jako `assertionSubjectName`normalizovanou deklaraci, nastavte **deklaraci PartnerClaimType** na . Ujistěte se, **že NameId** je první hodnota v kontrolním výrazu XML. Když definujete více než jeden kontrolní výraz, Azure AD B2C vybere hodnotu předmětu z poslední kontrolní výraz.

**OutputClaimsTransformations** Element může obsahovat kolekci **OutputClaimsTransformation** prvky, které se používají k úpravě výstupní deklarace nebo generovat nové.

Následující příklad ukazuje deklarace identity vrácené poskytovatelem identity na Facebooku:

- Deklarace **issuerUserId** je mapována na deklaraci **assertionSubjectName.**
- Deklarace **first_name** je mapována na deklaraci **givenName.**
- Nárok **last_name** je mapován na nárok na **příjmení.**
- Deklarace **displayName** bez mapování názvu.
- Deklarace **e-mailu** bez mapování názvu.

Technický profil také vrátí deklarace identity, které nejsou vráceny zprostředkovatelem identity:

- **IdentityProvider** deklarace, která obsahuje název zprostředkovatele identity.
- Deklarace **authenticationSource** s výchozí hodnotou **socialIdpAuthentication**.

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
| Partnerská entita | Ano | ADRESA URL metadat zprostředkovatele identity SAML. Zkopírujte metadata zprostředkovatele identity a přidejte je do elementu CDATA.`<![CDATA[Your IDP metadata]]>` |
| ChcePodepsanéPožadavky | Ne | Označuje, zda technický profil vyžaduje podepsání všech odchozích požadavků na ověření. Možné `true` hodnoty: `false`nebo . Výchozí hodnota je `true`. Pokud je hodnota `true`nastavena na , je třeba zadat kryptografický klíč **SamlMessageSigning** a podepsat všechny odchozí požadavky na ověření. Pokud je hodnota `false`nastavena na , parametry **SigAlg** a **Signature** (parametr řetězce dotazu nebo parametr post) jsou z požadavku vynechány. Tato metadata také řídí metadata **AuthnRequestsSigned** atribut, který je výstup v metadatech technického profilu Azure AD B2C, který je sdílen s poskytovatelem identity. Azure AD B2C nepodepíše požadavek, pokud je nastavena hodnota **WantsSignedRequests** v metadatech technického profilu `false` a metadata zprostředkovatele identity **WantAuthnRequestsSigned** je nastavena nebo `false` není zadána. |
| XmlSignatureAlgorithm | Ne | Metoda, která používá Azure AD B2C k podepsání požadavku SAML. Tato metadata řídí hodnotu parametru **SigAlg** (řetězec dotazu nebo parametr post) v požadavku SAML. Možné `Sha256`hodnoty: `Sha384` `Sha512`, `Sha1`, , nebo . Ujistěte se, že nakonfigurujete podpisový algoritmus na obou stranách se stejnou hodnotou. Používejte pouze algoritmus, který váš certifikát podporuje. |
| WantsSignedAssertions | Ne | Označuje, zda technický profil vyžaduje podepsání všech příchozích kontrolních výrazů. Možné `true` hodnoty: `false`nebo . Výchozí hodnota je `true`. Pokud je hodnota `true`nastavena na `saml:Assertion` , musí být podepsány všechny kontrolní oddíly odeslané poskytovatelem identity do Azure AD B2C. Pokud je hodnota `false`nastavena na , zprostředkovatel identity by neměl podepsat kontrolní výrazy, ale i v případě, že ano, Azure AD B2C neověří podpis. Tato metadata také řídí příznak metadat **WantsAssertionsSigned**, který je výstupem v metadatech technického profilu Azure AD B2C, který je sdílen s poskytovatelem identity. Pokud zakážete ověření kontrolních výrazů, můžete také zakázat ověření podpisu odpovědi (další informace naleznete v **tématu ResponsesSigned**). |
| Podepsané odpovědi | Ne | Možné `true` hodnoty: `false`nebo . Výchozí hodnota je `true`. Pokud je hodnota `false`nastavena na , poskytovatel identity by neměl podepsat odpověď SAML, ale i v případě, že ano, Azure AD B2C neověří podpis. Pokud je hodnota `true`nastavena na , odpověď SAML odeslaná poskytovatelem identity do Azure AD B2C je podepsána a musí být ověřena. Pokud zakážete ověření odpovědi SAML, můžete také zakázat ověření podpisu kontrolního výrazu (další informace naleznete v tématu **WantsSignedAssertions**). |
| ChceEncryptedAssertions | Ne | Označuje, zda technický profil vyžaduje šifrování všech příchozích kontrolních výrazů. Možné `true` hodnoty: `false`nebo . Výchozí hodnota je `false`. Pokud je hodnota `true`nastavena na , kontrolní výrazy odeslané poskytovatelem identity do Azure AD B2C musí být podepsány a **samlassertiondešterovací** kryptografický klíč musí být zadán. Pokud je hodnota `true`nastavena na , metadata technického profilu Azure AD B2C zahrnuje část **šifrování.** Zprostředkovatel identity přečte metadata a zašifruje kontrolní výraz odpovědi SAML s veřejným klíčem, který je k dispozici v metadatech technického profilu Azure AD B2C. Pokud povolíte šifrování kontrolních výrazů, bude také nutné zakázat ověření podpisu odpovědi (další informace naleznete v **tématu ResponsesSigned**). |
| IdpInitiatedProfileEnabled | Ne | Označuje, zda je povolen profil relace jednotného přihlášení, který byl iniciován profilem zprostředkovatele identity SAML. Možné `true` hodnoty: `false`nebo . Výchozí formát je `false`. V toku iniciovaném zprostředkovatelem identity je uživatel ověřen externě a nevyžádaná odpověď je odeslána do Azure AD B2C, která pak spotřebovává token, provede kroky orchestrace a odešle odpověď do aplikace předávající strany. |
| NázevIdPolicyFormat | Ne | Určuje omezení identifikátoru názvu, který má být použit k reprezentaci požadovaného subjektu. Pokud je vynechán, lze použít libovolný typ identifikátoru podporovaný zprostředkovatelem identity pro požadovaný subjekt. Například `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. **NameIdPolicyFormat** lze použít s **NameIdPolicyAllowCreate**. Pokyny k tomu, které zásady ID názvů jsou podporovány, naleznete v dokumentaci poskytovatele identity. |
| NameIdPolicyAllowCreate | Ne | Při použití **nameidpolicyformat**můžete také `AllowCreate` zadat vlastnost **NameIDPolicy**. Hodnota těchto metadat je `true` `false` nebo označuje, zda zprostředkovatel identity je povoleno vytvořit nový účet během toku přihlášení. Pokyny k tomu, jak to provést, naleznete v dokumentaci poskytovatele identity. |
| Rozšíření AuthenticationRequestExtensions | Ne | Prvky rozšíření signálu volitelný protokol, které jsou dohodnuty mezi Azure AD BC a zprostředkovatele identity. Rozšíření je prezentováno ve formátu XML. Data XML přidáte do elementu `<![CDATA[Your IDP metadata]]>`CDATA . Zkontrolujte dokumentaci poskytovatele identity a zjistěte, zda je prvek rozšíření podporován. |
| IncludeAuthnContextClassReferences | Ne | Určuje jeden nebo více odkazů IDENTIFIKÁTORU URI identifikujících třídy kontextu ověřování. Chcete-li například umožnit uživateli přihlásit se pouze pomocí `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`uživatelského jména a hesla, nastavte hodnotu na . Chcete-li povolit přihlášení prostřednictvím uživatelského jména a hesla přes chráněnou relaci (SSL/TLS), zadejte `PasswordProtectedTransport`. Podívejte se na dokumentaci poskytovatele identity pro pokyny o **AuthnContextClassRef** URI, které jsou podporovány. Zadejte více identifikátorů URI jako seznam oddělený čárkami. |
| IncludeKeyInfo | Ne | Označuje, zda požadavek na ověření SAML obsahuje veřejný klíč `HTTP-POST`certifikátu, pokud je vazba nastavena na . Možné `true` hodnoty: `false`nebo . |
| IncludeClaimResolvingInClaimsHandling  | Ne | Pro vstupní a výstupní deklarace určuje, zda je [řešení deklarací](claim-resolver-overview.md) zahrnuto do technického profilu. Možné hodnoty: `true` `false`  , nebo (výchozí). Pokud chcete použít překladač deklarací identity v `true`technickém profilu, nastavte toto na . |

## <a name="cryptographic-keys"></a>Kryptografické klíče

Prvek **CryptographicKeys** obsahuje následující atributy:

| Atribut |Požaduje se | Popis |
| --------- | ----------- | ----------- |
| SamlMessagePodpis |Ano | Certifikát X509 (sada klíčů RSA), který se má použít k podepisování zpráv SAML. Azure AD B2C používá tento klíč k podepsání požadavků a jejich odeslání zprostředkovateli identity. |
| SamlAssertionDešifrování |Ano | Certifikát X509 (sada klíčů RSA), který se používá k dešifrování zpráv SAML. Tento certifikát by měl být poskytnut poskytovatelem identity. Azure AD B2C používá tento certifikát k dešifrování dat odeslaných poskytovatelem identity. |
| Podepisování metadat |Ne | Certifikát X509 (sada klíčů RSA), který se má použít k podepisování metadat SAML. Azure AD B2C používá tento klíč k podepsání metadat.  |

## <a name="next-steps"></a>Další kroky

V následujících článcích najdete příklady práce s poskytovateli identit SAML v Azure AD B2C:

- [Přidání služby ADFS jako zprostředkovatele identity SAML pomocí vlastních zásad](identity-provider-adfs2016-custom.md)
- [Přihlaste se pomocí účtů Salesforce přes SAML](identity-provider-salesforce-custom.md)
