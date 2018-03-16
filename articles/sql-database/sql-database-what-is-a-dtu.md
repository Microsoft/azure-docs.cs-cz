---
title: 'SQL Database: Co je DTU? | Dokumenty Microsoft'
description: "Vysvětlení jednotky transakce Azure SQL Database."
keywords: "možnosti databáze, výkon databáze"
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/14/2017
ms.author: carlrab
ms.openlocfilehash: 9d13541444f487ad6afb9f59c6c6ac646091d42c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>Databáze jednotky transakce (Dtu) a jednotky transakcí elastické databáze (Edtu)
Tento článek vysvětluje jednotky databázové transakce (DTU) a elastické jednotky databázové transakce (eDTU). Popisuje také, co se stane při dosažení maximálního počtu DTU nebo eDTU.  

## <a name="what-are-database-transaction-units-dtus"></a>Jaké jsou jednotky transakcí databáze (Dtu)?
Pro jednu databázi Azure SQL na úrovni výkonu specifických v rámci [vrstvy služby](sql-database-single-database-resources.md), Microsoft zaručuje úroveň prostředky pro tuto databázi (bez ohledu na jakékoli jiné databáze v cloudu Azure) a poskytuje předvídatelný úroveň výkonu. Toto množství prostředků počítá se jako počet jednotky transakcí databáze nebo Dtu a je kombinaci měření procesoru, paměti a vstupu a výstupu (dat a transakční protokol vstupně-výstupních operací). Poměr mezi tyto prostředky se původně určený [OLTP srovnávacího testu zatížení](sql-database-benchmark-overview.md) navržený tak, aby Typická reálného OLTP úloh. Pokud vaše úlohy překročí množství kterýkoli z těchto prostředků, vaše propustnost je omezenému – což pomalejší výkon a vypršení časových limitů. Prostředky používané velikosti pracovní zátěže nemá dopad na prostředky, které jsou k dispozici pro ostatní databází SQL v cloudu Azure a prostředků používá jiné úlohy nemá dopad na prostředky, které jsou k dispozici k vaší databázi SQL.

![ohraničující pole](./media/sql-database-what-is-a-dtu/bounding-box.png)

Počet jednotek Dtu jsou zvláště užitečné pro pochopení relativní objem prostředků mezi databází SQL Azure na úrovních různých výkonu a úrovně služeb. Například zvýší počet jednotek Dtu zvýšením úrovně výkonu databáze rovná zvýší sadu prostředků, které jsou k dispozici pro tuto databázi. Například databáze Premium P11 se 1 750 DTU nabízí 350x více DTU výpočetního výkonu než databáze Basic s 5 DTU.  

Chcete-li získat podrobnější přehled o spotřeby prostředků (DTU) vašich úloh, použijte [Azure SQL Database Query Performance Insight](sql-database-query-performance.md) na:

- Identifikujte nejčastějších dotazů podle využití procesoru a doba trvání nebo provádění počtu, který lze ladit potenciálně pro zlepšení výkonu. Například dotaz intenzivním vstupně-výstupních operací může mít užitek z použití [techniky optimalizace v paměti](sql-database-in-memory.md) lepší využití paměti k dispozici na určité služby a výkonu úrovni.
- Přejděte na podrobné informace o dotazu, zobrazit historii využití prostředků a text.
- Výkon přístupu k ladění doporučení, které se zobrazí akce prováděné [Poradce pro funkci SQL Database](sql-database-advisor.md).

[Změnit úrovně služeb](sql-database-service-tiers.md) můžete kdykoli s minimální odstávkou aplikace (obecně v průměru méně než čtyři sekundy). Mnoha firmám a aplikacím stačí vytváření databází a nastavování výkonu na vyžádání, zejména v případě, že jsou vzorce používání relativně předvídatelné. Ale pokud vaše vzorce používání předvídatelné nejsou, může být správa nákladů a údržba obchodního modelu velmi těžká. V tomto scénáři použijete fondu elastické databáze s počtem jednotek Edtu, která jsou sdílena mezi několika databáze ve fondu.

