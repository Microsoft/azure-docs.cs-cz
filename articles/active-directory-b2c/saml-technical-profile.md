---
title: Technický profil SAML definování ve vlastních zásadách v Azure Active Directory B2C | Dokumentace Microsoftu
description: Technický profil SAML definování ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/21/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: d5120b7569acbe9735ea1a70fcb609d322d60793
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55154367"
---
# <a name="define-a-saml-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Technický profil SAML definování ve vlastních zásadách pro Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C poskytuje podporu pro zprostředkovatele identity SAML 2.0. Tento článek popisuje, jaké jsou specifikace technický profil pro interakci s zprostředkovatele deklarací identity, která podporuje tento protokol standardizované. S technický profil SAML vám může provést federaci s zprostředkovatele identity založené na SAML, jako například [služby AD FS](active-directory-b2c-custom-setup-adfs2016-idp.md) a [Salesforce](active-directory-b2c-setup-sf-app-custom.md). Tuto federaci umožňuje uživatelům se přihlaste se pomocí své stávající sociálních sítí nebo podnikové identity.

## <a name="metadata-exchange"></a>Metadata exchange

Metadata jsou informace, které používají protokol SAML k vystavení konfiguraci strany SAML, jako je například služba zprostředkovatele nebo zprostředkovatele identity. Metadata definuje umístění služeb, jako metodu přihlašování, přihlašování a odhlašování, certifikáty a další. Zprostředkovatel identity používá metadata k určení, jak ke komunikaci s Azure AD B2C. Metadata je nakonfigurována ve formátu XML a může být podepsané digitálním podpisem tak, aby druhou stranu může ověřit integritu metadat. Azure AD B2C federuje s zprostředkovatele identity SAML, funguje jako poskytovatel služeb zahajování požadavek SAML a čekání na odpověď SAML. A v některých případech přijímá nevyžádané ověřování SAML, která je také označována jako zprostředkovatele identity iniciované. 

Metadata se dá nakonfigurovat v obou stran jako "Statické Metadata" nebo "Dynamická Metadata". V režimu statické zkopírujte celý metadat z jedné strany a nastavte ji v druhé strany. V dynamickém režimu nastavit adresu URL metadat během druhé strany načte konfiguraci dynamicky. Zásady jsou stejné, nastavte metadata technický profil Azure AD B2C ve zprostředkovateli identity a nastavte metadat zprostředkovatele identity v Azure AD B2C.

Každý poskytovatel identity SAML má jiný postup k vystavení a nastavte poskytovatele služeb v tomto případě Azure AD B2C a Azure AD B2C metadata sady ve zprostředkovateli identity. Podívejte se na dokumentaci poskytovatele identity pokyny, jak postupovat.

Následující příklad ukazuje adresu URL metadat SAML technického profilu Azure AD B2C:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Nahraďte následující hodnoty:

- **váš název tenanta** názvem vašeho tenanta, jako je například fabrikam.b2clogin.com.
- **vaše zásady** názvem zásady. Pomocí zásad, které nakonfigurujete technický profil SAML zprostředkovatele nebo zásad, která dědí z této zásadě.
- **váš technický profil** s názvem technického profilu poskytovatele identity SAML.

## <a name="digital-signing-certificates-exchange"></a>Digitální podepisování certifikátů exchange

Pokud chcete vytvořit vztah důvěryhodnosti mezi Azure AD B2C a zprostředkovatele identity SAML, budete muset zadat platné X509 certifikát s privátním klíčem. Nahrajte certifikát s privátním klíčem (soubor .pfx) pro úložiště klíčů zásady Azure AD B2C. Azure AD B2C digitálně podepíše SAML žádost o přihlášení pomocí certifikátu, který zadáte. 

Certifikát se používá takto:

- Azure AD B2C generuje a podepisuje požadavek SAML, pomocí Azure AD B2C privátní klíč certifikátu. Posílá požadavek SAML zprostředkovatele identity, která ověřuje požadavek pomocí Azure AD B2C veřejný klíč certifikátu. Veřejný certifikát Azure AD B2C je přístupná prostřednictvím technického profilu. Alternativně můžete ručně nahrát soubor .cer do vašeho zprostředkovatele identity SAML.
- Zprostředkovatel identity podepíše data odeslaná do Azure AD B2C pomocí zprostředkovatele identity privátní klíč certifikátu. Azure AD B2C ověří data pomocí veřejného certifikátu zprostředkovatele identity. Každý poskytovatel identity má jiný postup instalace, podívejte se na dokumentaci poskytovatele identity pokyny, jak postupovat. Vaše zásady v Azure AD B2C, potřebuje přístup k veřejným klíčem certifikátu pomocí metadat zprostředkovatele identity.

