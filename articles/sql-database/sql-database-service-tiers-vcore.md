---
title: Služba Azure SQL Database – vCore | Dokumentace Microsoftu
description: Nákupní model založený na virtuálních jádrech umožňuje nezávisle na sobě škálovat výpočetní prostředky a prostředky úložiště, odpovídají zajištění místního výkonu a optimalizovat cena.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
manager: craigg
ms.date: 05/06/2019
ms.openlocfilehash: 520dba611e6791fca990e21173424a914c3d8e14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66693347"
---
# <a name="choose-among-the-vcore-service-tiers-and-migrate-from-the-dtu-service-tiers"></a>Zvolte mezi úrovněmi služeb vCore a migrovat z jednotek DTU úrovně služeb

Virtuální jádro (vCore) – na základě nákupní model umožňuje nezávisle na sobě škálovat výpočetní prostředky a prostředky úložiště, odpovídají zajištění místního výkonu a optimalizovat cena. Můžete ho taky zvolit generace hardwaru:

- **Gen4**: Až 24 logických procesorů podle Intel E5-2673 v3 (Haswell) 2,4 GHz procesorech vCore = 1 PP (fyzických jader), 7 GB na jádro, připojené SSD
- **Gen5**: Až 80 logických procesorů podle Intel E5-2673 v4 (Broadwell) 2.3 GHz procesorech vCore = 1 LP (hyper vlákno), 5.1 GB na jádro, rychlé eNVM SSD

Gen4 hardware nabízí podstatně více paměti na vCore. Gen5 hardware však umožňuje vertikálně navýšit kapacitu výpočetních prostředků mnohem vyšší.

> [!NOTE]
> Informace o úrovních služeb na základě DTU najdete v tématu [pro nákupní model založený na DTU úrovně služeb](sql-database-service-tiers-dtu.md). Informace o rozdílech mezi úrovněmi služeb pro založený na DTU a modely nákupu založený na virtuálních jádrech najdete v tématu [modely nákupu Azure SQL Database](sql-database-purchase-models.md).

## <a name="service-tier-characteristics"></a>Vlastnosti vrstvy služeb

Nákupní model založený na virtuálních jádrech nabízí tři úrovně služby: obecné účely, hyperškálovatelný systém a důležité obchodní informace. Tyto úrovně jsou rozlišené pomocí celou řadu velikostí výpočetních, návrhy vysokou dostupnost, izolaci chyb metody, typy a velikosti úložiště a vstupně-výstupních operací rozsahy.

Je nutné nakonfigurovat samostatně požadované období úložiště a uchovávání dat pro zálohování. K nastavení doby uchování zálohy, otevřete na webu Azure portal, přejděte na server (ne databáze) a potom přejděte ke **spravovat zálohy** > **konfigurace zásad**  >   **Bod v čase obnovení konfigurace** > **7 – 35 dnů**.

Následující tabulka vysvětluje rozdíly mezi třech úrovních:

