---
title: Zápis dotazů efektivní protokolu ve službě Azure Monitor | Dokumentace Microsoftu
description: Odkazy na zdroje informací, jak psát dotazy v Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: bwren
ms.openlocfilehash: f7eca75c6c445f801ad5c4dbc080d2ece9dc23f9
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54392228"
---
# <a name="writing-efficient-log-queries-in-azure-monitor"></a>Zápis dotazů efektivní protokolu ve službě Azure Monitor
Tento článek obsahuje doporučení pro zápis dotazů efektivní protokolu ve službě Azure Monitor. Pomocí těchto strategií, můžete zajistit, že vaše dotazy se spustí rychle a s minimálním overheard.

## <a name="scope-your-query"></a>Změňte obor dotazu
Máte dotaz zpracování více dat než skutečně potřebujete může vést k dlouhého dotazu a často za následek příliš mnoho dat ve výsledcích efektivně analyzovat. Ve výjimečných případech může dotaz i vypršení časového limitu a selhání.

### <a name="specify-your-data-source"></a>Zadejte zdroj dat
Prvním krokem při psaní dotazu efektivní je omezit její obor na jeho požadované datové zdroje. Určení tabulku je vždy upřednostňované nad systémem vyhledávání protokolem široké textu, jako `search *`. Dotaz na konkrétní tabulku, spusťte dotaz s názvem tabulky jako v následující:

``` Kusto
requests | ...
```

Můžete použít [hledání](/azure/kusto/query/searchoperator) k vyhledání hodnoty napříč více sloupců v konkrétní tabulky pomocí dotaz podobný tomuto:

``` Kusto
search in (exceptions) "The server was not found"

search in (exceptions, customEvents) "timeout"
```

Použití [sjednocení](/kusto/query/unionoperator) k dotazování několik tabulek takto:

``` Kusto
union requests, traces | ...
```

### <a name="specify-a-time-range"></a>Zadejte časový rozsah
Také byste měli omezit dotaz a časové rozmezí dat, která požadujete. Ve výchozím nastavení dotaz bude obsahovat data shromážděná za posledních 24 hodin. Tuto možnost můžete změnit [selektor rozsahu času](get-started-portal.md#select-a-time-range) nebo explicitně přidejte do dotazu. Je vhodné přidat filtr času ihned po názvu tabulky tak, aby zbývající část dotazu pouze zpracovává data v rámci tohoto rozsahu:

``` Kusto
requests | where timestamp > ago(1h)

requests | where timestamp between (ago(1h) .. ago(30m))
```
   
### <a name="get-only-the-latest-records"></a>Získat nejnovější záznamy

Vrátit pouze nejnovější záznamy, použít *horní* operátor stejně jako v následující dotaz, který vrátí nejnovější 10 záznamů přihlášení *trasování* tabulky:

``` Kusto
traces | top 10 by timestamp
```

   
### <a name="filter-records"></a>Filtrování záznamů
Chcete-li kontrolovat pouze protokoly, které odpovídají dané podmínce, použijte *kde* operátor stejně jako v následující dotaz, který vrátí pouze záznamy, ve kterém _severityLevel_ hodnota je vyšší než 0:

``` Kusto
traces | where severityLevel > 0
```



## <a name="string-comparisons"></a>Porovnávání řetězců
Když [vyhodnocování řetězce](/azure/kusto/query/datatypes-string-operators), obvykle byste měli použít `has` místo `contains` při hledání pro úplné tokeny. `has` je mnohem efektivnější, protože nemá k vyhledávání podřetězců.

## <a name="returned-columns"></a>Vrácené sloupce

Použití [projektu](/azure/kusto/query/projectoperator) Chcete-li zúžit sadu sloupců, které jsou zpracovávány pouze na ty budete potřebovat:

``` Kusto
traces 
| project timestamp, severityLevel, client_City 
| ...
```

Přestože můžete použít [rozšířit](/azure/kusto/query/extendoperator) pro výpočet hodnoty a vytvořit vlastní sloupce, obvykle je efektivnější vyfiltrujte sloupec tabulky.

Například první dotaz níže, který filtruje na _operace\_název_ by být efektivnější než druhý, který vytvoří nový _předplatné_ sloupec a na něm filtry:

``` Kusto
customEvents 
| where split(operation_Name, "/")[2] == "acb"

customEvents 
| extend subscription = split(operation_Name, "/")[2] 
| where subscription == "acb"
```

## <a name="using-joins"></a>Pomocí spojení
Při použití [spojení](/azure/kusto/query/joinoperator) operátor, vyberte v tabulce s menší počet řádků na levé straně dotazu.


## <a name="next-steps"></a>Další postup
Další informace o osvědčených postupech dotazů najdete v tématu [dotazování osvědčené postupy](/azure/kusto/query/best-practices).