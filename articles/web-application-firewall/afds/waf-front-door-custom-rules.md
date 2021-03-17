---
title: Vlastní pravidlo firewallu webových aplikací pro přední dveře Azure
description: Naučte se používat vlastní pravidla firewallu webových aplikací (WAF), která chrání vaše webové aplikace před škodlivými útoky.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 158bfe30bf48ee420be8efb9ff32fff0e555d9e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "79475820"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Vlastní pravidla pro bránu firewall webových aplikací s využitím front-dveří Azure

Firewall webových aplikací Azure (WAF) s front-dvířky umožňují řídit přístup k webovým aplikacím na základě podmínek, které definujete. Vlastní pravidlo WAF se skládá z čísla priority, typu pravidla, podmínek shody a akce. Existují dva typy vlastních pravidel: pravidla shody a pravidla omezení přenosové rychlosti. Pravidlo shody řídí přístup na základě sady podmínek párování, zatímco pravidlo omezení četnosti řídí přístup na základě podmínek párování a sazeb příchozích požadavků. Můžete zakázat vlastní pravidlo, aby se zabránilo jeho vyhodnocování, ale i zachovat konfiguraci. 

## <a name="priority-match-conditions-and-action-types"></a>Priority, podmínky shody a typy akcí

Můžete řídit přístup pomocí vlastního pravidla WAf, které definuje číslo priority, typ pravidla, pole podmínek shody a akci. 

- **Priority:** je jedinečné celé číslo, které popisuje pořadí vyhodnocování pravidel WAF. Pravidla s nižšími hodnotami priority se vyhodnocují před pravidly s vyššími hodnotami. Prioritní hodnoty musí být jedinečné mezi všemi vlastními pravidly.

- **Action:** definuje způsob směrování požadavku, pokud se shoduje pravidlo WAF. Můžete zvolit jednu z níže uvedených akcí, které se použijí, když požadavek odpovídá vlastnímu pravidlu.

    - *Allow* -WAF přepošle do back-endu záznam, zaznamená položku do protokolů WAF a ukončí.
    - *Blok* -požadavek je ZABLOKOVÁN, WAF odesílá odpověď klientovi bez předání požadavku back-endu. WAF zaprotokoluje záznam v protokolech WAF.
    - *Log* -WAF zaznamená záznam v protokolech WAF a pokračuje v vyhodnocení dalšího pravidla.
    - *Přesměrování* – WAF požadavek přesměrování na zadaný identifikátor URI, protokoluje záznam v protokolech WAF a ukončí.

- **Podmínka shody:** definuje proměnnou shody, operátor a hodnotu shody. Každé pravidlo může obsahovat více podmínek shody. Podmínka shody může být založená na geografickém umístění, IP adresách klientů (CIDR), velikosti nebo shodě řetězců. Shoda řetězců může být na seznamu proměnných shody.
  - **Proměnná shody:**
    - RequestMethod
    - QueryString
    - PostArgs
    - RequestUri
    - RequestHeader
    - Částmi
    - Soubory cookie
  - **Podnikatel**
    - Any: se často používá k definování výchozí akce, pokud se neshodují žádná pravidla. Any je operátor matched ALL.
    - Je rovno
    - Contains
    - LessThan: omezení velikosti
    - GreaterThan: omezení velikosti
    - LessThanOrEqual: omezení velikosti
    - GreaterThanOrEqual: omezení velikosti
    - BeginsWith
    - EndsWith
    - Regulární
  
  - **Regulární výraz** nepodporuje následující operace: 
    - Zpětná reference a zachycení dílčích výrazů
    - Libovolné kontrolní výrazy s nulovou šířkou
    - Reference k podrutinám a rekurzivní vzory
    - Podmíněné vzory
    - Řízení operací zpětného navrácení
    - Direktiva "\c single byte"
    - Direktiva pro porovnávání nového řádku \r
    - Začátek směrnice pro obnovení shody od \K
    - Popisky a vložený kód
    - Seskupení Atomic a kvantifikátory possessive

  - **Negace [nepovinné]:** Podmínku *negace* můžete nastavit na hodnotu true, pokud by výsledek podmínky měl být negace.
      
  - **Transformace [volitelné]:** Seznam řetězců s názvy transformací, které se mají provést před pokusem o shodu. Můžou to být následující transformace:
     - Velká písmena 
     - Malá písmena
     - Trim
     - RemoveNulls
     - UrlDecode
     - UrlEncode
     
   - **Hodnota shody:** Mezi podporované hodnoty metody požadavku HTTP patří:
     - GET
     - POST
     - PUT
     - HEAD
     - DELETE
     - ZÍSKÁTE
     - UZAMKNOUT
     - PROFILE
     - NASTAVENÍ
     - PROPFIND
     - PROPPATCH
     - MKCOL
     - KOPIÍ
     - Pøesunout

## <a name="examples"></a>Příklady

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>Příklad vlastních pravidel WAF na základě parametrů http

Tady je příklad, který ukazuje konfiguraci vlastního pravidla se dvěma podmínkami shody. Požadavky jsou ze zadané lokality podle definice odkazujícího serveru a řetězec dotazu neobsahuje "heslo".

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
Příklad konfigurace pro blokování metody PUT je znázorněna níže:

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

Můžete vytvořit vlastní pravidlo, které určuje omezení velikosti v rámci příchozího požadavku. Například pravidlo níže blokuje adresu URL, která je delší než 100 znaků.

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
- [Konfigurace zásad brány firewall webových aplikací pomocí Azure PowerShell](waf-front-door-custom-rules-powershell.md) 
- Další informace o [firewallu webových aplikací s předními dvířky](afds-overview.md)
- Přečtěte si, jak [vytvořit Front Door](../../frontdoor/quickstart-create-front-door.md).

