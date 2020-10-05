---
title: Jak vytvářet a podepisovat zásady Azure Attestation
description: Vysvětlení, jak vytvářet a podepisovat zásady ověření identity.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: a3afb12ac831d87b03d0bb16d1b7ef553f1bb906
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "90006815"
---
# <a name="how-to-author-and-sign-an-attestation-policy"></a>Jak vytvářet a podepisovat zásady ověření identity

Zásada ověření identity je soubor nahraný do Microsoft Azure ověření identity. Azure Attestation nabízí flexibilitu při nahrávání zásad ve formátu zásad specifických pro ověření identity. Případně je možné nahrát také zakódovanou verzi zásad v signatuře webu JSON. Správce zásad zodpovídá za zápis zásad ověření identity. Ve většině scénářů ověření identity předávající strana funguje jako správce zásad. Klient, který provádí volání ověření identity, odesílá legitimaci ověření identity, kterou služba analyzuje a převede na příchozí deklarace identity (sada vlastností, hodnota). Služba potom zpracuje deklarace na základě toho, co je definováno v zásadě, a vrátí vypočtený výsledek.

Zásada obsahuje pravidla, která určují kritéria autorizace, vlastnosti a obsah tokenu ověření identity. Ukázkový soubor zásad vypadá takto:

```
version=1.0;
authorizationrules
{
   c:[type="secureBootEnables", issuer=="AttestationService"]=> permit()
};

issuancerules
{
  c:[type="secureBootEnables", issuer=="AttestationService"]=> issue(claim=c)
  c:[type="notSafeMode", issuer=="AttestationService"]=> issue(claim=c)
};
```
 
Soubor zásad má tři segmenty, jak vidíte výše:

- **Version (verze**): verze je číslo verze gramatiky, která je následována. 

    ```
    version=MajorVersion.MinorVersion   
    ```

    V současné době je podporována pouze verze 1,0.

- **autorizačních pravidel**: Zajistěte si kolekci pravidel deklarací identity, která se zkontrolují jako první, abyste zjistili, jestli by ověřování Azure mělo pokračovat na **issuancerules**. Pravidla deklarace identity platí v pořadí, ve kterém jsou definována.

- **issuancerules**: kolekce pravidel deklarací identity, která se vyhodnotí, aby se do výsledku ověřování přidaly Další informace, jak jsou definované v zásadách. Pravidla deklarace identity platí v pořadí, ve kterém jsou definována a jsou také volitelná.

Další informace najdete v tématu [deklarace identity a deklarace identity](claim-rule-grammar.md) .
   
## <a name="drafting-the-policy-file"></a>Koncept souboru zásad

1. Vytvoří nový soubor.
1. Přidejte do souboru verzi.
1. Přidejte oddíly pro **autorizačních pravidel** a **issuancerules**.

  ```
  version=1.0;
  authorizationrules
  {
  =>deny();
  };
  
  issuancerules
  {
  };
  ```

  Autorizační pravidla obsahují akci odepřít () bez podmínky, aby se zajistilo, že se nezpracovávají žádná pravidla vystavování. Případně může autorizační pravidlo také obsahovat akci povolit (), aby bylo možné zpracovat pravidla vystavování.
  
4. Přidání pravidel deklarací do autorizačních pravidel

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  };
  ```

  Pokud příchozí deklarace identity obsahuje deklaraci identity, která odpovídá typu, hodnotě a vystaviteli, akce povolit () upozorní modul zásad, aby zpracoval **issuancerules**.
  
5. Přidejte do **issuancerules**pravidla deklarace identity.

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  => issue(type="SecurityLevelValue", value=100);
  };
  ```
  
  Odchozí sada deklarací bude obsahovat deklaraci identity s:

  ```
  [type="SecurityLevelValue", value=100, valueType="Integer", issuer="AttestationPolicy"]
  ```

  Složité zásady je možné vytvořit podobným způsobem. Další informace najdete v tématu [Příklady zásad ověření identity](policy-examples.md).
  
6. Uložte soubor.

## <a name="creating-the-policy-file-in-json-web-signature-format"></a>Vytvoření souboru zásad ve formátu podpisu webu JSON

Po vytvoření souboru zásad pro nahrání zásady ve formátu JWS postupujte podle následujících kroků.

1. Vygenerujte JWS, RFC 7515 se zásadami (s kódováním UTF-8) jako datovou část.
     - Identifikátor datové části pro zásady kódované Base64Url by měl být "AttestationPolicy".
     
     Ukázkový token JWT:
     ```
     Header: {"alg":"none"}
     Payload: {"AttestationPolicy":" Base64Url (policy)"}
     Signature: {}

     JWS format: eyJhbGciOiJub25lIn0.XXXXXXXXX.
     ```

2. Volitelné Podepište zásadu. Azure Attestation podporuje následující algoritmy:
     - **Žádné**: nepodepisujte datovou část zásad.
     - **RS256**: podporovaný algoritmus pro podepsání datové části zásad

3. Nahrajte JWS a ověřte zásady.
     - Pokud soubor zásad neobsahuje chyby syntaxe, je soubor zásad přijatý službou.
     - Pokud soubor zásad obsahuje syntaktické chyby, je soubor zásad od služby zamítnutý.

## <a name="signing-the-policy"></a>Podepisování zásady

Níže je uveden vzorový skript v jazyce Python, jak provést operaci podepisování zásad.

```python
from OpenSSL import crypto
import jwt
import getpass
       
def cert_to_b64(cert):
              cert_pem = crypto.dump_certificate(crypto.FILETYPE_PEM, cert)
              cert_pem_str = cert_pem.decode('utf-8')
              return ''.join(cert_pem_str.split('\n')[1:-2])
       
print("Provide the path to the PKCS12 file:")
pkcs12_path = str(input())
pkcs12_password = getpass.getpass("\nProvide the password for the PKCS12 file:\n")
pkcs12_bin = open(pkcs12_path, "rb").read()
pkcs12 = crypto.load_pkcs12(pkcs12_bin, pkcs12_password.encode('utf8'))
ca_chain = pkcs12.get_ca_certificates()
ca_chain_b64 = []
for chain_cert in ca_chain:
   ca_chain_b64.append(cert_to_b64(chain_cert))
   signing_cert_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM, pkcs12.get_privatekey())
signing_cert_b64 = cert_to_b64(pkcs12.get_certificate())
ca_chain_b64.insert(0, signing_cert_b64)

print("Provide the path to the policy text file:")
policy_path = str(input())
policy_text = open(policy_path, "r").read()
encoded = jwt.encode({'text': policy_text }, signing_cert_pkey, algorithm='RS256', headers={'x5c' : ca_chain_b64})
print("\nAttestation Policy JWS:")
print(encoded.decode('utf-8'))
```

## <a name="next-steps"></a>Další kroky
- [Nastavení ověření Azure pomocí PowerShellu](quickstart-powershell.md)
- [Ověření SGX enklávy pomocí ukázek kódu](https://docs.microsoft.com/samples/browse/?expanded=azure&terms=attestation)
