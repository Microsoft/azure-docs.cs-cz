---
title: Vlastní pravidlo firewallu webových aplikací pro přední dveře Azure
description: Naučte se používat vlastní pravidla firewallu webových aplikací (WAF), která chrání vaše webové aplikace před škodlivými útoky.
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/07/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 02b335de7f105d768168d5f798ec9109136d7430
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846263"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Vlastní pravidla pro bránu firewall webových aplikací s využitím front-dveří Azure
Firewall webových aplikací Azure (WAF) s front-Dvířk Service umožňují řídit přístup k webovým aplikacím na základě podmínek, které definujete. Vlastní pravidlo WAF se skládá z čísla priority, typu pravidla, podmínek shody a akce. Existují dva typy vlastních pravidel: pravidla shody a pravidla omezení přenosové rychlosti. Pravidlo shody řídí přístup na základě podmínek shody, zatímco pravidlo omezení četnosti řídí přístup na základě podmínek párování a sazeb příchozích požadavků. Můžete zakázat vlastní pravidlo, aby se zabránilo jeho vyhodnocování, ale i zachovat konfiguraci. Tento článek popisuje pravidla shody, která jsou založená na parametrech http.

## <a name="priority-match-conditions-and-action-types"></a>Priority, podmínky shody a typy akcí
Můžete řídit přístup pomocí vlastního pravidla WAf, které definuje číslo priority, typ pravidla, podmínky shody a akci. 

- **Priority:** je jedinečné celé číslo, které popisuje pořadí vyhodnocování pravidel WAF. Pravidla s nižšími hodnotami se vyhodnocují před pravidly s vyššími hodnotami.

- **Action:** definuje způsob směrování požadavku, pokud se shoduje pravidlo WAF. Můžete zvolit jednu z níže uvedených akcí, které se použijí, když požadavek odpovídá vlastnímu pravidlu.

    - *Allow* -WAF přepošle do back-endu záznam, zaznamená položku do protokolů WAF a ukončí.
    - *Blok* -požadavek je ZABLOKOVÁN, WAF odesílá odpověď klientovi bez předání požadavku back-endu. WAF zaprotokoluje záznam v protokolech WAF.
    - *Log* -WAF zaznamená záznam v protokolech WAF a pokračuje v vyhodnocení dalšího pravidla.
    - *Přesměrování* – WAF požadavek přesměrování na zadaný identifikátor URI, protokoluje záznam v protokolech WAF a ukončí.

- **Podmínka shody:** definuje proměnnou shody, operátor a hodnotu shody. Každé pravidlo může obsahovat více podmínek shody. Podmínka shody může být založená na následujících *proměnných shody*:
    - RemoteAddr (IP adresa klienta)
    - requestMethod
    - Řetězec dotazu
    - PostArgs
    - requestUri
    - RequestHeader
    - Částmi

- **Operator:** list obsahuje následující:
    - Any: se často používá k definování výchozí akce, pokud se neshodují žádná pravidla. Any je operátor matched ALL.
    - IPMatch: Definování omezení IP adres pro proměnnou RemoteAddr
    - Geografické porovnávání: definování geografického filtrování pro proměnnou RemoteAddr
    - Výši
    - Obsahuje
    - LessThan: omezení velikosti
    - GreaterThan: omezení velikosti
    - LessThanOrEqual: omezení velikosti
    - GreaterThanOrEqual: omezení velikosti
    - Filtr začíná na
     - endsWith

Podmínka negace  můžete nastavit na hodnotu true, pokud by výsledek podmínky měl být negace.

*Hodnota Match* definuje seznam možných hodnot shody.
Mezi podporované hodnoty metody požadavku HTTP patří:
- GET
- POST
- PUT
- ZÁHLAVÍ
- DELETE
- ZÍSKÁTE
- UZAMKNOUT
- PROFILU
- MOŽNOSTI
- SLOUŽÍ
- PROPPATCH
- MKCOL
- KOPIÍ
- PØESUNOUT

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

## <a name="next-steps"></a>Další postup
- Další informace o [firewallu webových aplikací](waf-overview.md)
- Přečtěte si, jak [vytvořit službu Front Door](quickstart-create-front-door.md).

