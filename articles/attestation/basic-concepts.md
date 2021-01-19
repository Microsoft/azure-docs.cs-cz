---
title: Základní koncepty Azure Attestation
description: Základní koncepty ověřování Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: c6c09dc771692cb2fc2f36840e729874cfaf2d09
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2021
ms.locfileid: "98572812"
---
# <a name="basic-concepts"></a>Základní koncepty

Níže jsou uvedené základní koncepty týkající se Microsoft Azure ověření identity.

## <a name="json-web-token-jwt"></a>JSON Web Token (JWT)

[JSON web token](https://jwt.io/) (Jwt) je otevřená standardní metoda [RFC7519](https://tools.ietf.org/html/rfc7519) pro bezpečné předávání informací mezi stranami jako objekt JavaScript Object Notation (JSON). Tyto informace lze ověřit a důvěřovat, protože jsou digitálně podepsány. JWTs je možné podepsat pomocí tajného klíče nebo páru veřejného a privátního klíče.

## <a name="json-web-key-jwk"></a>Webový klíč JSON (JWK)

[Webový klíč JSON](https://tools.ietf.org/html/rfc7517) (JWK) je datová struktura JSON, která představuje kryptografický klíč. Tato specifikace také definuje datovou strukturu JSON pro JWK sadu, která představuje sadu JWKs.

## <a name="attestation-provider"></a>Zprostředkovatel ověření identity

Zprostředkovatel ověření identity patří do poskytovatele prostředků Azure s názvem Microsoft. Attestation. Poskytovatel prostředků je koncový bod služby, který poskytuje kontrakt Azure pro ověření identity REST a je nasazený pomocí [Azure Resource Manager](../azure-resource-manager/management/overview.md). Každý poskytovatel ověření identity respektuje konkrétní, zjistitelnou zásadu. Poskytovatelé ověřování identity se vytvoří s výchozími zásadami pro každý typ ověření identity (Všimněte si, že VBS enklávy nemá žádné výchozí zásady). Další podrobnosti o výchozích zásadách pro SGX najdete v tématu [Příklady zásad ověřování](policy-examples.md) .

### <a name="regional-default-provider"></a>Regionální výchozí zprostředkovatel

Azure Attestation poskytuje v každé oblasti výchozího zprostředkovatele. Zákazníci se můžou rozhodnout použít výchozího poskytovatele pro ověření identity nebo vytvořit vlastní poskytovatele s vlastními zásadami. Výchozí poskytovatelé jsou přístupné pro všechny uživatele Azure AD a zásady přidružené k výchozímu zprostředkovateli se nedají změnit.

| Oblast | Identifikátor URI ověření | 
|--|--|
| East US | `https://sharedeus.eus.attest.azure.net` | 
| USA – západ | `https://sharedwus.wus.attest.azure.net` | 
| Spojené království – jih | `https://shareduks.uks.attest.azure.net` | 
| Spojené království – západ| `https://sharedukw.ukw.attest.azure.net  ` | 
| Kanada – východ | `https://sharedcae.cae.attest.azure.net` | 
| Střední Kanada | `https://sharedcac.cac.attest.azure.net` | 
| Severní Evropa | `https://sharedneu.neu.attest.azure.net` | 
| West Europe| `https://sharedweu.weu.attest.azure.net` | 
| USA – východ 2 | `https://sharedeus2.eus2.attest.azure.net` | 
| USA – střed | `https://sharedcus.cus.attest.azure.net` | 

## <a name="attestation-request"></a>Žádost o ověření identity

Požadavek na ověření identity je serializovaný objekt JSON odeslaný klientskou aplikací do poskytovatele ověření identity. Objekt Request pro SGX enklávy má dvě vlastnosti: 
- "Quot" – hodnota vlastnosti "Quota" je řetězec obsahující Base64URL zakódované reprezentace citace ověřování.
- "EnclaveHeldData" – hodnota vlastnosti "EnclaveHeldData" je řetězec obsahující Base64URL kódované reprezentace enklávy uchovávaných dat.

Azure Attestation ověří zadanou uvozovku a pak zajistí, aby se hodnota hash SHA256 zadaného enklávy dat zadala v prvních 32 bajtech pole reportData v uvozovkách. 

## <a name="attestation-policy"></a>Zásady ověřování identity

Zásady ověření identity se používají ke zpracování legitimace ověřování a dají se konfigurovat pro zákazníky. V jádru služby Azure Attestation je modul zásad, který zpracovává deklarace identity tvořící legitimaci. Zásady se používají k určení, jestli ověření identity Azure vystavuje token ověření na základě legitimace (nebo ne). tím se schválí i ověření identity (nebo ne). Proto by selhání při předání všech zásad způsobilo vydání tokenu JWT.

Pokud výchozí zásady ve zprostředkovateli ověření identity nesplňují požadavky, zákazníci budou moct vytvářet vlastní zásady v jakékoli oblasti podporované službou Azure Attestation. Správa zásad je klíčová funkce poskytovaná zákazníkům pomocí ověření identity Azure. Zásady budou specifické pro typ ověření identity a dají se použít k identifikaci enclaves nebo přidání deklarací identity do výstupního tokenu nebo k úpravě deklarací v výstupním tokenu. 

Podívejte [se na příklady zásad ověření identity](policy-examples.md) pro ukázky zásad.

## <a name="benefits-of-policy-signing"></a>Výhody podepisování zásad

Zásada ověření identity je ta, která nakonec určuje, jestli se token ověření identity vystavuje pomocí ověření identity Azure. Zásady také určují deklarace identity, které se mají vygenerovat v tokenu ověření identity. Je proto důležité, aby zásady vyhodnocené službou byly ve skutečnosti zásady zapsané správcem a nezměnily se ani nezměnily externími entitami. 

Model důvěryhodnosti definuje autorizační model poskytovatele ověření identity pro definování a aktualizaci zásad.  Podporují se dva modely – jedna založená na autorizaci Azure AD a jedna na základě držení kryptografických klíčů spravovaných zákazníkem (označovaného jako izolovaný model).  Izolovaný model povolí Azure Attestation, aby bylo zajištěno, že zásada odeslaná zákazníkem nebude zfalšována.

V izolovaném modelu vytvoří Správce poskytovatele ověření identity, který určí sadu důvěryhodných podpisů certifikátů X. 509 v souboru. Správce pak může přidat podepsané zásady do poskytovatele ověření identity. Při zpracování žádosti o ověření identity Azure ověří platnost podpisu zásady pomocí veřejného klíče reprezentovaného buď parametrem "JWK" nebo "X5C" v hlavičce.  Azure Attestation ověří také, jestli je veřejný klíč v hlavičce požadavku v seznamu důvěryhodných podpisových certifikátů přidružených k poskytovateli ověření identity. Tímto způsobem může předávající strana (Azure Attestation) důvěřovat zásadám podepsaným pomocí certifikátů X. 509, o kterých ví. 

Ukázky najdete v tématu [Příklady certifikátů podepisování zásad](policy-signer-examples.md) .

## <a name="attestation-token"></a>Token ověření identity

Odpověď Azure Attestation bude řetězec JSON, jehož hodnota obsahuje token JWT. Azure Attestation bude zabalit deklarace a vygeneruje podepsaný token JWT. Operace podepisování se provádí pomocí certifikátu podepsaného svým držitelem s názvem subjektu, který odpovídá elementu AttestUri poskytovatele ověření identity.

Rozhraní API pro získání metadat OpenID vrací odpověď konfigurace OpenID, jak je určena [protokolem zjišťování OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig). Rozhraní API načte metadata pro podpisové certifikáty používané službou Azure Attestation.

Příklad generovaného tokenu JWT pro SGX enklávy:

```
{
  "alg": "RS256",
  "jku": "https://tradewinds.us.attest.azure.net/certs",
  "kid": <self signed certificate reference to perform signature verification of attestation token,
  "typ": "JWT"
}.{
  "aas-ehd": <input enclave held data>,
  "exp": 1568187398,
  "iat": 1568158598,
  "is-debuggable": false,
  "iss": "https://tradewinds.us.attest.azure.net",
  "maa-attestationcollateral": 
    {
      "qeidcertshash": <SHA256 value of QE Identity issuing certs>,
      "qeidcrlhash": <SHA256 value of QE Identity issuing certs CRL list>,
      "qeidhash": <SHA256 value of the QE Identity collateral>,
      "quotehash": <SHA256 value of the evaluated quote>, 
      "tcbinfocertshash": <SHA256 value of the TCB Info issuing certs>, 
      "tcbinfocrlhash": <SHA256 value of the TCB Info issuing certs CRL list>, 
      "tcbinfohash": <SHA256 value of the TCB Info collateral>
     },
  "maa-ehd": <input enclave held data>,
  "nbf": 1568158598,
  "product-id": 4639,
  "sgx-mrenclave": <SGX enclave mrenclave value>,
  "sgx-mrsigner": <SGX enclave msrigner value>,
  "svn": 0,
  "tee": "sgx"
  "x-ms-attestation-type": "sgx", 
  "x-ms-policy-hash": <>,
  "x-ms-sgx-collateral": 
    {
      "qeidcertshash": <SHA256 value of QE Identity issuing certs>,
      "qeidcrlhash": <SHA256 value of QE Identity issuing certs CRL list>,
      "qeidhash": <SHA256 value of the QE Identity collateral>,
      "quotehash": <SHA256 value of the evaluated quote>, 
      "tcbinfocertshash": <SHA256 value of the TCB Info issuing certs>, 
      "tcbinfocrlhash": <SHA256 value of the TCB Info issuing certs CRL list>, 
      "tcbinfohash": <SHA256 value of the TCB Info collateral>
     },
  "x-ms-sgx-ehd": <>, 
  "x-ms-sgx-is-debuggable": true,
  "x-ms-sgx-mrenclave": <SGX enclave mrenclave value>,
  "x-ms-sgx-mrsigner": <SGX enclave msrigner value>, 
  "x-ms-sgx-product-id": 1, 
  "x-ms-sgx-svn": 1,
  "x-ms-ver": "1.0"
}.[Signature]
```
Některé výše použité deklarace identity se považují za zastaralé, ale jsou plně podporované.  Doporučuje se, aby veškerý budoucí kód a nástroje používaly nepoužívané názvy deklarací identity. Další informace najdete v tématu [deklarace identity vydané Azure Attestation](claim-sets.md) .

## <a name="encryption-of-data-at-rest"></a>Šifrování dat v klidovém umístění

K ochraně zákaznických dat zachovává Azure Attestation data v Azure Storage. Služba Azure Storage poskytuje šifrování neaktivních dat, která se zapisují do datových center, a dešifruje je pro zákazníky, kteří k nim mají přístup. K tomuto šifrování dochází pomocí spravovaného šifrovacího klíče společnosti Microsoft. 

Kromě ochrany dat ve službě Azure Storage využívá Azure Attestation taky Azure Disk Encryption (ADE) k šifrování virtuálních počítačů služby. Pro Azure Attestation běžící v enklávy v prostředích s důvěrnými výpočetními prostředími Azure se v současnosti nepodporuje rozšíření ADE. V takových scénářích, aby nedocházelo k ukládání dat v paměti, je stránkovací soubor zakázaný. 

Na místních pevných discích instance Azure Attestation se neukládají žádná zákaznická data.


## <a name="next-steps"></a>Další kroky

- [Jak vytvářet a podepisovat zásady ověření identity](author-sign-policy.md)
- [Nastavení ověření Azure pomocí PowerShellu](quickstart-powershell.md)
