---
title: Služba Azure SQL Database – vCore | Dokumentace Microsoftu
description: Nákupní model založený na virtuálních jádrech umožňuje nezávisle na sobě škálovat výpočetní prostředky a prostředky úložiště, odpovídají zajištění místního výkonu a optimalizovat cena.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: sashan, moslake
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 48180d4a9dcf7219815b5bc397647d7d97467cf7
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55961784"
---
# <a name="vcore-service-tiers-azure-hybrid-benefit-and-migration"></a>vCore úrovně služeb, zvýhodněné hybridní využití Azure a migrace

Nákupní model založený na virtuálních jádrech umožňuje nezávisle na sobě škálovat výpočetní prostředky a prostředky úložiště, odpovídají zajištění místního výkonu a optimalizovat cena. Umožňuje také můžete vybrat generaci:

- Gen4 - až 24 logických procesorů podle Intel E5-2673 v3 (Haswell) 2,4 GHz, vCore = 1 PP (fyzických jader), 7 GB na jádro, připojené SSD
- Gen5 - až 80 logických procesorů podle Intel E5-2673 v4 (Broadwell) 2,3 GHz procesorech, vCore = 1 LP (hyper vlákno), 5.1 GB na jádro, rychlé eNVM SSD

Gen4 hardware nabízí podstatně více paměti na vCore. Gen5 hardware však umožňuje vertikálně navýšit kapacitu výpočetních prostředků mnohem vyšší.

> [!NOTE]
> Informace o úrovních služeb na základě DTU najdete v tématu [založený na DTU úrovně](sql-database-service-tiers-dtu.md). Informace o odlišení těchto služeb na základě jednotek DTU úrovně a úrovní služeb založený na virtuálních jádrech najdete v tématu [modely nákupu Azure SQL Database](sql-database-service-tiers.md).

## <a name="service-tier-characteristics"></a>Vlastnosti úrovně služeb

Modelu virt. jader nabízí tři úrovně služeb pro obecné účely, hyperškálovatelný systém a důležité obchodní informace. Úrovně služby jsou rozlišené pomocí rozsah velikostí výpočetních, vysoká dostupnost, izolaci chyb, typy a velikosti úložiště a vstupně-výstupních operací. Je nutné nakonfigurovat samostatně požadované období úložiště a uchovávání dat pro zálohování. Na webu Azure Portal, přejděte na Server (ne databáze) > Spravovat zálohy > Konfigurace zásad > v době obnovení konfiguraci bodu > 7 – 35 dní.

Následující tabulka vám pomůže pochopit rozdíly mezi třech úrovních:

||**Obecné účely**|**Pro důležité obchodní informace**|**Velkokapacitní (preview)**|
|---|---|---|---|
|Nejvhodnější pro|Většinu obchodních úloh. Nabídky rozpočtu orientovaný vybalancovaných a škálovatelných výpočetních možností a možností ukládání.|Podnikové aplikace s vysokými nároky na V/V. Nabízí nejvyšší odolnost proti selhání s využitím několika izolovaných replik.|Většina podnikových úloh pomocí vysoce škálovatelného úložiště a požadavky na škálování pro čtení|
|Compute|Gen4: vCore 1 až 24<br/>Gen5: vCore 1 až 80|Gen4: vCore 1 až 24<br/>Gen5: vCore 1 až 80|Gen4: vCore 1 až 24<br/>Gen5: vCore 1 až 80|
|Memory (Paměť)|Gen4: 7 GB na jádro<br>Gen5: 5.1 GB na jádro | Gen4: 7 GB na jádro<br>Gen5: 5.1 GB na jádro |Gen4: 7 GB na jádro<br>Gen5: 5.1 GB na jádro|
|Storage|Používá [vzdálené úložiště úrovně premium](../virtual-machines/windows/premium-storage.md):<br/>Izolované databáze: 5 GB – 4 TB<br/>Spravovanou instanci: 32 GB - 8 TB |Používá místní úložiště SSD:<br/>Izolované databáze: 5 GB – 4 TB<br/>Spravovanou instanci: 32 GB - 4 TB |Flexibilní a zvětšování úložiště podle potřeby. Podporuje až 100 TB úložiště a další. Místní úložiště SSD pro mezipaměť fondu místní vyrovnávací paměti a místní datové úložiště. Jako konečné dlouhodobé úložiště dat Azure vzdálené úložiště. |
|Vstupně-výstupní propustnost (přibližné)|Izolované databáze: 500 IOPS na vCore s 7000 maximální IOPS</br>Spravovanou instanci: Závisí na [velikost souboru](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes)|5000 IOPS na jádro s 200 000 maximální IOPS|Bude doplněno|
|Dostupnost|1 repliky, žádné škálování pro čtení|3 repliky, 1 [repliky pro čtení škálování](sql-database-read-scale-out.md),<br/>Zóna redundantní HA|?|
|Zálohování|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 – 35 dní (7 dní ve výchozím nastavení)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 – 35 dní (7 dní ve výchozím nastavení)|zálohování na snímku do vzdáleného úložiště Azure a obnovení použijte tyto snímky pro rychlé obnovení. Zálohy jsou okamžité a nemají vliv vstupně-výstupním výkonem výpočetního výkonu. Obnovení jsou velmi rychlé a nejsou velikost operace dat (s ohledem minut, nikoli hodin nebo dnů).|
|V paměti|Nepodporuje se|Podporováno|Nepodporuje se|
|||

