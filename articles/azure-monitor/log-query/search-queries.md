---
title: Vyhledávací dotazy v protokolech Azure Monitor | Dokumenty společnosti Microsoft
description: Tento článek obsahuje kurz, jak začít používat vyhledávání v dotazech protokolu Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/06/2018
ms.openlocfilehash: e13f4abc37e348759e7d0b8a2f7d890c82fe0d15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660236"
---
# <a name="search-queries-in-azure-monitor-logs"></a>Vyhledávací dotazy v protokolech Azure Monitoru
Dotazy protokolu Azure Monitor můžete začít s názvem tabulky nebo příkaz hledání. Tento kurz se zabývá dotazy založené na vyhledávání. Každá metoda má své výhody.

Dotazy založené na tabulce začínají oborem dotazu a proto mají tendenci být efektivnější než vyhledávací dotazy. Vyhledávací dotazy jsou méně strukturované, což z nich dělá lepší volbu při hledání určité hodnoty ve sloupcích nebo tabulkách. **hledání** může prohledat všechny sloupce v dané tabulce nebo ve všech tabulkách pro zadanou hodnotu. Množství zpracovávaných dat může být enormní, což je důvod, proč tyto dotazy může trvat déle a může vrátit velmi velké sady výsledků.

## <a name="search-a-term"></a>Hledání termínu
Příkaz **hledání** se obvykle používá k prohledání určitého výrazu. V následujícím příkladu jsou všechny sloupce ve všech tabulkách zkontrolovány na termín "chyba":

```Kusto
search "error"
| take 100
```

I když jsou snadno použitelné, neskonsupované dotazy, jako je ten, který je uveden výše, nejsou účinné a pravděpodobně vrátí mnoho irelevantních výsledků. Lepší maješc by bylo hledat v příslušné tabulce, nebo dokonce v konkrétním sloupci.

### <a name="table-scoping"></a>Obor tabulky
Chcete-li vyhledat výraz v `in (table-name)` určité tabulce, přidejte těsně za **vyhledávací** operátor:

```Kusto
search in (Event) "error"
| take 100
```

nebo ve více tabulkách:
```Kusto
search in (Event, SecurityEvent) "error"
| take 100
```

### <a name="table-and-column-scoping"></a>Obor tabulky a sloupců
Ve výchozím nastavení **bude hledání** vyhodnocovat všechny sloupce v sadě dat. Chcete-li vyhledat pouze určitý sloupec (pojmenovaný *Zdroj* v níže uvedeném příkladu), použijte tuto syntaxi:

```Kusto
search in (Event) Source:"error"
| take 100
```

> [!TIP]
> Pokud použijete `==` `:`místo , výsledky by zahrnovaly záznamy, ve kterém *zdroj* sloupec má přesnou hodnotu "chyba", a v tomto přesném případě. Použití ':' bude zahrnovat záznamy, kde *Source* má hodnoty, jako je například "kód chyby 404" nebo "Chyba".

## <a name="case-sensitivity"></a>Rozlišování malých a velkých písmen
Ve výchozím nastavení je hledání termínů bez rozlišování velkých a malých písmen, takže hledání "dns" může přinést výsledky, jako je "DNS", "dns" nebo "Dns". Chcete-li, aby hledání rozlišování, použijte `kind` možnost:

```Kusto
search kind=case_sensitive in (Event) "DNS"
| take 100
```

## <a name="use-wild-cards"></a>Použití zástupných znaků
Příkaz **Hledat** podporuje zástupné znaky na začátku, na konci nebo uprostřed termínu.

Hledané výrazy začínající na "výhra":
```Kusto
search in (Event) "win*"
| take 100
```

Hledané výrazy, které končí na ".com":
```Kusto
search in (Event) "*.com"
| take 100
```

Hledané výrazy, které obsahují "www":
```Kusto
search in (Event) "*www*"
| take 100
```

Chcete-li hledat termíny, které začínají na "corp" a končí na ".com", například "corp.mydomain.com""

```Kusto
search in (Event) "corp*.com"
| take 100
```

Můžete také získat vše v tabulce pomocí pouze `search in (Event) *`divokou kartu: , ale `Event`to by bylo stejné jako psaní jen .

> [!TIP]
> Zatímco můžete `search *` použít k získání každého sloupce z každé tabulky, doporučujeme vždy obor dotazy na konkrétní tabulky. Neskopované dotazy může chvíli trvat a může vrátit příliš mnoho výsledků.

## <a name="add-and--or-to-search-queries"></a>Přidání *and* / *nebo* provyhledávání dotazů
Použití **a** vyhledávání záznamů, které obsahují více termínů:

```Kusto
search in (Event) "error" and "register"
| take 100
```

Použití **nebo** získání záznamů, které obsahují alespoň jeden z termínů:

```Kusto
search in (Event) "error" or "register"
| take 100
```

Pokud máte více podmínek vyhledávání, můžete je kombinovat do stejného dotazu pomocí závorek:

```Kusto
search in (Event) "error" and ("register" or "marshal*")
| take 100
```

Výsledky tohoto příkladu by záznamy, které obsahují termín "chyba" a také obsahují buď "registrovat" nebo něco, co začíná "marshal".

## <a name="pipe-search-queries"></a>Dotazy na vyhledávání potrubí
Stejně jako jakýkoli jiný příkaz, **vyhledávání** může být potrubím, takže výsledky hledání lze filtrovat, seřazené a agregované. Chcete-li například získat počet záznamů *událostí,* které obsahují "vyhrát":

```Kusto
search in (Event) "win"
| count
```




## <a name="next-steps"></a>Další kroky

- Podívejte se na další výukové programy na [webu kusto dotazovacího jazyka](/azure/kusto/query/).
