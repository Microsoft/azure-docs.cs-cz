---
title: Technický profil SAML definování ve vlastních zásadách v Azure Active Directory B2C | Dokumentace Microsoftu
description: Technický profil SAML definování ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c99099c9b0fdf485bcf1db1d00b23e1e119ec557
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614153"
---
# <a name="define-a-saml-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Technický profil SAML definování ve vlastních zásadách pro Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C poskytuje podporu pro zprostředkovatele identity SAML 2.0. Tento článek popisuje, jaké jsou specifikace technický profil pro interakci s zprostředkovatele deklarací identity, která podporuje tento protokol standardizované. S technický profil SAML, který může provést federaci pomocí zprostředkovatele identity na základě SAML, jako je služba AD FS nebo Salesforce, umožňuje uživatelům přihlásit se pomocí svých podnikových identit nebo existující sociální sítě.

## <a name="metadata-exchange"></a>Metadata exchange

Metadata jsou informace, které používají protokol SAML k vystavení konfiguraci strany SAML, jako je například služba zprostředkovatele nebo zprostředkovatele identity. Metadata definuje umístění služeb, jako metodu přihlašování, přihlašování a odhlašování, certifikáty a další. Zprostředkovatel identity používá metadata k určení, jak ke komunikaci s Azure AD B2C. Metadata je nakonfigurována ve formátu XML a může být podepsané digitálním podpisem tak, aby druhou stranu může ověřit integritu metadat. Azure AD B2C federuje s zprostředkovatele identity SAML, funguje jako poskytovatel služeb zahajování požadavek SAML a čekání na odpověď SAML. A v některých případech, s výjimkou nevyžádané ověřování SAML, která je také označována jako zprostředkovatele identity iniciované. 

Metadata se dá nakonfigurovat v obou stran jako "Statické Metadata" nebo "Dynamická Metadata". V režimu statické zkopírujte celý metadat z jedné strany a nastavte ji v druhé strany. V dynamickém režimu nastavit adresu URL metadat během druhé strany načte konfiguraci dynamicky. Zásady jsou stejné, nastavte metadata technický profil Azure AD B2C ve zprostředkovateli identity a nastavte metadat zprostředkovatele identity v Azure AD B2C.

Každý poskytovatel identity SAML má jiný postup k vystavení a nastavte poskytovatele služeb v tomto případě Azure AD B2C a Azure AD B2C metadata sady ve zprostředkovateli identity. Podívejte se na dokumentaci poskytovatele identity pokyny, jak postupovat.

Následující příklad ukazuje adresu URL metadat SAML technického profilu Azure AD B2C:

```
https://login.microsoftonline.com/te/your-tenant/your-policy/samlp/metadata?idptp=your-technical-profile
```

Nahraďte následující hodnoty:

- **váš tenant** názvem vašeho tenanta, jako je například vaše tenant.onmicrosoft.com
- **vaše zásady** názvem zásady. Pomocí zásad, které nakonfigurujete technický profil SAML zprostředkovatele nebo zásad, která dědí z této zásadě.
- **váš technický profil** s názvem technického profilu poskytovatele identity SAML

## <a name="digital-signing-certificates-exchange"></a>Digitální podepisování certifikátů exchange

Pokud chcete vytvořit vztah důvěryhodnosti mezi Azure AD B2C a zprostředkovatele identity SAML, budete muset zadat platné X509 certifikát s privátním klíčem. Nahrajte certifikát s privátním klíčem (soubor .pfx) pro úložiště klíčů zásady Azure AD B2C. Azure AD B2C digitálně podepíše SAML žádost o přihlášení pomocí certifikátu, který zadáte. 

Certifikát se používá takto:

- Azure AD B2C generuje a podepisuje požadavek SAML, pomocí Azure AD B2C privátní klíč certifikátu. Posílá požadavek SAML zprostředkovatele identity, která ověřuje požadavek pomocí Azure AD B2C veřejný klíč certifikátu. Veřejný certifikát Azure AD B2C je přístupná prostřednictvím technického profilu. Alternativně můžete ručně nahrát soubor .cer do vašeho zprostředkovatele identity SAML.
- Zprostředkovatel identity podepíše data odeslaná do Azure AD B2C pomocí zprostředkovatele identity privátní klíč certifikátu. Azure AD B2C ověří data pomocí veřejného certifikátu zprostředkovatele identity. Každý poskytovatel identity má jiný postup instalace, podívejte se na dokumentaci poskytovatele identity pokyny, jak na to. Vaše zásady v Azure AD B2C, potřebuje přístup k veřejným klíčem certifikátu pomocí metadat zprostředkovatele identity.

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
  <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
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

