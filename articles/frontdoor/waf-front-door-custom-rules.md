---
title: Webovou aplikaci vlastní pravidlo brány firewall pro Azure branou
description: Další informace o použití pravidel firewallu webových aplikací (WAF) vlastní chrání vaše webové aplikace před útoky se zlými úmysly.
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/07/2019
ms.author: kumud;tyao
ms.openlocfilehash: 744c6fb9235c9daa2d5239ef9fd13679db943650
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2019
ms.locfileid: "59362959"
---
#  <a name="custom-rules-for-web-application-firewall-with-azure-front-door"></a>Vlastní pravidla pro firewall webových aplikací s Azure branou
Firewall webových aplikací (WAF) s branou služby umožňuje řídit přístup k webovým aplikacím na základě podmínek, které definujete. Vlastní pravidlo WAF se skládá z priorita, typ pravidla, podmínky shody a akci. Existují dva typy vlastních pravidel: odpovídat pravidla a pravidla omezení přenosové rychlosti. Pravidla shody řídí přístup na základě porovnání podmínky, když pravidlo limit frekvence řídí přístup na základě porovnání podmínky a míry příchozí požadavky. Můžete zakázat vlastní pravidlo, které brání jeho právě vyhodnocuje, ale stále zachovat konfiguraci. Tento článek popisuje pravidla shody, založené na protokolu http parametry.

## <a name="priority-match-conditions-and-action-types"></a>Priorita, podmínky shody a typy akcí
Můžete řídit přístup pomocí vlastního pravidla WAf, která definuje priorita, typ pravidla, podmínky shody a akci. 

- **Priorita:** je jedinečné celé číslo, které popisuje pořadí vyhodnocení pravidla firewallu webových aplikací. Pravidla s nižší hodnoty jsou vyhodnocovány dříve, než pravidla s vyššími hodnotami

- **Akce:** definuje, jak směrovat žádost, pokud je nalezen odpovídající pravidlo WAF. Můžete zvolit jednu z následující akce při použití požadavek odpovídá vlastní pravidlo.

    - *Povolit* -WAF předá požadavek na back endu, zaznamená položku do protokolů WAF a ukončí.
    - *Blok* -žádost se zablokovala, WAF odešle odpověď do klienta bez předání požadavku do back endu. WAF zapisuje položku do protokolů WAF.
    - *Protokol* -protokolů WAF položky v WAF protokoly a pokračuje v vyhodnocení další pravidla.
    - *Přesměrovat* -WAF přesměruje požadavek na zadaný identifikátor URI, zapisuje položku do protokolů WAF a ukončí.

- **Odpovídají podmínce:** definuje proměnnou shoda, operátor a odpovídají hodnotě. Každé pravidlo může obsahovat více podmínek shody. Podmínku shody může být založeno na pod *odpovídat proměnné*:
    - RemoteAddr (IP adresa klienta)
    - requestMethod
    - Řetězec dotazu
    - PostArgs
    - RequestUri
    - RequestHeader
    - Includesearchresults: true

- **Operátor:** patří následující:
    - Žádné: se často používá k definování výchozí akci, pokud jsou neodpovídají žádná pravidla. Žádné je shoda všech operátor.
    - IPMatch: omezení IP adres pro proměnnou RemoteAddr definovat
    - GeoMatch: geografické filtrování pro proměnnou RemoteAddr definovat
    - rovno
    - Contains
    - LessThan: omezení velikosti
    - GreaterThan: omezení velikosti
    - LessThanOrEqual: omezení velikosti
    - GreaterThanOrEqual: omezení velikosti
    - Začíná na
     - endsWith

Můžete nastavit *negovat* podmínku, která má být true, pokud by měl bude negovat výsledek podmínku.

*Odpovídající hodnotě* definuje seznam hodnot možnou shodu.
Podporovaná metoda požadavku HTTP, který mezi hodnoty patří:
- GET
- POST
- PUT
- HEAD
- DELETE
- ZÁMEK
- ODEMKNUTÍ
- PROFIL
- MOŽNOSTI
- PROPFIND
- PROPPATCH
- MKCOL
- KOPÍROVÁNÍ
- PŘESUNUTÍ

## <a name="examples"></a>Příklady

### <a name="waf-custom-rules-example-based-on-http-parameters"></a>Příklad WAF vlastních pravidel na základě parametrů http

Tady je příklad, který znázorňuje konfiguraci vlastního pravidla s dvě podmínky shody. Požadavky jsou z určené lokality dle odkazující server a řetězec dotazu neobsahuje "password".

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
Příklad konfigurace týkající se blokování "Vložit" metoda se zobrazí níže:

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

Může vytvářet vlastní pravidlo, které určuje omezení velikosti pro část příchozího požadavku. Například následující pravidlo blokuje adresu Url, která je delší než 100 znaků.

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