> [!NOTE]
> Můžete získat bezplatné databázi Azure SQL na úrovni služby Basic ve spojení s bezplatným účtem Azure prozkoumat Azure. Informace najdete v tématu [vytvořte si spravovanou cloudovou databázi s bezplatným účtem Azure](https://azure.microsoft.com/free/services/sql-database/).

- Další informace najdete v tématu [vCore omezení prostředků v jedné databázi](sql-database-vcore-resource-limits-single-databases.md) a [vCore omezení prostředků ve spravované instanci](sql-database-managed-instance.md#vcore-based-purchasing-model).
- Další informace o úrovních služeb pro obecné účely a pro důležité obchodní informace najdete v tématu [úrovně služeb pro obecné účely a pro důležité obchodní informace](sql-database-service-tiers-general-purpose-business-critical.md).
- Podrobnosti na úrovni služby Hyperškálovatelného v nákupní model založený na virtuálních jádrech najdete v tématu [úroveň služby Hyperškálovatelného](sql-database-service-tier-hyperscale.md).  

> [!IMPORTANT]
> Pokud budete potřebovat méně než jedno virtuální jádro výpočetní kapacity, použijte nákupní model založený na DTU.

## <a name="azure-hybrid-benefit"></a>Zvýhodněné hybridní využití Azure

V nákupní model založený na virtuálních jádrech mohou vyměňovat své současné licence pro zlevněné sazby pro SQL Database pomocí [zvýhodněné hybridní využití Azure pro SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Tato výhoda Azure umožňuje využít vaše licence v místním SQL serveru a Šetřete na Azure SQL Database až 30 % používáte místní licence SQL serveru se Software Assurance.

![Ceny](./media/sql-database-service-tiers/pricing.png)

S programem Azure Hybrid Benefit můžete platit jenom za základní infrastrukturu Azure pomocí stávající licence SQL Server pro databázový stroj SQL samotný (**BasePrice**) nebo platby za základní infrastrukturu a licenci systému SQL Server (**LicenseIncluded**). Můžete vybrat nebo změnit váš licenční model pomocí webu Azure portal nebo pomocí jedné z následujících rozhraní API.

- Nastavení nebo aktualizaci typu licence pomocí Powershellu:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase):
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql)

- Nastavení nebo aktualizaci typu licence pomocí Azure CLI:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [Vytvoření az sql mi](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [aktualizace mi az sql](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- Nastavení nebo aktualizaci typu licence pomocí rozhraní REST API:

  - [Databáze – vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Databáze – aktualizace](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Spravované instance - vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Spravovaná instance – aktualizace](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migration-from-dtu-model-to-vcore-model"></a>Migrace z modelu DTU do modelu virt. jader

### <a name="migration-of-a-database"></a>Migrace databáze

Migrace databáze z nákupní model založený na DTU pro nákupní model založený na virtuálních jádrech je podobný upgrade a Downgrade mezi databáze Standard a Premium v nákupní model založený na DTU.

### <a name="migration-of-databases-with-geo-replication-links"></a>Migrace databází s vazby geografické replikace

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

Můžete vytvořit pouze typu geo-secondary pomocí stejné úrovně služeb jako primární. Pro databázi s protokolu vysokou míru generování doporučujeme, že se vytvoří sekundární se stejnou velikostí výpočetních jako primární. Při vytváření typu geo-secondary v elastickém fondu pro jednu primární databázi, doporučuje se, že má fond `maxVCore` nastavení, která odpovídá výpočetní velikost primární databáze. Pokud vytváříte typu geo-secondary v elastickém fondu pro primární do druhého elastického fondu, se doporučuje že fondy mají stejné `maxVCore` nastavení

### <a name="using-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Pomocí kopie databáze převést databázi založený na DTU na databázi založený na virtuálních jádrech

Můžete zkopírovat libovolnou databázi s velikostí výpočetními prostředky na základě jednotek DTU na databázi s velikostí výpočetních založený na virtuálních jádrech bez omezení nebo speciální pořadí úloh, tak dlouho, dokud výpočty velikost cíle podporuje maximální velikost zdrojové databáze. Kopírování databáze vytvoří snímek dat od počáteční čas operace kopírování a neprovede synchronizace dat mezi zdrojem a cílem.

## <a name="next-steps"></a>Další postup

- Podrobnosti o konkrétní výpočetní prostředky, velikosti a možnosti velikosti úložiště dostupné pro izolované databáze, najdete v části [limity prostředků založený na virtuálních jádrech SQL Database pro izolované databáze](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)
- Podrobnosti o konkrétní výpočetní prostředky, velikosti a možnosti velikosti úložiště dostupné pro elastické fondy, najdete v části [limity pro elastické fondy SQL Database založené na virtuálních jádrech prostředků](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
