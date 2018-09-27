---
title: Scénáře použití Query Store ve službě Azure Database for PostgreSQL
description: Tento článek popisuje některé scénáře pro Query Store ve službě Azure Database for PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: 3cd3ebf86221db1531bd19e94bb072b4559d07d6
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47395589"
---
# <a name="usage-scenarios-for-query-store"></a>Mezi scénáře použití pro Query Store

**Platí pro:** Azure Database for PostgreSQL 9.6 a 10

> [!IMPORTANT]
> Funkce Query Store je ve verzi Public Preview v omezeném počtu oblastí.

Query Store v širokou škálu scénářů, ve kterých je nejdůležitější výkon sledování a údržba předvídatelné zatížení můžete použít. Vezměte v úvahu následující příklady: 
- Identifikace a optimalizace nákladné nejčastější dotazy 
- A / B testování 
- Zachování výkonu stabilní během upgradu 
- Identifikace a zlepšení ad hoc úlohy 

## <a name="identify-and-tune-expensive-queries"></a>Identifikovat a vyladit nákladným dotazům 

### <a name="identify-longest-running-queries"></a>Identifikujte nejdelší spuštěných dotazů 
Použití [Query Performance Insight](concepts-query-performance-insight.md) zobrazení na webu Azure Portal rychle identifikovat nejdelší spuštěné dotazy. Tyto dotazy se obvykle mají spotřebovávat značné množství prostředků. Optimalizace nejdéle probíhající dotazy lze vylepšit výkon uvolnění prostředků pro použití jiné dotazy spuštěnými ve vašem systému. 

### <a name="target-queries-with-performance-deltas"></a>Cíl dotazů s rozdíly výkonu 
Query Store řezy data o výkonu do časových oken, takže můžete sledovat výkon dotazu v čase. To vám pomůže přesně určit která přispívají dotazy ke zvýšení celkového času stráveného. Díky tomu vám pomůžou cílové Poradce při potížích pro vaši úlohu.

### <a name="tuning-expensive-queries"></a>Ladění nákladným dotazům 
Při určování dotazu s výkonem neoptimální akci, kterou zvolíte, závisí na povaze problému: 
- Použití [doporučení k výkonu](concepts-performance-recommendations.md) k určení, jestli se nějaké navrhované indexy. Pokud ano, vytvořte index a potom Query Store slouží k vyhodnocení výkonu dotazů po vytvoření indexu. 
- Ujistěte se, že jsou statistiky aktuální pro základní tabulky používán dotazem.
- Spotřebovávaly nákladným dotazům. Například využít výhod Parametrizace dotazů a snížení využití dynamické SQL. Při čtení dat, jako jsou použití filtrování na straně databáze, ne na straně aplikace data, implementovat optimální logiku. 


## <a name="ab-testing"></a>A / B testování 
Použijte dotaz Store porovnat výkon úloh před a po ní, které máte v plánu zavedení aplikace mění. Příklady scénářů pro použití Query Store k vyhodnocení dopadu prostředí nebo aplikace změnit na výkon úloh: 
- Zavádí novou verzi aplikace. 
- Přidání další prostředky na server. 
- Vytvoření chybějících indexů na tabulky odkazované procedurou aktivovanou nákladným dotazům. 
 
V některém z těchto scénářů platí následující pracovní postup: 
1. Spuštění vaší úlohy pomocí služby Query Store před změnou plánované ke generování základní úrovně výkonu. 
2. Použijte změny aplikace, které se v tuto chvíli řízené v čase. 
3. Pokračujte v dostatečně dlouho spuštěná úloha pro generování bitové kopie systému výkonu po provedení změny. 
4. Porovnání výsledků z před a po provedení změny. 
5. Rozhodněte, jestli se má zachovat změn nebo vrácení zpět. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Identifikujte a Zlepšete ad hoc úlohy 
Některé úlohy není třeba dominantní dotazy, které můžete optimalizovat pro zvýšení výkonu aplikací. Tyto úlohy jsou obvykle rozdělení s relativně velký počet jedinečných dotazy, každý z nich využívání část systémové prostředky. Každá jedinečná dotaz provádí zřídka, takže jednotlivě že využití modulu runtime není důležité. Na druhé straně vzhledem k tomu, že aplikace vygenerovala neustále nové dotazy, podstatnou část systémové prostředky stráveného při kompilaci dotazu, což není ideální. Obvykle taková situace nastane, pokud vaše aplikace generuje dotazy (namísto použití uložené procedury a parametrizované dotazy), nebo pokud závisí na objektově relační mapování rozhraní, které generují dotazů ve výchozím nastavení. 
 
Pokud máte pod kontrolou kódu aplikace, můžete zvážit přepisování vrstvy přístupu k datům pomocí uložené procedury nebo parametrizovaných dotazů. Ale tato situace může také zlepšit beze změn aplikací vynucením Parametrizace dotazů pro celou databázi (všechny dotazy) nebo pro konkrétní dotaz šablony se stejnou hodnotu hash dotazu. 

## <a name="next-steps"></a>Další postup
- Další informace o [osvědčené postupy pro používání Query Store](concepts-query-store-best-practices.md)