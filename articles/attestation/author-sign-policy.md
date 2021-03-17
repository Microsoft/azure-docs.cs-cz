---
title: Jak vytvořit zásady Azure Attestation
description: Vysvětlení, jak vytvořit zásadu ověření identity.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 3e36de62b79788e2efdc3e9abf711924c4fba0c4
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341803"
---
# <a name="how-to-author-an-attestation-policy"></a>Vytvoření zásad ověření identity

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

- **Version (verze** ): verze je číslo verze gramatiky, která je následována. 

    ```
    version=MajorVersion.MinorVersion   
    ```

    V současné době je podporována pouze verze 1,0.

- **autorizačních pravidel** : Zajistěte si kolekci pravidel deklarací identity, která se zkontrolují jako první, abyste zjistili, jestli by ověřování Azure mělo pokračovat na **issuancerules**. Pravidla deklarace identity platí v pořadí, ve kterém jsou definována.

- **issuancerules** : kolekce pravidel deklarací identity, která se vyhodnotí, aby se do výsledku ověřování přidaly Další informace, jak jsou definované v zásadách. Pravidla deklarace identity platí v pořadí, ve kterém jsou definována a jsou také volitelná.

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
  
5. Přidejte do **issuancerules** pravidla deklarace identity.

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
  
6. Soubor uložte.

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
     - **Žádné** : nepodepisujte datovou část zásad.
     - **RS256** : podporovaný algoritmus pro podepsání datové části zásad

3. Nahrajte JWS a ověřte zásady.
     - Pokud soubor zásad neobsahuje chyby syntaxe, je soubor zásad přijatý službou.
     - Pokud soubor zásad obsahuje syntaktické chyby, je soubor zásad od služby zamítnutý.

## <a name="next-steps"></a>Další kroky
- [Nastavení ověření Azure pomocí PowerShellu](quickstart-powershell.md)
- [Ověření SGX enklávy pomocí ukázek kódu](/samples/browse/?expanded=azure&terms=attestation)