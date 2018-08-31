---
title: Služba Azure SQL Database – vCore | Dokumentace Microsoftu
description: Nákupní model založený na virtuálních jádrech umožňuje nezávisle na sobě škálovat výpočetní prostředky a prostředky úložiště, odpovídají zajištění místního výkonu a optimalizovat cena.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/30/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 8266d9e3530969154ac9c8c877badda9f8b4fed3
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307268"
---
# <a name="choosing-a-vcore-service-tier-compute-memory-storage-and-io-resources"></a>Výběr úrovně služeb vCore, výpočty, paměť, úložiště a vstupně-VÝSTUPNÍCH prostředků

Nákupní model založený na virtuálních jádrech umožňuje nezávisle na sobě škálovat výpočetní prostředky a prostředky úložiště, odpovídají zajištění místního výkonu a optimalizovat cena. Umožňuje také můžete vybrat generaci:
- Až 24 logické procesory generace 4 - podle Intel E5-2673 v3 (Haswell) 2,4 GHz, vCore = 1 PP (fyzických jader), 7 GB na jádro, připojené SSD
- Až 80 logické procesory generace 5 - podle Intel E5-2673 v4 (Broadwell) 2,3 GHz procesorech, vCore = 1 LP (hyper vlákno), 5.1. GB na jádro, rychlé eNVM SSD

modelu virt. jader také umožňuje používat [zvýhodněné hybridní využití Azure pro SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md) získat úspory nákladů.

## <a name="service-tier-characteristics"></a>Vlastnosti úrovně služeb

Modelu virt. jader nabízí dvě úrovně služeb pro obecné účely a pro důležité obchodní informace. Úrovně služby jsou rozlišené pomocí rozsah úrovně výkonu, vysoká dostupnost, izolaci chyb, typy úložiště a vstupně-výstupních operací. Zákazník musí zvlášť nakonfigurovat požadované období úložiště a uchovávání dat pro zálohování.

Následující tabulka vám pomůže pochopit rozdíly mezi těmito dvěma vrstvami:

||**Obecné účely**|**Pro důležité obchodní informace**|
|---|---|---|
|Nejvhodnější pro|Většinu obchodních úloh. Nabídky rozpočtu orientovaný vybalancovaných a škálovatelných výpočetních možností a možností ukládání.|Podnikové aplikace s vysokými nároky na V/V. Nabízí nejvyšší odolnost proti selhání s využitím několika izolovaných replik.|
|Compute|Gen4: vCore 1 až 24<br/>Gen5: vCore 1 až 80|Gen4: vCore 1 až 24<br/>Gen5: vCore 1 až 80|
|Memory (Paměť)|Gen4: 7 GB na jádro<br>Gen5: 5.1 GB na jádro | Gen4: 7 GB na jádro<br>Gen5: 5.1 GB na jádro |
|Úložiště|[Vzdálené úložiště úrovně Premium](../virtual-machines/windows/premium-storage.md),<br/>Izolované databáze: 5 GB až 4 TB<br/>Spravovanou instanci: 32 GB až 8 TB |Místní úložiště SSD<br/>Izolované databáze: 5 GB až 1 TB<br/>Spravovanou instanci: 32 GB až 4 TB |
|Vstupně-výstupní propustnost (přibližné)|Izolované databáze: 500 IOPS na vCore s 7000 maximální IOPS</br>Spravovanou instanci: Závisí na [velikost souboru](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)|5000 IOPS na jádro s 200000 maximální IOPS|
|Dostupnost|1 repliky, žádné škálování pro čtení|3 repliky, 1 [repliky pro čtení škálování](sql-database-read-scale-out.md),<br/>Zóna redundantní HA|
|Zálohování|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 – 35 dní (7 dní ve výchozím nastavení)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 – 35 dní (7 dní ve výchozím nastavení)|
|V paměti|neuvedeno|Podporováno|
|||

