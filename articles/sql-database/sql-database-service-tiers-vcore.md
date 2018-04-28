---
title: Služba Azure SQL Database - vCore | Microsoft Docs
description: Další informace o úrovních služeb pro jeden a zadejte velikost úložiště a úrovně výkonu databáze fondu.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/09/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: be5ecfdd4465d721dee49c4963cb2267b2b0a40a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="vcore-based-purchasing-model-for-azure-sql-database-preview"></a>na základě vCore nákupní model pro Azure SQL Database (preview)

[Azure SQL Database](sql-database-technical-overview.md) nabízí dva modely nákupu pro výpočty, úložiště a vstupně-výstupní operace prostředky: nákupní model na základě DTU a nákupní model (preview) na základě vCore. Následující tabulku a graf porovnání a porovnejte tyto dva modely nákupu.

> [!IMPORTANT]
> Na základě DTU nákupní model, najdete v části [na základě DTU nákupní model](sql-database-service-tiers-dtu.md).


|**Nákupní model**|**Popis**|**Nejvhodnější pro**|
|---|---|---|
|Na základě DTU modelu|Tento model je založen na připojené měření výpočty, úložiště a vstupně-výstupní operace prostředky. Úrovně výkonu se vyjadřují v jednotkách transakcí databáze (DTU) pro samostatné databáze a jednotkách transakcí elastické databáze (eDTU) pro elastické fondy. Další informace o Dtu a Edtu najdete v tématu [co jsou Dtu a Edtu](sql-database-what-is-a-dtu.md)?|Nejvhodnější pro zákazníky, kteří chtějí možnosti jednoduchý, předem nakonfigurovaných prostředků.| 
|na základě vCore modelu|Tento model můžete nezávisle škálovat výpočetní a úložnou kapacitu. Také umožňuje použít k získání úsporu nákladů Benefit hybridní Azure pro systém SQL Server.|Nejvhodnější pro zákazníky, kteří hodnota flexibilitu, řízení a průhlednost.|
||||  

![cenový model](./media/sql-database-service-tiers/pricing-model.png)

## <a name="vcore-based-purchasing-model--preview"></a>na základě vCore nákupní model (preview)

Virtuální základní představuje logický procesor nabízí s možností si vybrat mezi generací hardwaru. Nákupní model (preview) na základě vCore poskytuje flexibilitu, řízení, průhlednost spotřebu jednotlivých prostředků a snadný způsob, jak převede místní požadavků na zatížení do cloudu. Tento model umožňuje škálování výpočetní kapacity, paměť a úložiště podle jejich potřeb pracovního vytížení. V na základě vCore nákupu modelu (preview) Zákazníci můžete vybrat mezi obecné účely a obchodními úrovněmi kritické služby (preview) pro obě [jedna databáze](sql-database-single-database-resources.md) a [elastické fondy](sql-database-elastic-pool.md). 

Úrovně služeb jsou rozlišené pomocí rozsah úrovně výkonu, vysoká dostupnost, selhání izolace, typy úložiště a vstupně-výstupní operace. Zákazník musí nakonfigurovat samostatně požadované úložiště a jejich uchovávání období pro zálohování. Při použití modelu vCore, jsou způsobilé pro až na 30 procent úspor s izolované databáze i elastické fondy [výhody použití hybridní Azure pro systém SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md).

Základě vCore nákupu modelu (preview) Zákazníci mzdy pro:
- Výpočetní (úroveň služby + číslo vCores + generování hardwaru) *
- Typ a velikost úložiště, protokolu a data 
- Počet IOs **
- Zálohování úložiště (RA-GRS) ** 

\* V původní verzi public preview, logických procesorů 4 generace jsou založené na v3 Intel E5-2673 2,4 GHz procesorů (Haswell)

\*\* Verzi Preview jsou zdarma zálohy a IOs 7 dnů

> [!IMPORTANT]
> Výpočetní, IOs, data a budou se účtovat úložiště protokolů pro databáze nebo elastického fondu. Úložiště zálohy je účtován na každou databázi. Podrobnosti instance spravované poplatky najdete [Azure SQL Database spravované Instance](sql-database-managed-instance.md).