![Úvod do služby SQL Database: DTU izolované databáze podle úrovní](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

## <a name="what-are-elastic-database-transaction-units-edtus"></a>Jaké jsou jednotky transakcí elastické databáze (Edtu)?
Spíše než poskytují vyhrazené sadu prostředků (Dtu) k databázi SQL, která je neustále dostupná bez ohledu na to, jestli není potřeba, můžete umístit do databáze [elastický fond](sql-database-elastic-pool.md) na databázi SQL serveru, který sdílí fond prostředků mezi tyto databáze. Sdílené prostředky v elastickém fondu měří elastické jednotky transakcí databáze nebo Edtu. Elastické fondy poskytují jednoduché a nákladově efektivní řešení správy výkonnostních cílů pro více databází, jejichž vzory používání se značně liší a nelze je předvídat. V elastickém fondu, může zaručit, že žádné jedna databáze používá všechny prostředky ve fondu a také že minimální objem prostředků je vždy k dispozici do databáze v elastickém fondu. 

![Úvod do služby SQL Database: eDTU podle vrstvy a úrovně](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Fond obdrží stanovený počet eDTU za stanovenou cenu. V rámci elastického fondu disponují jednotlivé databáze flexibilní možností automatického škálování v rámci nakonfigurovaných mezí. V případě velkého zatížení může databáze spotřebovávat více eDTU, aby splnila požadavky, zatímco při nízkém zatížení databáze spotřebovávají méně eDTU a databáze bez zatížení dokonce nespotřebovávají žádné eDTU. Zřizování prostředků pro celý fond, a nikoli pro jednotlivé databáze, zjednoduše úlohy správy a umožňuje předem odhadnout náklady na fond.

Další eDTU lze do existujícího fondu přidat bez jakéhokoli výpadku databáze a bez jakéhokoli vlivu na databáze ve fondu. Podobně platí, že pokud již přidané eDTU nejsou potřebné, lze je z existujícího fondu kdykoli odebrat. Do fondu můžete přidávat databáze nebo je z něj odebírat, nebo můžete omezit množství eDTU, které databáze může využívat v případě velkého zatížení a vyhradit tak eDTU pro ostatní databáze. Pokud databáze podle předpokladu nedostatečně využívá prostředky, můžete ji odebrat z fondu a nakonfigurovat jako izolovanou databázi s předvídatelným množstvím požadovaných prostředků.

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Jak lze určit počet DTU potřebných pro určitou úlohu?
Pokud máte v úmyslu migrovat existující úlohu místního počítače nebo virtuálního počítače SQL Server do Azure SQL Database, můžete k odhadnutí potřebného počtu DTU použít [Kalkulačku DTU](http://dtucalculator.azurewebsites.net/). Pro existující úlohy Azure SQL Database můžete použít [SQL Database Query Performance Insight](sql-database-query-performance.md), abyste pochopili spotřebu databázových prostředků (TU) a lépe porozuměli možnostem optimalizace úloh. Můžete také použít DMV [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) a získat informace o spotřebě prostředků za poslední hodinu. Můžete také zadávat dotazy na zobrazení katalogu [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx). Získáte stejná data za posledních 14 dní, průměry za pětiminutová období ale mohou být méně přesné.

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Jak poznám, že by pro mě používání elastického fondu prostředků bylo výhodné?
Fondy jsou vhodné pro velký počet databází s konkrétními vzory využití. Pro danou databázi je tento vzor charakterizován nízkou mírou průměrného využití s relativně málo častými nárůsty využití. SQL Database automaticky vyhodnotí historické údaje používání prostředků databází na existujícím serveru SQL Database a doporučí odpovídající konfigurace fondu na webu Azure Portal. Další informace najdete v tématu [Kdy je vhodné používat elastický fond?](sql-database-elastic-pool.md)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>Co se stane, když dosáhnu Moje maximální Dtu?
Úrovně výkonu se kalibrují a řídí tak, aby poskytovaly zdroje potřebné ke spuštění vaší úlohy databáze až po maximální limity, které jsou pro vybranou úroveň služeb či úroveň výkonu povoleny. Pokud úloha dosahuje limity pro procesory / datový vstup/výstup / vstup/výstup protokolu, budete i nadále využívat prostředky na maximální povolené úrovni; je ale pravděpodobné, že se u vašich dotazů zvýší latence. Tyto limity nevedou k žádným chybám, ale mohou zpomalit zpracování úlohy, pokud se zpomalení nezvýší natolik, že začne docházet k vypršení časového limitu dotazů. Pokud dosahujete limitů pro maximální povolený počet souběžných relací/požadavků uživatelů (pracovních vláken), zobrazují se explicitní chyby. Informace o limitech jiných prostředků než procesory, paměť, datový vstup/výstup či vstup výstup protokolu transakcí najdete v článku [Limity prostředků Azure SQL Database]( sql-database-resource-limits.md#what-happens-when-database-and-elastic-pool-resource-limits-are-reached).

## <a name="next-steps"></a>Další kroky
* V tématu [vrstvy služby](sql-database-service-tiers.md) pro informace o Dtu a Edtu, které jsou k dispozici pro izolované databáze a pro elastické fondy, stejně jako omezení na prostředky než procesoru, paměti, vstupně-výstupních dat a transakce protokolu vstupně-výstupní operace.
* Chcete-li pochopit svou spotřebu (DTU), přečtěte si článek [SQL Database Query Performance Insight](sql-database-query-performance.md).
* Chcete-li lépe pochopit metodiku používanou úlohou srovnávacího testu OLTP k určení kombinace pro jednotku DTU, přečtěte si článek [Přehled srovnávacích testů SQL Database](sql-database-benchmark-overview.md).
