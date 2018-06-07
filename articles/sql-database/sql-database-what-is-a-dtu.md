---
title: 'SQL Database: Co je DTU? | Dokumenty Microsoft'
description: Vysvětlení jednotky transakce Azure SQL Database.
keywords: možnosti databáze, výkon databáze
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 50b12de2400f0db90108e3eeb158357039ac92ec
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649818"
---
# <a name="database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>Databáze jednotky transakce (Dtu) a jednotky transakcí elastické databáze (Edtu)
Tento článek vysvětluje jednotky databázové transakce (DTU) a elastické jednotky databázové transakce (eDTU). Popisuje také, co se stane při dosažení maximálního počtu DTU nebo eDTU. Konkrétní informace o cenách, najdete v části [Azure SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/single/).

## <a name="what-are-database-transaction-units-dtus"></a>Jaké jsou jednotky transakcí databáze (Dtu)?
Pro jednu databázi Azure SQL na úrovni výkonu specifických v rámci [vrstvy služby](sql-database-single-database-resources.md), Microsoft zaručuje úroveň prostředky pro tuto databázi (nezávisle na jakékoli jiné databáze v cloudu Azure), poskytování předvídatelný úroveň výkonu. Objem prostředků počítá se jako počet jednotky transakcí databáze nebo Dtu a je určena připojené výpočty, úložiště a vstupně-výstupní operace prostředky. Poměr mezi tyto prostředky se původně určený [OLTP srovnávacího testu zatížení](sql-database-benchmark-overview.md), navrženou Typická reálného OLTP úloh. Pokud vaše úlohy překročí množství kterýkoli z těchto prostředků, vaše propustnost je omezenému – což pomalejší výkon a vypršení časových limitů. Prostředky používané velikosti pracovní zátěže nemají vliv na jiné databáze SQL v cloudu Azure dostupné prostředky a prostředky používané další úlohy, nemá dopad na prostředky, které jsou k dispozici k vaší databázi SQL.

![ohraničující pole](./media/sql-database-what-is-a-dtu/bounding-box.png)

Počet jednotek Dtu jsou zvláště užitečné pro pochopení relativní objem prostředků mezi databází SQL Azure na úrovních různých výkonu a úrovně služeb. Například zvýší počet jednotek Dtu zvýšením úrovně výkonu databáze rovná zvýší sadu prostředků, které jsou k dispozici pro tuto databázi. Například databáze Premium P11 se 1 750 DTU nabízí 350x více DTU výpočetního výkonu než databáze Basic s 5 DTU.  

Chcete-li získat podrobnější přehled o spotřeby prostředků (DTU) vašich úloh, použijte [Azure SQL Database Query Performance Insight](sql-database-query-performance.md) na:

- Identifikujte nejčastějších dotazů podle využití procesoru a doba trvání nebo provádění počtu, který lze ladit potenciálně pro zlepšení výkonu. Například dotaz intenzivním vstupně-výstupní operace může mít užitek z použití [techniky optimalizace v paměti](sql-database-in-memory.md) lepší využití paměti k dispozici na určité služby a výkonu úrovni.
- Přejděte na podrobné informace o dotazu, zobrazit historii využití prostředků a text.
- Výkon přístupu k ladění doporučení, které se zobrazí akce prováděné [Poradce pro funkci SQL Database](sql-database-advisor.md).

Můžete změnit [DTU úrovně služeb](sql-database-service-tiers-dtu.md) kdykoli s minimálními výpadky do vaší aplikace (obvykle průměrování než čtyři sekund). Mnoha firmám a aplikacím stačí vytváření databází a nastavování výkonu na vyžádání, zejména v případě, že jsou vzorce používání relativně předvídatelné. Ale pokud vaše vzorce používání předvídatelné nejsou, může být správa nákladů a údržba obchodního modelu velmi těžká. V tomto scénáři použijete fondu elastické databáze s počtem jednotek Edtu, jež jsou sdílena mezi více databází ve fondu.