||**Obecné účely**|**Pro důležité obchodní informace**|**Velkokapacitní**|
|---|---|---|---|
|Nejvhodnější pro|Většinu obchodních úloh. Nabídky levným provozem, vybalancované a škálovatelné výpočetní možnosti a možnosti úložiště.|Obchodní aplikace s vysokými požadavky na vstupně-výstupních operací. Nabízí nejvyšší odolnost proti selhání s využitím několika izolovaných replik.|Většina podnikových úloh pomocí vysoce škálovatelného úložiště a požadavky na škálování pro čtení.|
|Compute|**Zřízení výpočetních**:<br/>Gen4: 1 až 24 virtuálních jader<br/>Gen5: 2 až 80 virtuálních jader<br/>**Výpočetní prostředí**:<br/>Gen5: 0,5 – 4 virtuální jádra|**Zřízení výpočetních**:<br/>Gen4: 1 až 24 virtuálních jader<br/>Gen5: 2 až 80 virtuálních jader|**Zřízení výpočetních**:<br/>Gen4: 1 až 24 virtuálních jader<br/>Gen5: 2 až 80 virtuálních jader|
|Memory (Paměť)|**Zřízení výpočetních**:<br/>Gen4: 7 GB na vCore<br/>Gen5: 5.1 GB na vCore<br/>**Výpočetní prostředí**:<br/>Gen5: 3 GB na vCore|**Zřízení výpočetních**:<br/>Gen4: 7 GB na vCore<br/>Gen5: 5.1 GB na vCore |**Zřízení výpočetních**:<br/>Gen4: 7 GB na vCore<br/>Gen5: 5.1 GB na vCore|
|Úložiště|Používá vzdálené úložiště.<br/>**Izolované databáze zřízení výpočetních**:<br/>5 GB – 4 TB<br/>**Výpočetní prostředí izolované databáze**:<br/>5 GB - 1 TB<br/>**Spravovaná instance**: 32 GB - 8 TB |Používá místní úložiště SSD.<br/>**Izolované databáze zřízení výpočetních**:<br/>5 GB – 4 TB<br/>**Spravovaná instance**:<br/>32 GB - 4 TB |Flexibilní zvětšování úložiště podle potřeby. Podporuje až 100 TB úložiště. Pro fond vyrovnávacích pamětí v místní mezipaměti a místní úložiště dat používá místní úložiště SSD. Jako konečné dlouhodobé úložiště dat používá vzdálené úložiště Azure. |
|Vstupně-výstupní propustnost (přibližné)|**Izolované databáze**: 500 IOPS na vCore s 7000 maximální vstupně-výstupních operací.<br/>**Spravovaná instance**: Závisí na [velikost souboru](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes).|5000 IOPS na jádro s 200 000 maximální IOPS|Velkokapacitní je architektura vícevrstevných s ukládáním do mezipaměti na různých úrovních. Efektivní IOPs, bude záviset na zatížení.|
|Dostupnost|1 repliky, žádnou repliku škálování pro čtení|3 repliky, 1 [repliky pro čtení škálování](sql-database-read-scale-out.md),<br/>zónově redundantní vysoké dostupnosti (HA)|1 repliky pro čtení i zápis a navíc 0-4 [repliky škálování pro čtení](sql-database-read-scale-out.md)|
|Zálohování|[Geograficky redundantní úložiště jen pro čtení (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 – 35 dní (7 dní ve výchozím nastavení)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7 – 35 dní (7 dní ve výchozím nastavení)|Zálohy založené na snímku do vzdáleného úložiště Azure. Tyto snímky obnoví použít pro rychlé obnovení. Zálohy jsou okamžité a nebudou mít vliv na výpočetní výkon vstupně-výstupních operací. Obnovení jsou rychlé a nejsou operace velikost dat (minuty a současně spíše než hodiny nebo dny).|
|V paměti|Nepodporuje se|Podporováno|Nepodporuje se|
|||

> [!NOTE]
> Na úrovni služby basic ve spojení s bezplatným účtem Azure můžete získat bezplatné databázi Azure SQL. Další informace najdete v tématu [vytvořte si spravovanou cloudovou databázi s bezplatným účtem Azure](https://azure.microsoft.com/free/services/sql-database/).

- Další informace o omezení prostředků na vCore, naleznete v tématu [vCore omezení prostředků v jedné databázi](sql-database-vcore-resource-limits-single-databases.md) a [vCore omezení prostředků ve spravované instanci](sql-database-managed-instance.md#vcore-based-purchasing-model).
- Další informace o obecné účely a úrovní služeb kritické obchodní najdete v tématu [úrovně služeb pro obecné účely a pro důležité obchodní informace](sql-database-service-tiers-general-purpose-business-critical.md).
- Další informace o úrovni služeb hyperškálovatelného v nákupní model založený na virtuálních jádrech najdete v tématu [úroveň služby Hyperškálovatelného](sql-database-service-tier-hyperscale.md).  

## <a name="azure-hybrid-benefit"></a>Zvýhodněné hybridní využití Azure

V úrovni zřízených výpočetních nákupní model založený na virtuálních jádrech, mohou vyměňovat své současné licence pro zlevněné sazby za SQL Database s použitím [zvýhodněné hybridní využití Azure pro SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Tato výhoda Azure umožňuje uložit až 30 procent na Azure SQL Database s použitím vašich místních licencí SQL serveru s programem Software Assurance.

![Ceny](./media/sql-database-service-tiers/pricing.png)

S programem Azure Hybrid Benefit můžete platit jenom za základní infrastrukturu Azure s využitím vaší stávající licenci na SQL Server pro databázový stroj SQL samotného (základní výpočetní ceny), nebo můžete platit základní infrastruktury a systému SQL Server licence (ceny s licencí).

Můžete vybrat nebo změnit váš licenční model s využitím webu Azure portal nebo pomocí jedné z následujících rozhraní API:

- Nastavení nebo aktualizaci typu licence pomocí prostředí PowerShell:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- Nastavení nebo aktualizaci typu licence pomocí rozhraní příkazového řádku Azure:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [Vytvoření az sql mi](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [aktualizace mi az sql](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- Nastavení nebo pomocí rozhraní REST API aktualizovat typ licence:

  - [Databáze – vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Databáze – aktualizace](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Spravované instance - vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Spravovaná instance – aktualizace](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migrace z modelu založeného na DTU na model založený na virtuálních jádrech

### <a name="migrate-a-database"></a>Migrace databáze

Migrace databáze z nákupní model založený na DTU pro nákupní model založený na virtuálních jádrech je podobný upgrade a Downgrade mezi úrovní služeb standard a premium v nákupní model založený na DTU.

### <a name="migrate-databases-with-geo-replication-links"></a>Migrace databází s vazby geografické replikace

Migrace z modelu založeného na DTU pro nákupní model založený na virtuálních jádrech je podobný upgradu nebo downgradu vztahy geografické replikace mezi databáze v úrovních služby standard a premium. Během migrace není nutné zastavit geografickou replikaci, ale je třeba dodržovat tato pravidla klasifikace:

- Při upgradu, musíte nejdřív upgradovat sekundární databáze a pak upgradovat primární.
- Při downgradu, pořadí: musí nejprve provést downgrade primární databáze a poté downgradovat sekundární.

Při použití geografické replikace mezi dva elastické fondy, doporučujeme označit jeden fond jako primární a druhý jako sekundární. V takovém případě migrace elastických fondů používejte stejné pokyny k sekvencování. Pokud máte elastických fondů, které obsahují primární i sekundární databáze, fond s vyšší využití považovat za primární a postupujte podle pravidla klasifikace odpovídajícím způsobem.  

Následující tabulka uvádí pokyny k migraci konkrétních scénářů:

|Aktuální úroveň služby|Cílové úrovni služeb|Typ migrace|Akce uživatele|
|---|---|---|---|
|Standard|Obecné účely|Laterální|Můžete migrovat v libovolném pořadí, ale potřeba zajistit odpovídající vCore velikosti *|
|Premium|Pro důležité obchodní informace|Laterální|Můžete migrovat v libovolném pořadí, ale potřeba zajistit odpovídající vCore velikosti *|
|Standard|Pro důležité obchodní informace|Upgrade|Musíte migrovat nejprve sekundární|
|Pro důležité obchodní informace|Standard|Downgrade|Musíte migrovat nejdříve primární|
|Premium|Obecné účely|Downgrade|Musíte migrovat nejdříve primární|
|Obecné účely|Premium|Upgrade|Musíte migrovat nejprve sekundární|
|Pro důležité obchodní informace|Obecné účely|Downgrade|Musíte migrovat nejdříve primární|
|Obecné účely|Pro důležité obchodní informace|Upgrade|Musíte migrovat nejprve sekundární|
||||

\* Každých 100 Dtu na úrovni standard vyžadují aspoň 1 virtuální jádro a každý 125 Dtu na úrovni premium vyžadují aspoň 1 virtuální jádro.

### <a name="migrate-failover-groups"></a>Migrace skupin převzetí služeb při selhání

Migrace skupin převzetí služeb při selhání s více databázemi vyžaduje jednotlivých migraci primární a sekundární databází. Během tohoto procesu použít stejné aspekty a pravidel klasifikace. Po databáze jsou převedeny na nákupní model založený na virtuálních jádrech, skupiny převzetí služeb při selhání zůstávají v platnosti se stejnými nastaveními zásad.

### <a name="create-a-geo-replication-secondary-database"></a>Vytvoření sekundární databáze geografické replikace

Sekundární databáze geografická replikace (typu geo-secondary) můžete vytvořit pouze pomocí stejné úrovně služeb jako u primární databáze. Pro databáze s vysokou generování protokolu rychlostí doporučujeme vytvořit geograficky sekundární se stejnou velikostí výpočetních jako primární.

Pokud vytváříte v elastickém fondu pro jednu primární databázi typu geo-secondary, ujistěte se, že `maxVCore` nastavení fondu odpovídá velikost výpočetních primární databáze. Pokud vytváříte typu geo-secondary pro primární do druhého elastického fondu, doporučujeme, abyste fondy mají stejné `maxVCore` nastavení.

### <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Můžete převést databázi založený na DTU na databázi založený na virtuálních jádrech kopie databáze

Můžete zkopírovat libovolnou databázi s velikostí výpočetními prostředky na základě jednotek DTU na databázi s velikostí výpočetních založený na virtuálních jádrech bez omezení nebo speciální pořadí úloh, tak dlouho, dokud výpočty velikost cíle podporuje maximální velikost zdrojové databáze. Kopírování databáze vytvoří snímek dat od počáteční čas operace kopírování a nebude synchronizovat data mezi zdrojem a cílem.

## <a name="next-steps"></a>Další postup

- Konkrétní výpočetní velikosti a možnosti velikosti úložiště dostupné pro izolované databáze najdete v tématu [limity prostředků založený na virtuálních jádrech SQL Database pro izolované databáze](sql-database-vcore-resource-limits-single-databases.md).
- Konkrétní výpočetní velikosti a možnosti velikosti úložiště dostupné pro elastické fondy najdete v tématu [limity pro elastické fondy SQL Database založené na virtuálních jádrech prostředků](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