Další informace najdete v tématu [vCore omezení prostředků v databázi Singelton](sql-database-vcore-resource-limits-single-databases.md) a [vCore omezení prostředků ve spravované instanci](sql-database-managed-instance.md#vcore-based-purchasing-model). 

> [!IMPORTANT]
> Pokud budete potřebovat méně než jedno virtuální jádro výpočetní kapacity, použijte nákupní model založený na DTU.

Zobrazit [nejčastější dotazy k SQL Database](sql-database-faq.md) odpovědi na nejčastější dotazy. 

## <a name="storage-considerations"></a>Aspekty úložišť

Zvažte použití těchto zdrojů:
- Přidělené úložiště využívané datové soubory (MDF) a soubory protokolu souborů (LDF).
- Každá úroveň výkonu izolované databáze podporuje maximální velikost databáze, s výchozí maximální velikost 32 GB.
- Když konfigurujete velikost požadované izolovanou databázi (velikost MDF), 30 % dalšího úložiště, je automaticky přidaná kvůli podpoře LDF
- Velikost úložiště ve spravované instanci musí být určena v násobcích po 32 GB.
- Můžete vybrat libovolné velikosti databáze typu singleton mezi 10 GB a podporované maximum
 - Pro úložiště úrovně Standard zvětšit nebo zmenšit velikost v přírůstcích po 10 GB
 - Pro Premium storage zvětšit nebo zmenšit velikost v přírůstcích po 250 GB
- V úrovni General Purpose služby `tempdb` používá připojené SSD a náklady na toto úložiště je zahrnutá v ceně vCore.
- V rámci úrovně služeb pro důležité obchodní informace `tempdb` sdílené složky připojené SSD se soubory MDF a LDF a náklady na úložiště databáze tempDB je zahrnutá v ceně vCore.

> [!IMPORTANT]
> Bude vám účtována celkový úložiště přidělené MDF a LDF.

Chcete-li monitorovat aktuální celková velikost MDF a LDF, použijte [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Chcete-li monitorovat aktuální velikost jednotlivých souborů MDF a LDF, použijte [katalogu sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Zálohování a úložiště

Přidělení úložiště pro zálohování databáze podporují bod v webu času obnovení (PITR) a [dlouhá období uchovávání dat (LTR)](sql-database-long-term-retention.md) schopnosti služby SQL Database. Toto úložiště je přidělena odděleně pro každou databázi a účtovat jako dvě samostatné na databázi poplatky. 

- **PITR**: zálohování jednotlivých databází se zkopírují do [úložiště RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md) se automaticky. Velikost úložiště zvyšuje dynamicky vytvářené nových záloh.  Úložiště využívané týdenními úplnými zálohami, denními rozdílovými zálohami a zálohami protokolů transakcí se kopíruje každých 5 minut. Spotřeba úložiště závisí na četnosti změn databáze a dobu uchování. Můžete nakonfigurovat samostatný uchovávají pro každou databázi mezi 7 až 35 dnů. Minimální objem úložiště rovna 1 x velikosti dat je k dispozici bez dalších poplatků. Pro většinu databází je tato částka dostatečná k uložení zálohy za 7 dní.
- **LTR**: SQL Database nabízí možnost konfigurace dlouhodobého uchovávání úplné zálohy po dobu až 10 let. Pokud je povolené zásady LTR, tyto zálohy se ukládají do úložiště RA-GRS automaticky, ale můžete řídit, jak často se zkopírují zálohy. Aby splnila požadavek na dodržení jiný, můžete vybrat různých období uchovávání záloh týdenní, měsíční nebo roční. Tato konfigurace bude definovat, jak velké úložiště se použije k zálohování LTR. Zleva doprava cenové kalkulačky můžete odhadnout náklady na úložiště zleva doprava. Další informace najdete v tématu [Dlouhodobé uchovávání](sql-database-long-term-retention.md).

## <a name="azure-hybrid-use-benefit"></a>Azure Hybrid Use Benefit

V nákupní model založený na virtuálních jádrech mohou vyměňovat své současné licence pro zlevněné sazby pro SQL Database pomocí [zvýhodněné hybridní využití Azure pro SQL Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md). Tato výhoda Azure umožňuje využít vaše licence v místním SQL serveru a Šetřete na Azure SQL Database až 30 % používáte místní licence SQL serveru se Software Assurance.

![Ceny](./media/sql-database-service-tiers/pricing.png)

## <a name="migration-from-dtu-model-to-vcore-model"></a>Migrace z modelu DTU do modelu virt. jader

### <a name="migration-of-single-databases-with-geo-replication-links"></a>Migrace jednotlivých databází s vazby geografické replikace

Migrace z modelu založeného na DTU na model založený na virtuálních jádrech je podobný upgradu nebo downgradu vztahy geografické replikace mezi databáze Standard a Premium. Nevyžaduje se, že se ukončuje geografickou replikaci, ale uživatel musí dodržovat pravidla klasifikace. Při upgradu, musíte nejdřív upgradovat sekundární databáze a pak upgradovat primární. Při downgradu, pořadí: musí nejprve provést downgrade primární databáze a poté downgradovat sekundární. 

Při použití geografické replikace mezi dva elastické fondy, doporučuje se určit jeden fond jako primární a druhá – jako sekundární. V takovém případě by měl migraci elastických fondů pomocí stejné pokyny.  Je však že je technicky možné, že elastický fond obsahuje primární i sekundární databáze. V takovém případě správně migraci vám by měl zpracovávat fond s vyšší využití jako "primární" a postupujte podle pravidla klasifikace odpovídajícím způsobem.  

Následující tabulka uvádí pokyny k migraci konkrétních scénářů: 

|Aktuální úroveň služby|Cílové úrovni služeb|Typ migrace|Akce uživatele|
|---|---|---|---|
|Standard|Obecné účely|Laterální|Můžete migrovat v libovolném pořadí, ale potřeba zajistit odpovídající vCore velikosti *|
|Premium|Pro důležité obchodní informace|Laterální|Můžete migrovat v libovolném pořadí, ale potřeba zajistit odpovídající vCore velikosti *|
|Standard|Pro důležité obchodní informace|Upgrade|Musíte migrovat nejprve sekundární|
|Pro důležité obchodní informace|Standard|Downgradovat|Musíte migrovat nejdříve primární|
|Premium|Obecné účely|Downgradovat|Musíte migrovat nejdříve primární|
|Obecné účely|Premium|Upgrade|Musíte migrovat nejprve sekundární|
|Pro důležité obchodní informace|Obecné účely|Downgradovat|Musíte migrovat nejdříve primární|
|Obecné účely|Pro důležité obchodní informace|Upgrade|Musíte migrovat nejprve sekundární|
||||

\* Každý 100 DTU ve standardní úrovni vyžaduje alespoň 1 virtuální jádro a každý 125 DTU na úrovni Premium vyžaduje alespoň 1 virtuální jádro

### <a name="migration-of-failover-groups"></a>Migrace skupin převzetí služeb při selhání 

Migrace skupin převzetí služeb při selhání s více databázemi vyžaduje jednotlivých migraci primární a sekundární databází. Během tohoto procesu použít stejné aspekty a pravidel klasifikace. Databáze po převodu na model založený na virtuálních jádrech, skupiny převzetí služeb při selhání zůstávají v platnosti se stejnými nastaveními zásad. 

### <a name="creation-of-a-geo-replication-secondary"></a>Vytvoření sekundární geografické replikace

Můžete vytvořit pouze typu geo-secondary pomocí stejné úrovně služeb jako primární. Pro databázi s protokolu vysokou míru generování doporučujeme, že se vytvoří sekundární se stejnou úrovní výkonu jako primární. Při vytváření typu geo-secondary v elastickém fondu pro jednu primární databázi, doporučuje se, že má fond `maxVCore` nastavení, která odpovídá primární databázi úrovně výkonu. Pokud vytváříte typu geo-secondary v elastickém fondu pro primární do druhého elastického fondu, se doporučuje že fondy mají stejné `maxVCore` nastavení

### <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Převést databázi založený na DTU na databázi založený na virtuálních jádrech, pomocí kopie databáze.

Všechny databáze s úrovní výkonu založený na DTU můžete zkopírovat do databáze s založený na virtuálních jádrech výkonu úrovně bez omezení nebo speciální, pořadí úloh jako cílovou úrovní výkonu podporuje maximální velikost zdrojové databáze. Je to proto, že kopie databáze vytvoří snímek dat od počáteční čas operace kopírování a neprovede synchronizace dat mezi zdrojem a cílem. 

## <a name="next-steps"></a>Další postup

- Podrobnosti o konkrétní úrovně výkonu a možnosti velikosti úložiště dostupné pro izolované databáze, najdete v části [limity prostředků založený na virtuálních jádrech SQL Database pro izolované databáze](sql-database-vcore-resource-limits-single-databases.md#single-database-storage-sizes-and-performance-levels)
- Podrobnosti o konkrétní úrovně výkonu a úložiště možnosti velikosti k dispozici pro elastické fondy najdete v části [limity pro elastické fondy SQL Database založené na virtuálních jádrech prostředků](sql-database-vcore-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels).