> [!IMPORTANT]
> Oblast omezení: 
>
> Nákupní model (preview) na základě vCore dosud nejsou k dispozici v Austrálie – jihovýchod. Ve verzi preview není k dispozici v následujících oblastech: západní Evropa, střední Francie, Spojené království – jih a Spojené království – západ.
> 

## <a name="choosing-service-tier-compute-memory-storage-and-io-resources"></a>Výběr vrstvy služeb, výpočetní, paměť, úložiště a vstupně-výstupní operace prostředky

Převádění na základě vCore nákupní model (preview) umožňuje nezávisle škálovat výpočetní a úložnou kapacitu, shodovat s místními výkonu a optimalizovat ceny. Pokud vaše databáze nebo elastického fondu spotřebovává více než 300 DTU převod na vCore může snížit vaše náklady. Můžete převést pomocí rozhraní API podle volby nebo pomocí portálu Azure, nedojde k výpadku. Převod však není požadováno. Pokud nákupní model DTU splňuje vaše požadavky na výkon a business, by měly pokračovat, jeho použití. Pokud se rozhodnete převést z modelu DTU vCore-model, měli byste vybrat úroveň výkonu pomocí následující pravidlo: každý 100 DTU ve standardní vrstvě vyžaduje minimálně 1 vCore a každý 125 DTU v úrovni Premium vyžaduje minimálně 1 vCore.

Následující tabulka vám pomůže pochopit rozdíly mezi těmito dvěma vrstvami:

||**Obecné účely**|**Kritické obchodní**|
|---|---|---|
|Nejvhodnější pro|Většinu úloh firmy. Nabízí rozpočet orientované vyrovnáváním a škálovatelné možnosti výpočetního prostředí a úložiště.|Podnikové aplikace s vysokými nároky na V/V. Nabízí nejvyšší odolnost proti selhání s využitím několika izolovaných replik.|
|Compute|1 až 16 vCore|1 až 16 vCore|
|Memory (Paměť)|7 GB za jádra |7 GB za jádra |
|Úložiště|Vzdálené úložiště Premium, 5 GB – 4 TB|Místní úložiště SSD, 5 GB – 1 TB|
|Propustnost vstupně-výstupní operace (přibližnou)|500 IOPS na vCore s 7500 maximální IOPS|5000 IOPS za jádra|
|Dostupnost|1 repliky, bez škálování pro čtení|repliky 3, 1 [čtení škálování](sql-database-read-scale-out.md), zónu redundantní HA|
|Zálohování|RA-GRS, 7-35 dní (7 dní ve výchozím nastavení)|RA-GRS, 7-35 dní (7 dní ve výchozím nastavení) *|
|V paměti|neuvedeno|Podporováno|
|||

\* Během preview doba uchovávání záloh není Konfigurovatelný a je nastaven na 7 dní.

> [!IMPORTANT]
> Pokud budete potřebovat méně než jeden vCore výpočetní kapacitu, použijte na základě DTU nákupní model.