Certifikát podepsaný svým držitelem je přijatelné pro většinu scénářů. Pro produkční prostředí doporučujeme použít x X509 certifikátu vydaného certifikační autoritou. Také jak je popsáno dále v tomto dokumentu, mimo produkční prostředí můžete zakázat na obou stranách přihlašování SAML.

Následující diagram znázorňuje výměny metadat a certifikátu:

![Výměna metadat a certifikátů](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)


## <a name="digital-encryption"></a>Digitální šifrování

K šifrování kontrolního výrazu SAML odpovědi, zprostředkovatel identity vždy používá veřejný klíč certifikátu šifrování v technickém profilu Azure AD B2C. Pokud Azure AD B2C potřebuje k dešifrování dat, používá soukromá část šifrovací certifikát.

K šifrování kontrolního výrazu SAML odpovědi:

1. Nahrajte platný X509 certifikát s privátním klíčem (soubor .pfx) pro úložiště klíčů zásady Azure AD B2C.
2. Přidat **CryptographicKey** element s identifikátorem `SamlAssertionDecryption` na technický profil **CryptographicKeys** kolekce. Nastavte **StorageReferenceId** k názvu klíče zásad, kterou jste vytvořili v kroku 1.
3. Nastavte technického profilu **WantsEncryptedAssertions** k `true`.
4. Aktualizujte zprostředkovatele identity s novými metadaty technický profil Azure AD B2C. Měli byste vidět **KeyDescriptor** s **použít** nastavenou na `encryption` obsahující veřejný klíč certifikátu.

Následující příklad ukazuje část Azure AD B2C technický profil šifrování metadat:

```XML
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

## <a name="identity-provider-initiated-flow"></a>Tok iniciované zprostředkovatele identity

V jedné přihlašování relaci (nevyžádané žádosti) iniciovaných zprostředkovatele identity přijde nevyžádané odpověď SAML k poskytovateli služby v tomto případě technický profil Azure AD B2C. V tomto toku uživatele neprocházejí přes webovou aplikaci nejdřív ale se přesměruje na poskytovatele identity. Při odesílání požadavku na stránce ověřování je uživateli poskytnutý poskytovatelem identity. Uživatel nedokončí přihlášení a potom se přesměruje žádost na Azure AD B2C s odpověď SAML, který obsahuje kontrolní výrazy. Azure AD B2C přečte kontrolní výrazy a vydá nový token SAML a pak přesměruje uživatele zpět do aplikace předávající strany. Provádí přesměrování **AssertionConsumerService** elementu **umístění** vlastnost.


![SAML zprostředkovatele identity iniciované](media/saml-technical-profile/technical-profile-idp-saml-idp-initiated.png) 

Při vytváření zprostředkovatele identity spuštění toku vezměte v úvahu následující požadavky zásad:

- První krok Orchestrace musí být že jediný deklarací exchange, která odkazuje na technický profil SAML.
- Technický profil musí mít metadata položky s názvem **IdpInitiatedProfileEnabled** nastavena na `true`.
- Předávající strana zásad musí být přijímající strany SAML.
- Předávající strana zásada musí mít metadata položky s názvem **IdpInitiatedProfileEnabled** nastavena na `true`.
- Nevyžádané odpovědi musí být zaslána `/your-tenant/your-policy/samlp/sso/assertionconsumer` koncového bodu. Jakýkoli stav relay součástí odpovědi je předat dál předávající straně. Nahraďte následující hodnoty: **vašeho tenanta** názvem vašeho tenanta. **vaše zásady** názvem zásady pro předávající strany.
    
## <a name="protocol"></a>Protocol (Protokol)

**Název** atribut elementu protokol musí být nastavena na `SAML2`. 

## <a name="output-claims"></a>Výstupní deklarace identit.
 
**OutputClaims** prvek obsahuje seznam deklarací identity vrátí SAML zprostředkovatele identity v části `AttributeStatement` oddílu. Budete muset namapovat název deklarace identity, definovaný ve svojí zásadě název definovaný ve zprostředkovateli identity. Může také obsahovat deklarace identity, které nejsou vrácené zprostředkovatelem identity tak dlouho, dokud jste nastavili `DefaultValue` atribut.
 
Číst kontrolní výraz SAML **NamedId** v **subjektu** jako normalizované deklarace identity, nastavte deklarace identity **PartnerClaimType** k `assertionSubjectName`. Ujistěte se, **NameId** je první hodnota v kontrolní výraz XML. Při definování více než jeden výraz vybere Azure AD B2C hodnotu subjektu z posledního kontrolního výrazu.
 
**OutputClaimsTransformations** element může obsahovat kolekci **OutputClaimsTransformation** prvky, které se používají ke změně výstupní deklarace identit nebo generovat nové značky.
 
Následující příklad ukazuje deklarace vrácené poskytovatelem identity Facebooku:

- **SocialIdpUserId** deklarace identity se mapuje na **assertionSubjectName** deklarací identity.
- **Křestní_jméno** deklarace identity se mapuje na **givenName** deklarací identity.
- **Příjmení** deklarace identity se mapuje na **příjmení** deklarací identity.
- **DisplayName** deklarace identity bez mapování názvů.
- **E-mailu** deklarace identity bez mapování názvů.
 
Technický profil také vrátí hodnotu deklarace identity, které nejsou vrácené zprostředkovatelem identity: 
 
- **IdentityProvider** deklarace identity, který obsahuje název zprostředkovatele identity.
- **AuthenticationSource** deklarace identity s výchozí hodnotou **socialIdpAuthentication**.
 
```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="assertionSubjectName" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email"  />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadata

