---
title: Zápis efektivních dotazů protokolu v Azure Monitor | Microsoft Docs
description: Odkazy na zdroje informací o tom, jak zapisovat dotazy v Log Analytics.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2019
ms.openlocfilehash: a5ee03f6c42f076549856161a6ebe0b1888fe4aa
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894133"
---
# <a name="writing-efficient-log-queries-in-azure-monitor"></a>Zápis efektivních dotazů protokolu v Azure Monitor
Tento článek poskytuje doporučení pro vytváření efektivních dotazů protokolu v Azure Monitor. Pomocí těchto strategií můžete zajistit, že dotazy budou spouštěny rychle a s minimálním přesvědčit.

## <a name="scope-your-query"></a>Určení rozsahu dotazu
Zpracování dotazů s větším množstvím dat, než kolik skutečně potřebujete, může vést k dlouhotrvajícímu dotazu a často má za následek příliš mnoho dat, aby bylo možné efektivně analyzovat. V extrémních případech může dotaz dokonce i vyprší časový limit a selhat.

### <a name="specify-your-data-source"></a>Zadat zdroj dat
Prvním krokem při psaní efektivního dotazu je omezení jeho oboru na požadované zdroje dat. Zadání tabulky je vždy upřednostňováno před spuštěním fulltextového vyhledávání, například `search *`. Chcete-li zadat dotaz na konkrétní tabulku, spusťte dotaz s názvem tabulky, jak je uvedeno v následujícím seznamu:

``` Kusto
requests | ...
```

Pomocí [vyhledávání](/azure/kusto/query/searchoperator) můžete vyhledat hodnotu v různých sloupcích v určitých tabulkách pomocí dotazu, jako je následující:

``` Kusto
search in (exceptions) "The server was not found"

search in (exceptions, customEvents) "timeout"
```

Použijte [sjednocení](/azure/kusto/query/unionoperator) k dotazování několika tabulek, jako jsou následující:

``` Kusto
union requests, traces | ...
```

### <a name="specify-a-time-range"></a>Zadejte časový rozsah.
Měli byste také omezit dotaz na časový rozsah dat, která požadujete. Ve výchozím nastavení bude dotaz zahrnovat data shromážděná za posledních 24 hodin. Tuto možnost můžete změnit v [selektoru časového rozsahu](get-started-portal.md#select-a-time-range) nebo ji přidat explicitně do dotazu. Nejvhodnější je přidat filtr času hned za název tabulky, aby zbytek dotazu zpracoval pouze data v tomto rozsahu:

``` Kusto
requests | where timestamp > ago(1h)

requests | where timestamp between (ago(1h) .. ago(30m))
```
   
### <a name="get-only-the-latest-records"></a>Získat pouze nejnovější záznamy

Chcete-li vrátit pouze nejnovější záznamy, použijte operátor *Top* jako v následujícím dotazu, který vrátí nejnovější 10 záznamů zaznamenaných v tabulce *trasování* :

``` Kusto
traces | top 10 by timestamp
```

   
### <a name="filter-records"></a>Filtrovat záznamy
Chcete-li zkontrolovat pouze protokoly, které odpovídají dané podmínce, použijte operátor *WHERE* jako v následujícím dotazu, který vrátí pouze záznamy, jejichž hodnota _severityLevel_ je větší než 0:

``` Kusto
traces | where severityLevel > 0
```



## <a name="string-comparisons"></a>Porovnávání řetězců
Při [vyhodnocování řetězců](/azure/kusto/query/datatypes-string-operators)byste při hledání úplných tokenů měli obvykle použít `has` místo `contains`. `has` je efektivnější, protože není nutné hledat podřetězce.

## <a name="returned-columns"></a>Vrácené sloupce

Pomocí [aplikace Project](/azure/kusto/query/projectoperator) můžete zúžit sadu zpracovávaných sloupců jenom na ty, které potřebujete:

``` Kusto
traces 
| project timestamp, severityLevel, client_City 
| ...
```

I když můžete použít možnost [Extended](/azure/kusto/query/extendoperator) pro výpočet hodnot a vytváření vlastních sloupců, bude obvykle efektivnější filtrovat sloupec tabulky.

Například první dotaz níže, který filtruje _operace\_název_ by byl efektivnější než druhý, který vytvoří nový sloupec _odběru_ a provede na něj filtry:

``` Kusto
customEvents 
| where split(operation_Name, "/")[2] == "acb"

customEvents 
| extend subscription = split(operation_Name, "/")[2] 
| where subscription == "acb"
```

## <a name="using-joins"></a>Použití spojení
Při použití operátoru [Join](/azure/kusto/query/joinoperator) vyberte tabulku s méně řádky, která má být na levé straně dotazu.


## <a name="next-steps"></a>Další kroky
Další informace o osvědčených postupech dotazů najdete v tématu [osvědčené postupy pro dotazy](/azure/kusto/query/best-practices).