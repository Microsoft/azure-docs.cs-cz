---
title: Azure SQL Database modely nákupu | Dokumentace Microsoftu
description: Další informace o zakoupení modelu pro službu Azure SQL Database.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/17/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: a4bf21f972da1aa92d6f127e8cbabb89a9c31489
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44719949"
---
# <a name="azure-sql-database-purchasing-models-and-resources"></a>Azure SQL Database nákupu modely a prostředky 

Azure SQL Database umožňuje jednoduše koupit plně spravovanou PaaS databázový stroj, který nejlépe vyhovuje vašim potřebám výkonu a nákladů. V závislosti na modelu nasazení Azure SQL Database můžete vybrat nákupní model, který nejlépe vyhovuje vašim potřebám: 
 - [Logické servery](sql-database-logical-servers.md) v [Azure SQL Database](sql-database-technical-overview.md) nabízí dva modely nákupu pro výpočty, úložiště a vstupně-VÝSTUPNÍCH prostředků: nákupní model založený na DTU a [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md). 
 - [Spravované instance](sql-database-managed-instance.md) jenom nabídky Azure SQL Database [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md).

Následující tabulku a graf porovnání a kontrast tyto dva modely nákupu.

|**Nákupní model**|**Popis**|**Nejlepší pro**|
|---|---|---|
|Model na základě DTU|Tento model je založen na připojené míře výpočetní prostředky, úložiště a vstupně-VÝSTUPNÍCH prostředků. Úrovně výkonu se vyjadřují v jednotkách transakcí databáze (DTU) pro samostatné databáze a jednotkách transakcí elastické databáze (eDTU) pro elastické fondy. Další informace o jednotkách Dtu a Edtu najdete v tématu [co jsou jednotky Dtu a Edtu](sql-database-service-tiers.md#what-are-database-transaction-units-dtus)?|Nejvhodnější pro zákazníky, kteří chtějí jednoduché, předem nakonfigurované možnosti prostředku.| 
|Model na základě virtuálních jader|Tento model umožňuje zvolit nezávisle na sobě výpočetní a úložnou kapacitu. Také umožňuje používat zvýhodněné hybridní využití Azure pro SQL Server k získání úspory nákladů.|Nejvhodnější pro zákazníky, kteří hodnota flexibilitu, řízení a transparentnost.|
||||  

![cenový model](./media/sql-database-service-tiers/pricing-model.png)

## <a name="vcore-based-purchasing-model"></a>nákupní model založený na virtuálních jádrech 

Virtuální jádro reprezentuje logický procesor nabízený s možností volby mezi generacemi hardwaru a fyzické charakteristiky hardwaru (například počet jader, paměti a velikost úložiště). Nákupní model založený na virtuálních jádrech poskytuje flexibilitu, kontrolu, transparentnost spotřeby jednotlivých prostředků a jednoduchý způsob převodu místních požadavků na zatížení do cloudu. Tento model umožňuje zvolit výpočetních, paměťových a úložiště na základě jejich potřebám provádění úloh. V nákupní model založený na virtuálních jádrech Zákazníci si mohou vybrat mezi [Obecné](sql-database-high-availability.md#standardgeneral-purpose-availability) a [pro důležité obchodní informace](sql-database-high-availability.md#premiumbusiness-critical-availability) úrovně služeb pro obě [izolované databáze](sql-database-single-database-scale.md), [spravované instance](sql-database-managed-instance.md), a [elastické fondy](sql-database-elastic-pool.md). 

Nákupní model založený na virtuálních jádrech umožňuje nezávisle na sobě vybrat úložnou a výpočetní prostředky, odpovídají zajištění místního výkonu a optimalizovat cena. V nákupní model založený na virtuálních jádrech zákazníci platí za:
- COMPUTE (úrovni služby a počet virtuálních jader a množství paměti a generaci) *
- Typ a velikost úložiště dat a protokolů 
- Počet IOs ** - pro [logické servery](sql-database-logical-servers.md) pouze
- Zálohování úložišť (pro čtení RA-GRS) ** 

\* V počáteční verzi public preview, logické procesory generace 4 jsou založené na Intel E5-2673 v3 (Haswell) 2,4 GHz procesorech.

\*\* Během období preview je zdarma sedm dní a IOs.

> [!IMPORTANT]
> Výpočetní prostředky, IOs, data a úložiště protokolů se bude fakturovat za databázi nebo elastický fond. Úložiště záloh se účtuje za každou databázi. Podrobné informace o Managed Instance poplatky, najdete v tématu [Azure SQL Database Managed Instance](sql-database-managed-instance.md).
> **Omezení oblasti:** nákupní model založený na virtuálních jádrech ještě není k dispozici v následujících oblastech: západní Evropa, Francie – střed, Velká Británie – Jih, Velká Británie – západ a Austrálie – jihovýchod.

Pokud vaše databáze nebo elastického fondu využívá víc než 300 DTU převod na vCore může snížit vaše náklady. Můžete převést pomocí rozhraní API podle výběru nebo na webu Azure portal, bez výpadků. Převod však není povinné. Pokud model nákupu DTU splňuje výkonu a obchodních požadavcích, měli byste pokračovat, jeho použití. Pokud se rozhodnete převést z modelu jednotek DTU na modelu virt. jader, by měl vybrat úroveň výkonu pomocí následující pravidlo: vyžaduje minimálně 1 virtuální jádro v úrovni General Purpose; každý 100 DTU na úrovni Standard Každý 125 DTU na úrovni Premium vyžaduje alespoň 1 virtuální jádro v úrovni pro důležité obchodní informace.

## <a name="dtu-based-purchasing-model"></a>Nákupní model založený na DTU

Jednotky transakcí databáze (DTU) představuje kombinaci měření procesoru, paměti, čte a zapisuje. Nákupní model založený na DTU nabízí sadu předem nakonfigurované sady výpočetních prostředků a zahrnuté úložiště na jednotce různé úrovně výkonu aplikace. Zákazníci, kteří dávají přednost jednoduchosti, kterou nabízejí předem nakonfigurované sady prostředků a pevných plateb každý měsíc, může se stát model založený na DTU více odpovídají jejich potřebám. V nákupní model založený na DTU Zákazníci si mohou vybrat mezi **základní**, **standardní**, a **Premium** úrovně služeb pro obě [izolované databáze](sql-database-single-database-scale.md) a [elastické fondy](sql-database-elastic-pool.md). Tento model nákupu není k dispozici v [spravované instance](sql-database-managed-instance.md).

### <a name="what-are-database-transaction-units-dtus"></a>Co jsou jednotky databázové transakce (Dtu)?
Pro izolované databáze Azure SQL na konkrétní úroveň výkonu v rámci [úroveň služby](sql-database-single-database-scale.md), Microsoft zaručuje určité úrovně prostředků pro tuto databázi (nezávisle na jakékoli jiné databáze v cloudu Azure), poskytování předvídatelný úroveň výkonu. Objem prostředků se počítá jako počet jednotek databázové transakce nebo počet jednotek Dtu a je jako součást balíčku míra výpočty, úložiště a vstupně-VÝSTUPNÍCH prostředků. Poměr mezi těmito prostředky byl původně určené [úlohou srovnávacího testu OLTP](sql-database-benchmark-overview.md), navržená tak, aby jako typická pro úlohy OLTP v reálném světě. Pokud vaše úloha překračuje množství některý z těchto prostředků, propustnost je omezené – následek pomalejší výkon a časové limity. Prostředky využívané třídou úlohy nemají vliv na prostředky dostupné pro jiné databáze SQL v cloudu Azure a prostředky využívané třídou jiné úlohy nemají vliv na prostředky dostupné pro vaši službu SQL database.

![ohraničujícího rámečku](./media/sql-database-what-is-a-dtu/bounding-box.png)

Počet jednotek Dtu jsou zvláště užitečná pro pochopení relativní množství prostředků mezi databázemi Azure SQL na různé úrovně výkonu a úrovně služeb. Například zdvojnásobení jednotek Dtu zvýšením úrovně výkonu databáze odpovídá zdvojnásobení sady prostředků, které jsou k dispozici pro tuto databázi. Například databáze Premium P11 se 1 750 DTU nabízí 350x více DTU výpočetního výkonu než databáze Basic s 5 DTU.  

Chcete-li získat podrobnější přehled o spotřebě prostředků (DTU) vašich úloh, použijte [Azure SQL Database Query Performance Insight](sql-database-query-performance.md) na:

- Identifikujte hlavní dotazy podle počtu CPU/doba trvání/spuštění, který může být potenciálně vyladěný za účelem vylepšení výkonu. Například dotaz náročné na vstupně-výstupních operací by mohlo prospět použití [techniky optimalizace v paměti](sql-database-in-memory.md) lepší využití paměti k dispozici na na určité služby vrstvu a úroveň výkonu.
- Přejít na podrobnosti o dotazu, zobrazit jeho historii využití prostředků a text.
- Doporučení, které ukazují akce prováděné pro optimalizaci výkonu přístupu [služby SQL Database Advisor](sql-database-advisor.md).

### <a name="what-are-elastic-database-transaction-units-edtus"></a>Co jsou elastické jednotky databázové transakce (Edtu)?
Spíše než poskytuje vyhrazenou sadu prostředků (Dtu), které nemusí být vždy nezbytné pro SQL Database, která je vždy k dispozici, můžete umístit do databáze [elastického fondu](sql-database-elastic-pool.md) na server služby SQL Database, která sdílí fond prostředků mezi Tyto databáze. Sdílené prostředky v elastickém fondu jsou měřeny pomocí elastic Database Transaction Units nebo Edtu. Elastické fondy poskytují jednoduché nákladově efektivní řešení správy výkonnostních cílů pro více databází s značně liší a nepředvídatelným způsobem. Elastický fond zaručuje, že prostředky nemůže být zpracován jedna databáze ve fondu, zatímco vždy zajištění všem databázím ve fondu má minimální množství potřebné prostředky, které jsou k dispozici. 

![Úvod do služby SQL Database: eDTU podle vrstvy a úrovně](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Fond je uveden stanovený počet jednotek Edtu za stanovenou cenu. V rámci elastického fondu disponují jednotlivé databáze flexibilní možností automatického škálování v rámci nakonfigurovaných mezí. Databáze při větším zatížení bude spotřebovávat více Edtu, které mají pokrýt poptávku. Databáze pod označením zatížení se spotřebovávají méně Edtu. Databáze s žádné zatížení bude nespotřebovávají žádné Edtu. Zřizování prostředků pro celý fond, spíše než jednotlivé databáze, jsou úlohy správy zjednodušené, poskytuje předvídatelný rozpočet pro fond.

Další eDTU lze do existujícího fondu přidat bez jakéhokoli výpadku databáze a bez jakéhokoli vlivu na databáze ve fondu. Podobně platí, že pokud již přidané eDTU nejsou potřebné, lze je z existujícího fondu kdykoli odebrat. Můžete přičíst nebo odečíst databáze ve fondu nebo omezit množství Edtu, které databáze můžete použít v případě velkého zatížení a vyhradit tak Edtu pro ostatní databáze. Pokud databáze je předvídatelně využívá prostředky, můžete ji odebrat z fondu a nakonfigurovat jako izolovanou databázi s předvídatelným množstvím požadovaných prostředků.

### <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Jak lze určit počet DTU potřebných pro určitou úlohu?
Pokud máte v úmyslu migrovat existující úlohu místního počítače nebo virtuálního počítače SQL Server do Azure SQL Database, můžete k odhadnutí potřebného počtu DTU použít [Kalkulačku DTU](http://dtucalculator.azurewebsites.net/). Pro existující úlohy Azure SQL Database, můžete použít [SQL Database Query Performance Insight](sql-database-query-performance.md) abyste pochopili spotřebu databázových prostředků (Dtu), abyste získali hlubší vhled pro optimalizaci vašich úloh. Můžete také použít [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) zobrazení dynamické správy k zobrazení využití prostředků za poslední hodinu. Můžete také zobrazit katalog [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) zobrazí využití prostředků za posledních 14 dní, ale v méně přesné průměry pět minut.

### <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Jak poznám, že by pro mě používání elastického fondu prostředků bylo výhodné?
Fondy jsou vhodné pro velký počet databází s konkrétními vzory využití. Pro danou databázi tento vzor charakterizován průměr nízké využití s relativně málo častými nárůsty využití. SQL Database automaticky vyhodnotí historické údaje používání prostředků databází na existujícím serveru SQL Database a doporučí odpovídající konfigurace fondu na webu Azure Portal. Další informace najdete v tématu [Kdy je vhodné používat elastický fond?](sql-database-elastic-pool.md)


## <a name="next-steps"></a>Další postup

- Nákupní model založený na virtuálních jádrech najdete v části [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md)
- Nákupní model založený na DTU najdete v části [nákupní model založený na DTU](sql-database-service-tiers-dtu.md).