Podrobnosti o konkrétní úrovně výkonu a možnosti velikosti úložiště k dispozici pro izolované databáze najdete v tématu [limitů na základě vCore prostředků databáze SQL pro izolované databáze](sql-database-vcore-resource-limits.md#single-database-storage-sizes-and-performance-levels) a elastické fondy najdete v tématu [databáze SQL omezení zdrojů na základě vCore pro elastické fondy](sql-database-vcore-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

V tématu [nejčastější dotazy k databázi SQL](sql-database-faq.md) odpovědi na nejčastější dotazy. 

## <a name="storage-considerations"></a>Aspekty úložišť

Zvažte použití těchto zdrojů:
- Úložiště přidělené používá datových souborů (MDF) a soubory protokolu souborů (LDF).
- Každá úroveň výkonu podporuje maximální velikost databáze, s výchozí maximální velikost 32 GB.
- Když konfigurujete velikost požadovaná databáze (velikost MDF), 30 % dodatečné úložiště se automaticky přidá k podpoře LDF
- Můžete vybrat všechny velikosti databáze mezi 10 GB a maximální podporované
 - Pro standardní úložiště zvýšení nebo snížení velikosti v přírůstcích po 10 GB
 - Pro storage úrovně Premium zvětšit nebo zmenšit velikost v přírůstcích po 250 GB
- Ve vrstvě služeb obecné účely `tempdb` používá připojené SSD a toto úložiště náklady je součástí vCore ceny.
- V rámci firmy kritické úrovně služby `tempdb` připojené SSD s soubory MDF a LDF a úložiště databáze tempDB náklady je součástí ceny vCore sdílené složky.

> [!IMPORTANT]
> Budou účtovat celkové úložiště přidělené MDF a LDF.

Chcete-li monitorovat aktuální celková velikost MDF a LDF, použijte [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Chcete-li monitorovat aktuální velikost jednotlivých souborů MDF a LDF, použijte [katalogu sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

## <a name="backups-and-storage"></a>Zálohování a úložiště

Úložiště pro zálohování databáze je přidělen pro podporu bodem v možnostech doba obnovení (Možnosti PITR) a dlouhou dobu uchování zleva doprava databáze SQL. Toto úložiště je přidělen samostatně pro každou databázi a účtován jako dva samostatné za databáze poplatky. 

- **Možnosti PITR**: jednotlivé databáze zálohy se zkopírují do úložiště RA-GRS jsou automaticky. Velikost úložiště zvyšuje dynamicky vytvářené nových záloh.  Úložiště využívané týdenními úplnými zálohami, denními rozdílovými zálohami a zálohami protokolů transakcí se kopíruje každých 5 minut. Spotřeba úložiště závisí na míru změn databáze a dobu uchování. Můžete nakonfigurovat doby uchování samostatné pro každou databázi mezi 7 až 35 dnů. Velikost minimální úložiště rovna 1 x velikost dat je k dispozici bez dalších poplatků. Pro většinu databáze je toto množství dost pro ukládání záloh 7 dní.
- **Zleva doprava**: SQL Database nabízí možnost konfigurace dlouhodobé uchovávání úplných záloh pro až 10 let. Pokud je LTR zásada povolena, tato zálohy se ukládají do úložiště RA-GRS automaticky, ale můžete řídit, jak často se zkopírují zálohy. Chcete-li splnit požadavky různých kompatibility, můžete vybrat odlišných období uchování pro týdenní, měsíční nebo roční zálohy. Tato konfigurace bude definovat, jak velké úložiště se použije pro zálohy zleva doprava. Cenová Kalkulačka zleva doprava. můžete použít k zjištění přibližné hodnoty náklady na úložiště zleva doprava. Další informace najdete v tématu [Dlouhodobé uchovávání](sql-database-long-term-retention.md).

## <a name="azure-hybrid-use-benefit"></a>Azure Hybrid Use Benefit

V na základě vCore nákupu modelu (preview), můžete exchange stávající licence pro zvýhodněné sazby na databázi SQL pomocí [výhody použití hybridní Azure pro systém SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md). Této výhody Azure umožňuje používat místní licencí systému SQL Server uložit až 30 % na Azure SQL Database pomocí programu Software Assurance místní licencí systému SQL Server.

![Ceny](./media/sql-database-service-tiers/pricing.png)

## <a name="migration-of-single-databases-with-geo-replication-links"></a>Migrace jednotlivých databází s odkazy geografická replikace

Migrace z na základě DTU modelu na základě vCore modelu je podobná upgrade nebo přechod na starší verzi geografická replikace vztahy mezi databázemi Standard a Premium. Nevyžaduje se, že se ukončuje geografická replikace, ale uživatel musí odpovídat pravidlům sekvencování. Při upgradu, musíte napřed upgradovat sekundární databázi a pak upgradovat primární. Když přechod na starší verzi, změnit pořadí: je nutné nejprve starší verzi primární databázi a poté downgradovat sekundární. 

Pokud používáte geografická replikace mezi dvěma elastické fondy, důrazně doporučujeme vyhradit jeden fond jako primární a druhá – jako sekundární. Migrace elastické fondy by měl v takovém případě použijte stejné pokyny.  Je však že technicky je možné, že fondu elastické databáze obsahuje primární i sekundární databáze. V takovém případě správně můžete migrovat by měl považují fond s vyšší využití jako "primární" a postupujte podle pravidla sekvencování odpovídajícím způsobem.  

Následující tabulka obsahuje pokyny pro migraci konkrétních scénářů: 

|Aktuální úroveň služby|Cílové úrovně služby|Typ migrace|Akce uživatele|
|---|---|---|---|
|Standard|Obecné účely|Laterální|Můžete migrovat v libovolném pořadí, ale potřeba zajistit odpovídající vCore nastavení velikosti *|
|Premium|Pro důležité obchodní informace|Laterální|Můžete migrovat v libovolném pořadí, ale potřeba zajistit odpovídající vCore nastavení velikosti *|
|Standard|Pro důležité obchodní informace|Upgrade|Musíte migrovat sekundární nejprve|
|Pro důležité obchodní informace|Standard|Downgradovat|Musíte migrovat nejprve primární|
|Premium|Obecné účely|Downgradovat|Musíte migrovat nejprve primární|
|Obecné účely|Premium|Upgrade|Musíte migrovat sekundární nejprve|
|Pro důležité obchodní informace|Obecné účely|Downgradovat|Musíte migrovat nejprve primární|
|Obecné účely|Pro důležité obchodní informace|Upgrade|Musíte migrovat sekundární nejprve|
||||

\* Každý 100 DTU ve standardní vrstvě vyžaduje minimálně 1 vCore a každý 125 DTU v úrovni Premium vyžaduje minimálně 1 vCore

## <a name="migration-of-failover-groups"></a>Migrace skupin převzetí služeb při selhání 

Migrace skupin převzetí služeb při selhání s více databází vyžaduje jednotlivých migrace primárních a sekundárních databází. Během tohoto procesu stejné aspekty a sekvencování pravidla použít. Po databáze jsou převedeny na modelu na základě vCore, převzetí služeb při selhání skupiny zůstane v platnosti se stejným nastavením zásad. 

## <a name="creation-of-a-geo-replication-secondary"></a>Vytvoření sekundární geografická replikace

Můžete vytvořit pouze sekundární geograficky pomocí stejné vrstvě služby jako primární. Pro databázi s míru generování vysoké protokolu se důrazně doporučuje, aby sekundární je vytvořen se stejnou úrovní výkonu jako primární. Pokud vytváříte geo sekundární v elastický fond pro jednu primární databázi, se důrazně doporučuje, aby ve fondu není `maxVCore` nastavení, která odpovídá na úroveň výkonu primární databáze. Pokud vytváříte geo sekundární v elastický fond pro primární v jiném elastického fondu, se důrazně doporučuje, že fondy mají stejné `maxVCore` nastavení

## <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Kopie databáze pomocí převést databázi na základě jednotek DTU na databázi na základě vCore.

Všechny databáze s úrovní výkonu na základě DTU můžete zkopírovat do databáze s výkonu na základě vCore úrovni bez omezení nebo speciální pořadí úloh také podporuje úrovně výkonu cílové databáze maximální velikost zdrojové databáze. Je to proto, že kopírování databáze vytvoří snímek dat od počáteční čas operace kopírování a nebude provádět synchronizaci dat mezi zdrojem a cílem. 

## <a name="next-steps"></a>Další postup

- Podrobnosti o konkrétní úrovně výkonu a možnosti velikosti úložiště k dispozici v tématu [limitů prostředků na základě DTU databáze SQL](sql-database-dtu-resource-limits.md) a [limitů prostředků na základě vCore SQL Database](sql-database-vcore-resource-limits.md).
- V tématu [nejčastější dotazy k databázi SQL](sql-database-faq.md) odpovědi na nejčastější dotazy.
- Další informace o [předplatného Azure a omezení služby, kvóty a omezení](../azure-subscription-service-limits.md)
