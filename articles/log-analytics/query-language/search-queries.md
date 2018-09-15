---
title: Vyhledávací dotazy v Log Analytics | Dokumentace Microsoftu
description: Tento článek obsahuje kurz pro zahájení práce zápis vyhledávací dotazy v Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/06/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 2ccef960378190f10e64318f91039871657a1a46
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603749"
---
# <a name="search-queries-in-log-analytics"></a>Vyhledávací dotazy v Log Analytics

> [!NOTE]
> By se měla Dokončit [Začínáme s dotazy v Log Analytics](get-started-queries.md) před dokončením v této lekci.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Dotazy služby Azure Log Analytics můžete začít pomocí názvu tabulky nebo příkaz pro hledání. Tento kurz se zabývá vyhledávacích dotazů. Existují výhody každé metody.

Dotazy založené na tabulce začněte tím, že vytváření oboru dotazu a proto jsou obvykle mnohem efektivnější než vyhledávací dotazy. Vyhledávací dotazy jsou že menší strukturovaná, což je lepší volbou při vyhledávání pro konkrétní hodnoty napříč sloupců nebo tabulek. **hledání** můžete zkontrolovat všechny sloupce z dané tabulky, nebo všechny tabulky, pro zadanou hodnotu. Množství zpracovávaných dat může být obrovské, což je důvod, proč může trvat delší dobu pro dokončení těchto dotazů a může vrátit velmi velké množství výsledků.

## <a name="search-a-term"></a>Hledání pojmu
**Hledání** příkazu se obvykle používá k vyhledání konkrétní období. V následujícím příkladu jsou všechny sloupce všech tabulek vyhledávat termín "Chyba":

```KQL
search "error"
| take 100
```

Když jsou snadno použitelné, bez ohledu na obor dotazy jako ten výše jsme si ukázali, nejsou efektivní a které můžete chtít vrátit mnoho relevantní výsledky. Doporučeno bude hledat v příslušné tabulce nebo v určitém sloupci.

### <a name="table-scoping"></a>Rozsah tabulky
Chcete-li vyhledat termín v určité tabulce, přidejte `in (table-name)` hned za **hledání** operátor:

```KQL
search in (Event) "error"
| take 100
```

nebo ve více tabulkách:
```KQL
search in (Event, SecurityEvent) "error"
| take 100
```

### <a name="table-and-column-scoping"></a>Tabulky a sloupce oborů
Ve výchozím nastavení **hledání** vyhodnotí všechny sloupce v datové sadě. K vyhledání pouze v určitém sloupci, použijte následující syntaxi:

```KQL
search in (Event) Source:"error"
| take 100
```

> [!TIP]
> Pokud používáte `==` místo `:`, výsledky bude zahrnovat záznamy, ve kterém *zdroj* sloupec má přesná hodnota "Chyba" a v tomto případě přesné. Pomocí ":" nebude obsahovat záznamy kde *zdroj* obsahuje hodnoty, jako je například "kód chyby 404" nebo "Chyba".

## <a name="case-sensitivity"></a>Rozlišování
Ve výchozím nastavení je velká a malá písmena, termín vyhledávání, takže vyhledávání dns."může vést k výsledkům, například"DNS","dns"nebo"Dns". Chcete-li do hledání velká a malá písmena, použijte `kind` možnost:

```KQL
search kind=case_sensitive in (Event) "DNS"
| take 100
```

## <a name="use-wild-cards"></a>Použít zástupné znaky
**Hledání** zástupnými znaky na začátku, end nebo střední termín, který podporuje příkaz.

Chcete-li hledat podmínky, které začínají řetězcem "win":
```KQL
search in (Event) "win*"
| take 100
```

Chcete-li hledat podmínky, které končí řetězcem ".com":
```KQL
search in (Event) "*.com"
| take 100
```

Chcete-li hledat podmínky, které obsahují "www":
```KQL
search in (Event) "*www*"
| take 100
```

Chcete-li hledat podmínky, které začíná "corp" a končí na ".com", jako je například "corp.mydomain.com" "

```KQL
search in (Event) "corp*.com"
| take 100
```

Můžete také získáte všechno, co v tabulce s použitím pouze zástupný znak: `search in (Event) *`, ale, že budou stejné jako psaní právě `Event`.

> [!TIP]
> Přestože můžete použít `search *` zobrazíte všechny sloupce z každé tabulky se doporučuje, oboru vždy své dotazy na konkrétní tabulky. Bez ohledu na obor dotazů může chvíli trvat a může vrátit příliš mnoho výsledků.

## <a name="add-and--or-to-search-queries"></a>Přidat *a* / *nebo* hledání dotazů
Použití **a** k vyhledání záznamů, které obsahují více sad podmínek:

```KQL
search in (Event) "error" and "register"
| take 100
```

Použití **nebo** získat záznamy, které obsahují alespoň jeden podmínek:

```KQL
search in (Event) "error" or "register"
| take 100
```

Pokud máte více podmínek vyhledávání, můžete je zkombinovat do stejného dotazu pomocí závorek:

```KQL
search in (Event) "error" and ("register" or "marshal*")
| take 100
```

Výsledky v tomto příkladu by záznamy, které obsahují pojem "Chyba" a také obsahovat buď "register" nebo něco začínající na "zařazování".

## <a name="pipe-search-queries"></a>Kanál vyhledávacích dotazů
Stejně jako jakýkoli příkaz **hledání** lze přesměrovat tak, aby výsledky hledání můžete filtrovat, řazení a agregovat. Například, chcete-li získat číslo *události* záznamy, které obsahují "win":

```KQL
search in (Event) "win"
| count
```




## <a name="next-steps"></a>Další postup

- Další kurzy na [lokality jazyk dotazu Log Analytics ](https://docs.loganalytics.io)