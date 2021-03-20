---
title: Scénáře úložiště dotazů – Azure Database for PostgreSQL – jeden server
description: Tento článek popisuje některé scénáře pro úložiště dotazů na serveru Azure Database for PostgreSQL-Single.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: ad646e39e9b6a87611dc2f40330a77fe70c92c27
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91710545"
---
# <a name="usage-scenarios-for-query-store"></a>Scénáře použití pro úložiště dotazů

**Platí pro:** Azure Database for PostgreSQL – jeden server verze 9,6, 10, 11

Úložiště dotazů můžete použít v nejrůznějších scénářích, ve kterých je sledování a udržování předvídatelného výkonu úloh velmi důležité. Představte si následující příklady: 
- Identifikace a ladění hlavních náročných dotazů 
- Testování A/B 
- Udržení stabilního výkonu během upgradů 
- Identifikace a vylepšení úloh ad hoc 

## <a name="identify-and-tune-expensive-queries"></a>Identifikace a optimalizace nákladných dotazů 

### <a name="identify-longest-running-queries"></a>Identifikujte nejdelší běžící dotazy 
Pomocí zobrazení [Query Performance Insight](concepts-query-performance-insight.md) v Azure Portal můžete rychle identifikovat nejdelší běžící dotazy. Tyto dotazy obvykle často využívají významné množství prostředků. Optimalizace nejdelších dotazů může zvýšit výkon tím, že se uvolní prostředky pro použití jinými dotazy běžícími ve vašem systému. 

### <a name="target-queries-with-performance-deltas"></a>Cílové dotazy s rozdíly ve výkonu 
V úložišti dotazů jsou data o výkonu rozdělená do oken času, takže můžete sledovat výkon dotazů v průběhu času. To vám pomůže identifikovat přesně to, které dotazy přispívají k nárůstu v celkové době strávené. Výsledkem je, že můžete provést cílené řešení potíží s úlohou.

### <a name="tuning-expensive-queries"></a>Optimalizace nákladných dotazů 
Pokud identifikujete dotaz s optimálním výkonem, záleží na tom, jakou akci chcete provést: 
- Použijte [doporučení k výkonu](concepts-performance-recommendations.md) k určení, jestli existují nějaké navrhované indexy. Pokud ano, vytvořte index a pak pomocí úložiště dotazů vyhodnoťte výkon dotazu po vytvoření indexu. 
- Ujistěte se, že jsou statistiky aktuální pro podkladové tabulky používané dotazem.
- Zvažte přepsání drahých dotazů. Můžete například využít výhod dotazů Parametrizace a snížit používání dynamického jazyka SQL. Implementujte optimální logiku při čtení dat, jako je použití filtrování dat na straně databáze, nikoli na straně aplikace. 


## <a name="ab-testing"></a>Testování A/B 
Použijte úložiště dotazů pro porovnání výkonu úloh před a po změně plánu, který chcete zavést. Příklady scénářů použití úložiště dotazů k vyhodnocení dopadu změny prostředí nebo aplikace na výkon úlohy: 
- Zavedení nové verze aplikace 
- Přidání dalších prostředků na server. 
- Vytváření chybějících indexů na tabulkách, na které odkazují nákladné dotazy. 
 
V některém z těchto scénářů použijte následující pracovní postup: 
1. Spusťte své zatížení s úložištěm dotazů předtím, než plánovaná změna vygeneruje směrný plán výkonu. 
2. Použijte změny aplikace v řízeném okamžiku v čase. 
3. Pokračujte v běhu úlohy dostatečně dlouho, aby se po provedení změny vygenerovala image výkonu systému. 
4. Porovnat výsledky před změnou a po ní. 
5. Rozhodněte, jestli chcete zachovat změnu nebo vrácení změn. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Identifikace a vylepšení úloh ad hoc 
Některé úlohy nemají dominantní dotazy, které můžete ladit pro zlepšení celkového výkonu aplikace. U těchto úloh se obvykle používá poměrně velký počet jedinečných dotazů, přičemž každý z nich spotřebovává část systémových prostředků. Každý jedinečný dotaz je spuštěn zřídka, takže jeho spotřeba za běhu není kritická. Na druhé straně, vzhledem k tomu, že aplikace generuje nové dotazy po celou dobu, je při kompilaci dotazů vyčerpána podstatná část systémových prostředků, která není optimální. K této situaci obvykle dochází, pokud vaše aplikace generuje dotazy (namísto použití uložených procedur nebo parametrizovaných dotazů) nebo pokud spoléhá na objektově relační rozhraní mapování, která ve výchozím nastavení generují dotazy. 
 
Pokud ovládáte kód aplikace, můžete zvážit přepsání vrstvy přístupu k datům, aby bylo možné použít uložené procedury nebo parametrizované dotazy. Tato situace se ale dá taky zlepšit bez změn aplikace tím, že se vynutí Parametrizace dotazů pro celou databázi (všechny dotazy) nebo pro jednotlivé šablony dotazů se stejnou hodnotou hash dotazu. 

## <a name="next-steps"></a>Další kroky
- Další informace o [osvědčených postupech pro používání úložiště dotazů](concepts-query-store-best-practices.md)