| Atribut | Požadováno | Popis |
| --------- | -------- | ----------- |
| PartnerEntity | Ano | Adresa URL metadat SAML zprostředkovatele identity. Zkopírujte metadat zprostředkovatele identity a přidejte do elementu CDATA `<![CDATA[Your IDP metadata]]>` |
| WantsSignedRequests | Ne | Určuje, zda technický profil vyžaduje všechny odchozí žádosti o ověření podepsat. Možné hodnoty: `true` nebo `false`. Výchozí hodnota je `true`. Pokud je hodnota nastavená na `true`, **SamlMessageSigning** kryptografický klíč se musí zadat a všechny odchozí žádosti o ověření přihlášení. Pokud je hodnota nastavena na `false`, **SigAlg** a **podpis** parametry (řetězec dotazu nebo odeslat parametr) jsou vynechány z požadavku. Tato metadata řídí také metadata **AuthnRequestsSigned** atribut, který je výstup v metadatech technický profil Azure AD B2C, jež jsou sdílena s tímto poskytovatelem identity. Azure AD B2C není podepsat žádost, pokud **WantsSignedRequests** v technickém profilu metadata nastavená na `false` a metadat zprostředkovatele identity **WantAuthnRequestsSigned** je nastavena na `false` nebo není zadaný. |
| XmlSignatureAlgorithm | Ne | Metoda, která používá Azure AD B2C k podpisu požadavku SAML. Tato metadata řídí hodnota **SigAlg** parametr (řetězec dotazu nebo odeslat parametr) v požadavku SAML. Možné hodnoty: `Sha256`, `Sha384`, `Sha512`, nebo `Sha1`. Ujistěte se, že nakonfigurujete algoritmus podpisu na obou stranách se stejnou hodnotou. Použijte pouze algoritmus, který podporuje váš certifikát. | 
| WantsSignedAssertions | Ne | Určuje, zda technický profil vyžaduje všechny příchozí kontrolní výrazy byly podepsané. Možné hodnoty: `true` nebo `false`. Výchozí hodnota je `true`. Pokud je hodnota nastavena na `true`, všechny části kontrolní výrazy `saml:Assertion` odeslané podle identity musí být podepsané zprostředkovatele na Azure AD B2C. Pokud je hodnota nastavena na `false`, zprostředkovatel identity neměli podepisovat kontrolní výrazy, ale i v případě, že dělá, Azure AD B2C se ověřit podpis. Tato metadata řídí také příznak metadata **WantsAssertionsSigned**, což je výstup v metadatech technický profil Azure AD B2C, jež jsou sdílena s tímto poskytovatelem identity. Pokud zakážete ověření kontrolních výrazů, můžete také chtít zakázat ověřování podpisu odpovědi (Další informace najdete v tématu **ResponsesSigned**). |
| ResponsesSigned | Ne | Možné hodnoty: `true` nebo `false`. Výchozí hodnota je `true`. Pokud je hodnota nastavena na `false`, zprostředkovatel identity by neměl podepsat odpověď SAML, ale i v případě, že dělá, Azure AD B2C se ověřit podpis. Pokud je hodnota nastavena na `true`, odpověď SAML odeslanou poskytovatelem identity Azure AD B2C je podepsaná a musí být ověřené. Pokud zakážete ověření odpověď SAML, můžete také chtít zakázat ověřování podpisu kontrolního výrazu (Další informace najdete v tématu **WantsSignedAssertions**). |
| WantsEncryptedAssertions | Ne | Určuje, zda technický profil vyžaduje všechny příchozí kontrolní výrazy byly šifrované. Možné hodnoty: `true` nebo `false`. Výchozí hodnota je `false`. Pokud je hodnota nastavena na `true`, kontrolní výrazy odesílat poskytovatelem identity Azure AD B2C musí být podepsané a **SamlAssertionDecryption** kryptografický klíč se musí zadat. Pokud je hodnota nastavena na `true`, obsahuje metadata technický profil Azure AD B2C **šifrování** oddílu. Zprostředkovatel identity načte metadata a šifruje odpověď kontrolní výraz SAML s veřejným klíčem, který je k dispozici v metadatech technický profil Azure AD B2C. Pokud povolíte šifrování kontrolní výrazy, budete také muset zakázat ověřování podpisu odpovědi (Další informace najdete v tématu **ResponsesSigned**). | 
| IdpInitiatedProfileEnabled | Ne | Určuje, zda je povoleno profil relace přihlášení, která byla spuštěna v rámci profilu poskytovatele identity SAML. Možné hodnoty: `true` nebo `false`. Výchozí formát je `false`. V aplikaci flow zahájené poskytovatelem identity je uživatel ověřený externě a nevyžádané odpovědi jsou odeslána do Azure AD B2C, který pak token využívá, provede kroků Orchestrace a pak odešle odpověď aplikaci předávající strany. |
| NameIdPolicyFormat | Ne | Určuje omezení na identifikátor názvu, který se má použít k reprezentaci požadovaný předmětu. Pokud tento parametr vynechán, lze použít libovolný typ identifikátoru zprostředkovatel identity pro požadovaný předmět. Například `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. **NameIdPolicyFormat** jde použít s **NameIdPolicyAllowCreate**. Podívejte se na dokumentaci poskytovatele identity pokyny o tom, jaký název ID zásad umožňují. |
| NameIdPolicyAllowCreate | Ne | Při použití **NameIdPolicyFormat**, můžete také určit, `AllowCreate` vlastnost **NameIDPolicy**. Hodnota tato metadata je `true` nebo `false` označující, jestli smí vytvořit nový účet během toku přihlášení zprostředkovatele identity. Podívejte se na dokumentaci poskytovatele identity pokyny, jak postupovat. |
| AuthenticationRequestExtensions | Ne | Volitelné protokol zpráv rozšíření prvky, které jsou schváleny mezi službami Azure AD BC a zprostředkovatele identity. Rozšíření se zobrazí ve formátu XML. Přidat data XML CDATA elementu `<![CDATA[Your IDP metadata]]>`. Pokud chcete zobrazit, pokud element rozšíření je podporován v dokumentaci zprostředkovatele identity. |
| IncludeAuthnContextClassReferences | Ne | Určuje jeden nebo více odkazů identifikátor URI identifikující třídy kontextu ověřování. Například pokud chcete, aby uživatel se přihlásit pomocí uživatelského jména a hesla, nastavte hodnotu na `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`. Chcete-li povolit přihlášení pomocí uživatelského jména a hesla přes chráněný relace (SSL/TLS), zadejte `PasswordProtectedTransport`. Podívejte se na dokumentaci poskytovatele identity pro doprovodné materiály **AuthnContextClassRef** identifikátory URI, které jsou podporovány. |
| IncludeKeyInfo | Ne | Určuje, zda požadavek na ověření SAML obsahuje veřejný klíč certifikátu, pokud vazby je nastavena na `HTTP-POST`. Možné hodnoty: `true` nebo `false`. |

## <a name="cryptographic-keys"></a>Kryptografické klíče

**CryptographicKeys** prvek obsahuje následující atributy:

| Atribut |Požadováno | Popis |
| --------- | ----------- | ----------- |
| SamlMessageSigning |Ano | X509 certifikát (sadu klíčů RSA) se má použít k podepsání zprávy SAML. Azure AD B2C používá tento klíč k podepsání žádosti a posílat je zprostředkovatel identity. |
| SamlAssertionDecryption |Ano | X509 certifikát (sadu klíčů RSA) se má použít k dešifrování zpráv SAML. Tento certifikát musí být zadána poskytovatelem identity. Azure AD B2C používá tento certifikát k dešifrování dat odesílaných zprostředkovatele identity. |
| MetadataSigning |Ne | X509 certifikát (sadu klíčů RSA) se má použít k podepsání metadat SAML. Azure AD B2C používá tento klíč k podepsání metadata.  |

## <a name="examples"></a>Příklady

- [Přidání služby AD FS jako zprostředkovatele identity SAML pomocí vlastních zásad](active-directory-b2c-custom-setup-adfs2016-idp.md)
- [Přihlaste se pomocí účtů Salesforce přes SAML](active-directory-b2c-setup-sf-app-custom.md)