## <a name="metadata"></a>Metadata

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| PartnerEntity | Ano | Adresa URL metadat SAML zprostředkovatele identity. Zkopírujte metadat zprostředkovatele identity a přidejte do elementu CDATA `<![CDATA[Your IDP metadata]]>` |
| WantsSignedRequests | Ne | Určuje, zda technický profil vyžaduje všechny odchozí žádosti o ověření podepsat. Možné hodnoty: `true` nebo `false`. Výchozí hodnota je `true`. Pokud je hodnota nastavená na `true`, **SamlMessageSigning** kryptografický klíč se musí zadat a všechny odchozí žádosti o ověření přihlášení. Pokud je hodnota nastavena na `false`, **SigAlg** a **podpis** parametry (řetězec dotazu nebo odeslat parametr) jsou vynechány z požadavku. Tato metadata řídí také metadata **AuthnRequestsSigned** atribut, který je výstup v metadatech technický profil Azure AD B2C, jež jsou sdílena s tímto poskytovatelem identity. |
| XmlSignatureAlgorithm | Ne | Metoda, která používá Azure AD B2C k podpisu požadavku SAML. Tato metadata řídí hodnota **SigAlg** parametr (řetězec dotazu nebo odeslat parametr) v požadavku SAML. Možné hodnoty: `Sha256`, `Sha384`, `Sha512`, nebo `Sha1`. Ujistěte se, že nakonfigurujete algoritmus podpisu na obou stranách se stejnou hodnotou. Použijte pouze algoritmus, který podporuje váš certifikát. | 
| WantsSignedAssertions | Ne | Určuje, zda technický profil vyžaduje všechny příchozí kontrolní výrazy byly podepsané. Možné hodnoty: `true` nebo `false`. Výchozí hodnota je `true`. Pokud je hodnota nastavena na `true`, všechny části kontrolní výrazy `saml:Assertion` odeslané podle identity musí být podepsané zprostředkovatele na Azure AD B2C. Pokud je hodnota nastavena na `false`, zprostředkovatel identity neměli podepisovat kontrolní výrazy, ale i v případě, že dělá, Azure AD B2C se ověřit podpis. Tato metadata řídí také příznak metadata **WantsAssertionsSigned**, což je výstup v metadatech technický profil Azure AD B2C, jež jsou sdílena s tímto poskytovatelem identity. Pokud zakážete ověření kontrolních výrazů, můžete také chtít zakázat ověřování podpisu odpovědi (Další informace najdete v tématu **ResponsesSigned**). |
| ResponsesSigned | Ne | Možné hodnoty: `true` nebo `false`. Výchozí hodnota je `true`. Pokud je hodnota nastavena na `false`, zprostředkovatel identity by neměl podepsat odpověď SAML, ale i v případě, že dělá, Azure AD B2C se ověřit podpis. Pokud je hodnota nastavena na `true`, odpověď SAML odeslanou poskytovatelem identity Azure AD B2C je podepsaná a musí být ověřené. Pokud zakážete ověření odpověď SAML, můžete také chtít zakázat ověřování podpisu kontrolního výrazu (Další informace najdete v tématu **WantsSignedAssertions**). |
| WantsEncryptedAssertions | Ne | Určuje, zda technický profil vyžaduje všechny příchozí kontrolní výrazy byly šifrované. Možné hodnoty: `true` nebo `false`. Výchozí hodnota je `false`. Pokud je hodnota nastavena na `true`, kontrolní výrazy odesílat poskytovatelem identity Azure AD B2C musí být podepsané a **SamlAssertionDecryption** kryptografický klíč se musí zadat. Pokud je hodnota nastavena na `true`, obsahuje metadata technický profil Azure AD B2C **šifrování** oddílu. Zprostředkovatel identity načte metadata a šifruje odpověď kontrolní výraz SAML s veřejným klíčem, který je k dispozici v metadatech technický profil Azure AD B2C. Pokud povolíte šifrování kontrolní výrazy, budete také muset zakázat ověřování podpisu odpovědi (Další informace najdete v tématu **ResponsesSigned**). | 
| IdpInitiatedProfileEnabled | Ne |Určuje, zda je povoleno profil relace přihlášení, která byla spuštěna v rámci profilu poskytovatele identity SAML. Možné hodnoty: `true` nebo `false`. Výchozí formát je `false`. V aplikaci flow zahájené poskytovatelem identity je uživatel ověřený externě a nevyžádané odpovědi jsou odeslána do Azure AD B2C, který pak token využívá, provede kroků Orchestrace a pak odešle odpověď aplikaci předávající strany. |

## <a name="cryptographic-keys"></a>Kryptografické klíče

**CryptographicKeys** prvek obsahuje následující atributy:

| Atribut |Požaduje se | Popis |
| --------- | ----------- | ----------- |
| SamlMessageSigning |Ano | X509 certifikát (sadu klíčů RSA) se má použít k podepsání zprávy SAML. Azure AD B2C používá tento klíč k podepsání žádosti a posílat je zprostředkovatel identity. |
| SamlAssertionDecryption |Ano | X509 certifikát (sadu klíčů RSA) se má použít k dešifrování zpráv SAML. Tento certifikát musí být zadána poskytovatelem identity. Azure AD B2C používá tento certifikát k dešifrování dat odesílaných zprostředkovatele identity. |
| MetadataSigning |Ne | X509 certifikát (sadu klíčů RSA) se má použít k podepsání metadat SAML. Azure AD B2C používá tento klíč k podepsání metadata.  |

## <a name="examples"></a>Příklady

- [Přidání služby AD FS jako zprostředkovatele identity SAML pomocí vlastních zásad](active-directory-b2c-custom-setup-adfs2016-idp.md)
- [Přihlaste se pomocí účtů Salesforce přes SAML](active-directory-b2c-setup-sf-app-custom.md)













