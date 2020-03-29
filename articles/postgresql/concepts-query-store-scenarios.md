---
title: Scénáře úložiště dotazů – databáze Azure pro PostgreSQL – jeden server
description: Tento článek popisuje některé scénáře pro úložiště dotazů v Azure Database for PostgreSQL - jeden server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 31e3f82b6ea1b1fc15c0832dc03edce2a59f1e1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768346"
---
# <a name="usage-scenarios-for-query-store"></a>Scénáře použití pro úložiště dotazů

**Platí pro:** Databáze Azure pro PostgreSQL – verze s jedním serverem 9.6, 10, 11

Úložiště dotazů můžete použít v celé řadě scénářů, ve kterých je sledování a udržování předvídatelného výkonu pracovního vytížení důležité. Představte si následující příklady: 
- Identifikace a ladění nejvyšších drahých dotazů 
- A/B testování 
- Udržování stabilního výkonu během upgradů 
- Identifikace a zlepšení úloh ad hoc 

## <a name="identify-and-tune-expensive-queries"></a>Identifikace a ladění nákladných dotazů 

### <a name="identify-longest-running-queries"></a>Identifikace nejdéle spuštěných dotazů 
Pomocí zobrazení [Přehled výkonu dotazů](concepts-query-performance-insight.md) na webu Azure Portal můžete rychle identifikovat nejdéle spuštěné dotazy. Tyto dotazy obvykle mají tendenci spotřebovávat značné množství prostředků. Optimalizace nejdéle běžících otázek může zvýšit výkon uvolněním prostředků pro použití jinými dotazy spuštěnými v systému. 

### <a name="target-queries-with-performance-deltas"></a>Cílové dotazy s rozdíly výkonu 
Úložiště dotazů rozdělí data o výkonu do časových oken, takže můžete sledovat výkon dotazu v průběhu času. To vám pomůže přesně určit, které dotazy přispívají ke zvýšení celkové doby strávené. V důsledku toho můžete provést cílené řešení potíží s vaší úlohou.

### <a name="tuning-expensive-queries"></a>Ladění nákladných dotazů 
Při identifikaci dotazu s neoptimálním výkonem závisí akce, kterou podniknete, na povaze problému: 
- Pomocí [doporučení výkonu](concepts-performance-recommendations.md) můžete zjistit, zda existují nějaké navrhované indexy. Pokud ano, vytvořte index a potom pomocí úložiště dotazů vyhodnoťte výkon dotazu po vytvoření indexu. 
- Ujistěte se, že statistiky jsou aktuální pro podkladové tabulky používané dotazem.
- Zvažte přepisování drahých dotazů. Můžete například využít parametrizaci dotazu a omezit použití dynamického sql. Implementujte optimální logiku při čtení dat, jako je použití filtrování dat na straně databáze, nikoli na straně aplikace. 


## <a name="ab-testing"></a>A/B testování 
Pomocí úložiště dotazů můžete porovnat výkon pracovního vytížení před a po změně aplikace, kterou chcete zavést. Příklady scénářů pro použití úložiště dotazů k posouzení dopadu prostředí nebo změny aplikace na výkon pracovního vytížení: 
- Zavedení nové verze aplikace. 
- Přidání dalších prostředků na server. 
- Vytváření chybějící indexy v tabulkách odkazuje nákladné dotazy. 
 
V některém z těchto scénářů použijte následující pracovní postup: 
1. Spusťte úlohu s úložištěm dotazů před plánovanou změnou a vygenerujte směrný plán výkonu. 
2. Použít změny aplikace (y) v řízeném okamžiku v čase. 
3. Pokračujte ve spuštění úlohy dostatečně dlouho na to, abyste po změně vygenerovali bitovou kopii výkonu systému. 
4. Porovnejte výsledky před a po změně. 
5. Rozhodněte se, zda chcete zachovat změnu nebo vrácení zpět. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Identifikace a zlepšení úloh ad hoc 
Některé úlohy nemají dominantní dotazy, které můžete vyladit ke zlepšení celkového výkonu aplikace. Tyto úlohy jsou obvykle charakterizovány relativně velkým počtem jedinečných dotazů, z nichž každý spotřebovává část systémových prostředků. Každý jedinečný dotaz se spouští zřídka, takže jednotlivá jejich spotřeba za běhu není kritická. Na druhou stranu vzhledem k tomu, že aplikace generuje nové dotazy po celou dobu, významná část systémových prostředků je vynaložena na kompilaci dotazů, což není optimální. Obvykle k této situaci dochází, pokud vaše aplikace generuje dotazy (namísto použití uložených procedur nebo parametrizované dotazy) nebo pokud se spoléhá na rozhraní mapování objektu relační, které generují dotazy ve výchozím nastavení. 
 
Pokud máte kontrolu nad kódem aplikace, můžete zvážit přepsání vrstvy přístupu k datům, abyste mohli používat uložené procedury nebo parametrizované dotazy. Tuto situaci však lze také zlepšit bez změn aplikace vynucením parametrizace dotazu pro celou databázi (všechny dotazy) nebo pro jednotlivé šablony dotazů se stejnou hodnotou hash dotazu. 

## <a name="next-steps"></a>Další kroky
- Další informace o [doporučených postupech používání Úložiště dotazů](concepts-query-store-best-practices.md)