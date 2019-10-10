---
title: Hledat dotazy v protokolech Azure Monitor | Microsoft Docs
description: Tento článek popisuje kurz, jak začít používat hledání v Azure Monitorch dotazech protokolu.
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
ms.date: 08/06/2018
ms.author: bwren
ms.openlocfilehash: a0ceb5aa82b0d38ab5d2567689e3e131ba781ce9
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255000"
---
# <a name="search-queries-in-azure-monitor-logs"></a>Hledání dotazů v protokolech Azure Monitor
Azure Monitor dotazy protokolu mohou začít buď s názvem tabulky, nebo pomocí příkazu pro hledání. Tento kurz se zabývá dotazy založenými na hledání. Existují výhody pro jednotlivé metody.

Dotazy založené na tabulkách začínají oborem dotazu, a proto jsou v úmyslu efektivnější než vyhledávací dotazy. Vyhledávací dotazy jsou méně strukturované, což jim umožňuje lepší volbu při hledání konkrétní hodnoty napříč sloupci nebo tabulkami. **hledání** může pro zadanou hodnotu vyhledat všechny sloupce v dané tabulce nebo ve všech tabulkách. Objem zpracovávaných dat může být mimořádně velký, což znamená, že tyto dotazy mohou trvat delší dobu a mohou vracet velmi velké sady výsledků.

## <a name="search-a-term"></a>Hledání termínu
Vyhledávací **příkaz se** obvykle používá k prohledávání konkrétního termínu. V následujícím příkladu jsou všechny sloupce ve všech tabulkách prohledávány pro termín "Chyba":

```Kusto
search "error"
| take 100
```

I když je lze snadno použít, nevymezené dotazy, jako je ten, který byl zobrazen výše, nejsou efektivní a mohou vracet mnoho nepodstatných výsledků. Lepším postupem je vyhledat v příslušné tabulce nebo dokonce konkrétní sloupec.

### <a name="table-scoping"></a>Rozsah tabulky
Chcete-li vyhledat termín v určité tabulce, přidejte `in (table-name)` hned za operátor **hledání** :

```Kusto
search in (Event) "error"
| take 100
```

nebo ve více tabulkách:
```Kusto
search in (Event, SecurityEvent) "error"
| take 100
```

### <a name="table-and-column-scoping"></a>Rozsah tabulek a sloupců
Ve výchozím nastavení vyhodnotí **hledání** všechny sloupce v datové sadě. Chcete-li vyhledat pouze konkrétní sloupec (pojmenovaný *zdroj* v následujícím příkladu), použijte tuto syntaxi:

```Kusto
search in (Event) Source:"error"
| take 100
```

> [!TIP]
> Použijete-li místo `:` `==`, výsledky by zahrnovaly záznamy, ve kterých má *zdrojový* sloupec přesně hodnotu "Error" a v tomto případě přesně. Použití ': ' bude obsahovat záznamy, kde *zdroj* obsahuje hodnoty, například "kód chyby 404" nebo "Error".

## <a name="case-sensitivity"></a>Rozlišování velkých a malých písmen
Ve výchozím nastavení pojem hledání nerozlišuje velká a malá písmena, takže hledání "DNS" by mohlo mít za následek například "DNS", "DNS" nebo "DNS". Pokud chcete rozlišovat velikost písmen pro hledání, použijte možnost `kind`:

```Kusto
search kind=case_sensitive in (Event) "DNS"
| take 100
```

## <a name="use-wild-cards"></a>Použití zástupných karet
Příkaz **Search** podporuje zástupné znaky na začátku, konci nebo uprostřed období.

Hledání podmínek začínajících řetězcem "Win":
```Kusto
search in (Event) "win*"
| take 100
```

Hledání podmínek, které končí na ". com":
```Kusto
search in (Event) "*.com"
| take 100
```

Chcete-li hledat výrazy, které obsahují text "www":
```Kusto
search in (Event) "*www*"
| take 100
```

Hledat výrazy, které začínají na "Corp" a končí v ". com", například "corp.mydomain.com".

```Kusto
search in (Event) "corp*.com"
| take 100
```

Vše v tabulce můžete také získat pomocí pouze zástupné karty: `search in (Event) *`, ale to by bylo stejné jako psaní pouze `Event`.

> [!TIP]
> I když můžete použít `search *` k získání každého sloupce z každé tabulky, doporučujeme vždy určit rozsah dotazů na konkrétní tabulky. Dokončení dotazů bez oboru může chvíli trvat a může vrátit příliš mnoho výsledků.

## <a name="add-and--or-to-search-queries"></a>Přidat *a* / *nebo* vyhledat dotazy
Pomocí **a** můžete vyhledat záznamy, které obsahují několik výrazů:

```Kusto
search in (Event) "error" and "register"
| take 100
```

Použijte **nebo** k získání záznamů, které obsahují alespoň jeden z těchto podmínek:

```Kusto
search in (Event) "error" or "register"
| take 100
```

Pokud máte více podmínek vyhledávání, můžete je zkombinovat do stejného dotazu pomocí závorek:

```Kusto
search in (Event) "error" and ("register" or "marshal*")
| take 100
```

Výsledky tohoto příkladu by byly záznamy, které obsahují pojem "Chyba" a také obsahují "Registry" nebo něco, co začíná "zařazování".

## <a name="pipe-search-queries"></a>Vyhledávací dotazy kanálu
Stejně jako u jakéhokoli jiného příkazu může být **vyhledávání** rozděleno do kanálu, aby bylo možné výsledky hledání filtrovat, seřadit a agregovat. Například pro získání počtu záznamů *událostí* , které obsahují "Win":

```Kusto
search in (Event) "win"
| count
```




## <a name="next-steps"></a>Další kroky

- Další kurzy najdete na [webu dotazovacího jazyka Kusto](/azure/kusto/query/).
