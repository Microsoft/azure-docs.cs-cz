---
title: Vlastní pravidlo brány firewall webové aplikace pro Azure Front Door
description: Přečtěte si, jak používat vlastní pravidla brány firewall webových aplikací (WAF), která chrání webové aplikace před škodlivými útoky.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 158bfe30bf48ee420be8efb9ff32fff0e555d9e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475820"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Vlastní pravidla pro bránu firewall webových aplikací s azure front door

Azure Web Application Firewall (WAF) s front door umožňuje řídit přístup k webovým aplikacím na základě podmínek, které definujete. Vlastní pravidlo WAF se skládá z čísla priority, typu pravidla, podmínek shody a akce. Existují dva typy vlastních pravidel: pravidla shody a pravidla omezení rychlosti. Pravidlo shody řídí přístup na základě sady odpovídajících podmínek, zatímco pravidlo omezení rychlosti řídí přístup na základě odpovídajících podmínek a rychlosti příchozích požadavků. Můžete zakázat vlastní pravidlo, aby se zabránilo jeho vyhodnocení, ale stále zachovat konfiguraci. 

## <a name="priority-match-conditions-and-action-types"></a>Priorita, podmínky shody a typy akcí

Přístup můžete řídit pomocí vlastního pravidla WAf, které definuje číslo priority, typ pravidla, pole podmínek shody a akci. 

- **Priorita:** je jedinečné celé číslo, které popisuje pořadí vyhodnocení pravidel WAF. Pravidla s nižšími prioritami jsou vyhodnocována před pravidly s vyššími hodnotami. Prioritní čísla musí být mezi všemi vlastními pravidly jedinečná.

- **Akce:** definuje způsob směrování požadavku, pokud je pravidlo WAF spárováno. Můžete zvolit jednu z níže uvedených akcí, které se použijí, když požadavek odpovídá vlastnímu pravidlu.

    - *Povolit* - WAF předá úkol back-endu, zaznamená položku v protokolech WAF a ukončí.
    - *Blok* - Požadavek je blokován, WAF odešle odpověď klientovi bez předání požadavku back-endu. WAF přihlásí položku v protokolech WAF.
    - *Log* - WAF protokoluje položku v protokolech WAF a pokračuje v vyhodnocování dalšího pravidla.
    - *Přesměrování* – WAF přesměruje požadavek na zadaný identifikátor URI, zaznamená položku v protokolech WAF a ukončí.

- **Podmínka shody:** definuje proměnnou shody, operátor a hodnotu shody. Každé pravidlo může obsahovat více podmínek shody. Podmínka shody může být založena na geografickém umístění, IP adresách klienta (CIDR), velikosti nebo smyčcové shodě. String shoda může být proti seznamu proměnných shody.
  - **Shoda proměnná:**
    - RequestMethod
    - QueryString
    - PostArgs
    - Requesturi
    - Hlavička požadavku
    - RequestBody
    - Soubory cookie
  - **Operátor:**
    - Any: se často používá k definování výchozí akce, pokud nejsou spárována žádná pravidla. Každý je shoda všech operátorů.
    - Rovno
    - Contains
    - LessThan: omezení velikosti
    - GreaterThan: omezení velikosti
    - LessThanOrEqual: omezení velikosti
    - GreaterThanOrEqual: omezení velikosti
    - BeginsWith
    - EndsWith
    - Regex
  
  - **Společnost Regex** nepodporuje následující operace: 
    - Zpětné odkazy a zachycení podvýrazů
    - Libovolné kontrolní výrazy s nulovou šířkou
    - Odkazy na podprogram a rekurzivní vzory
    - Podmíněné vzory
    - Slovesa ovládacího prvku backtracking
    - Jednobajtová direktiva \C
    - Direktiva shody \R newline
    - Direktiva obnovení shody \K
    - Popisky a vložený kód
    - Atomové seskupení a přivlastňovací kvantifikátory

  - **Negovat [nepovinné]:** Pokud by měl být výsledek podmínky negován negována, můžete nastavit podmínku *negovat.*
      
  - **Transformace [nepovinné]:** Seznam řetězců s názvy transformací, které je třeba provést před pokusem o shodu. Mohou to být následující transformace:
     - Velká písmena 
     - Malá písmena
     - Trim
     - Odebrat nulls
     - UrlDecode
     - Urlencode
     
   - **Hodnota shody:** Mezi podporované hodnoty metody požadavku HTTP patří:
     - GET
     - POST
     - PUT
     - HEAD
     - DELETE
     - Zámek
     - Odemknout
     - PROFILE
     - Možnosti
     - PROPFIND
     - PROPPATCH
     - MKCOL
     - Kopírovat
     - Přesunout

## <a name="examples"></a>Příklady

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>Příklad vlastních pravidel WAF založený na parametrech http

Zde je příklad, který ukazuje konfiguraci vlastního pravidla se dvěma podmínkami shody. Požadavky pocházejí ze zadaného webu, jak je definováno odkazem, a řetězec dotazu neobsahuje "heslo".

```
# http rules example
{
  "name": "AllowFromTrustedSites",
  "priority": 1,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestHeader",
      "selector": "Referer",
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "www.mytrustedsites.com/referpage.html"
      ]
    },
    {
      "matchVariable": "QueryString",
      "operator": "Contains",
      "matchValue": [
        "password"
      ],
      "negateCondition": true
    }
  ],
  "action": "Allow",
  "transforms": []
}

```
Příklad konfigurace pro blokování metody "PUT" je zobrazen takto:

``` 
# http Request Method custom rules
{
  "name": "BlockPUT",
  "priority": 2,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestMethod",
      "selector": null,
      "operator": "Equal",
      "negateCondition": false,
      "matchValue": [
        "PUT"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

### <a name="size-constraint"></a>Omezení velikosti

Můžete vytvořit vlastní pravidlo, které určuje omezení velikosti na část příchozí ho požadavku. Například pod pravidlem blokuje adresu URL, která je delší než 100 znaků.

```
# http parameters size constraint
{
  "name": "URLOver100",
  "priority": 5,
  "ruleType": "MatchRule",
  "matchConditions": [
    {
      "matchVariable": "RequestUri",
      "selector": null,
      "operator": "GreaterThanOrEqual",
      "negateCondition": false,
      "matchValue": [
        "100"
      ]
    }
  ],
  "action": "Block",
  "transforms": []
}
```

## <a name="next-steps"></a>Další kroky
- [Konfigurace zásad brány firewall webových aplikací pomocí Azure PowerShellu](waf-front-door-custom-rules-powershell.md) 
- Další informace o [webové aplikační bráně firewall s předními dveřmi](afds-overview.md)
- Přečtěte si, jak [vytvořit Front Door](../../frontdoor/quickstart-create-front-door.md).