![Úvod do služby SQL Database: DTU izolované databáze podle úrovní](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

## <a name="what-are-elastic-database-transaction-units-edtus"></a>Jaké jsou jednotky transakcí elastické databáze (Edtu)?
Spíše než poskytují vyhrazené sadu prostředků (Dtu), které nemusí být vždy potřebné pro databázi SQL, která je vždy k dispozici, můžete umístit do databáze [elastický fond](sql-database-elastic-pool.md) na databázi SQL serveru, který sdílí fond prostředků mezi Tyto databáze. Sdílené prostředky v elastickém fondu se měří elastické jednotky transakcí databáze nebo Edtu. Elastické fondy poskytují jednoduché nákladově efektivní řešení ke správě výkonnostní cíle pro více databází s široce různých a vzorce nepředvídatelné. Fondu elastické databáze zaručuje, že prostředky nemůže být využívány službou jedna databáze ve fondu, zatímco Každá databáze ve fondu zajistíte vždy obsahuje minimální množství potřebné prostředky, které jsou k dispozici. 

![Úvod do služby SQL Database: eDTU podle vrstvy a úrovně](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Fond je uveden se stanoveným počtem jednotek Edtu pro sadu ceny. V rámci elastického fondu disponují jednotlivé databáze flexibilní možností automatického škálování v rámci nakonfigurovaných mezí. Databáze v rámci větší zatížení spotřebuje další Edtu potřeby. Databáze pod světlejší zatížení budou využívat menší Edtu. Databáze s žádné zatížení bude využívat žádné Edtu. Podle zřizování prostředků pro celý fond, místo na databázi, úlohy správy jsou zjednodušená, poskytuje předvídatelný nároky pro fond.

Další eDTU lze do existujícího fondu přidat bez jakéhokoli výpadku databáze a bez jakéhokoli vlivu na databáze ve fondu. Podobně platí, že pokud již přidané eDTU nejsou potřebné, lze je z existujícího fondu kdykoli odebrat. Můžete přidat nebo odečíst databáze ve fondu nebo limit množství Edtu databázi můžete použít v případě velkého zatížení tak, aby vyhradil Edtu pro jiné databáze. Pokud databázi je předvídatelné pod využití prostředků, můžete přesunout mimo fondu a nakonfigurovat ho jako jednu databázi s velikostí předvídatelný požadované prostředky.

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Jak lze určit počet DTU potřebných pro určitou úlohu?
Pokud máte v úmyslu migrovat existující úlohu místního počítače nebo virtuálního počítače SQL Server do Azure SQL Database, můžete k odhadnutí potřebného počtu DTU použít [Kalkulačku DTU](http://dtucalculator.azurewebsites.net/). Pro existující úlohy Azure SQL Database, můžete použít [SQL databáze Query Performance Insight](sql-database-query-performance.md) porozumět vaší spotřeby prostředků databáze (Dtu) Chcete-li získat podrobnější přehled pro optimalizaci velikosti pracovní zátěže. Můžete také [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV zobrazíte spotřeby prostředků za poslední hodinu. Alternativně zobrazení katalogu [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) zobrazí spotřeby prostředků za posledních 14 dní, ale v nižší přesnost průměry pět minut.

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Jak poznám, že by pro mě používání elastického fondu prostředků bylo výhodné?
Fondy jsou vhodné pro velký počet databází s konkrétními vzory využití. Tento vzor pro danou databázi je charakterizovaná průměr nízkou míru využívání s relativně málo časté využití. SQL Database automaticky vyhodnotí historické údaje používání prostředků databází na existujícím serveru SQL Database a doporučí odpovídající konfigurace fondu na webu Azure Portal. Další informace najdete v tématu [Kdy je vhodné používat elastický fond?](sql-database-elastic-pool.md)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>Co se stane, když dosáhnu Moje maximální Dtu?
Úrovně výkonu jsou kalibrován a řídí zajistit prostředky potřebné ke spuštění úlohy databáze až do maximální povolený pro vaše úroveň vrstvy a výkonu vybrané službě. Pokud vaše úlohy je stiskne jedním z omezení procesoru/Data vstupně-výstupní operace nebo protokolu vstupně-výstupní operace, nadále budete dostávat maximální úroveň prostředků, které jsou povolené, ale také pravděpodobně si všimnete latence vyšší dotazu. Tyto limity nevedou k žádným chybám, ale mohou zpomalit zpracování úlohy, pokud se zpomalení nezvýší natolik, že začne docházet k vypršení časového limitu dotazů. Pokud dostanete relací/žádosti maximální povolené souběžných uživatelů (pracovních vláken), zobrazí se explicitní chyby. V tématu [limitů prostředků Azure SQL Database]( sql-database-dtu-resource-limits.md#what-happens-when-database-and-elastic-pool-resource-limits-are-reached) informace o limitů prostředků, které nesouvisí s Procesorem, pamětí, vstupně-výstupní operace dat nebo transakcí protokolu vstupně-výstupní operace.

## <a name="next-steps"></a>Další postup
* V tématu [na základě DTU nákupní model](sql-database-service-tiers-dtu.md) pro informace o Dtu a Edtu, které jsou k dispozici pro izolované databáze a pro elastické fondy, stejně jako omezení na prostředky než procesoru, paměti, vstupně-výstupní operace dat a transakce protokolu vstupně-výstupní operace.
* V tématu [nákupní model (preview) na základě vCore](sql-database-service-tiers-vcore.md) informace o úrovně přidělení a služby na základě vCore prostředků. 
* Chcete-li pochopit svou spotřebu (DTU), přečtěte si článek [SQL Database Query Performance Insight](sql-database-query-performance.md).
* Chcete-li lépe pochopit metodiku používanou úlohou srovnávacího testu OLTP k určení kombinace pro jednotku DTU, přečtěte si článek [Přehled srovnávacích testů SQL Database](sql-database-benchmark-overview.md).
