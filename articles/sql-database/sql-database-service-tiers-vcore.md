---
title: Služba Azure SQL Database – vCore | Microsoft Docs
description: Nákupní model založený na vCoreech umožňuje nezávisle škálovat výpočetní prostředky a prostředky úložiště, odpovídat místnímu výkonu a optimalizovat ceny.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 08/29/2019
ms.openlocfilehash: 4af269faab21207e1a754e309cac16e5e0a94b69
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164343"
---
# <a name="choose-among-the-vcore-service-tiers-and-migrate-from-the-dtu-service-tiers"></a>Výběr mezi úrovněmi služby vCore a migrace z úrovní služeb DTU

Nákupní model založený na Virtual Core (vCore) umožňuje nezávisle škálovat výpočetní prostředky a prostředky úložiště, odpovídat místnímu výkonu a optimalizovat ceny. Umožňuje vám také zvolit generaci hardwaru:

- **COMPUTE GEN4 –** : Až 24 logických procesorů založených na procesorech Intel E5-2673 V3 (Haswell) 2,4 GHz, vCore = 1 PP (fyzický jádro), 7 GB na vCore, připojená jednotka SSD
- **Gen5**: Až 80 logických procesorů založených na procesorech Intel E5-2673 v4 (Broadwell) 2,3 GHz, vCore = 1 LP (Hyper-thread), 5,1 GB za vCore pro zřízené výpočetní prostředky a až 24 GB na vCore pro výpočetní prostředky, Fast eNVM SSD

COMPUTE GEN4 – hardware nabízí podstatně větší množství paměti na vCore. Gen5 hardware ale umožňuje škálovat výpočetní prostředky mnohem vyšší.

> [!IMPORTANT]
> Nové databáze COMPUTE GEN4 – již nejsou podporovány v oblastech Austrálie – východ a Brazílie – jih.
> [!NOTE]
> Další informace o úrovních služeb založených na DTU najdete v tématu [úrovně služeb pro nákupní model založený na DTU](sql-database-service-tiers-dtu.md). Informace o rozdílech mezi úrovněmi služeb pro nákupní modely založené na DTU a na vCore naleznete v tématu [Azure SQL Database nákup modelů](sql-database-purchase-models.md).

## <a name="service-tier-characteristics"></a>Vlastnosti vrstvy služeb

Nákupní model založený na vCore poskytuje tři úrovně služeb: obecné účely, škálování a důležité pro podnikání. Tyto úrovně služeb jsou rozlišené škálou výpočetních velikostí, návrhů s vysokou dostupností, metod pro izolaci chyb, typů a velikostí úložiště a vstupně-výstupními rozsahy.

Je nutné samostatně nakonfigurovat požadované úložiště a dobu uchovávání záloh. Pokud chcete nastavit dobu uchování zálohy, otevřete Azure Portal, přejděte na server (ne na databázi) a pak přejděte na **Správa zálohování** > **Konfigurovat bod zásad** > **v čase obnovení konfigurace** > **7 – 35 dní**.

Následující tabulka vysvětluje rozdíly mezi těmito třemi úrovněmi:

||**Obecné účely**|**Důležité pro podnikání**|**Hyperškálovatelný**|
|---|---|---|---|
|Nejlepší pro|Většina obchodních úloh. Nabízí uživatelsky orientované, vyvážené a škálovatelné možnosti výpočtů a úložiště.|Podnikové aplikace s požadavky vysokého vstupu a výstupu. Nabízí nejvyšší odolnost proti chybám pomocí několika izolovaných replik.|Většina obchodních úloh s vysokou škálovatelností úložiště a požadavky na škálování pro čtení.|
|Compute|**Zřízené výpočetní**prostředky:<br/>COMPUTE GEN4 – 1 až 24 virtuální jádra<br/>Gen5 2 až 80 virtuální jádra<br/>**Výpočetní**prostředí bez serveru:<br/>Gen5 0,5 – 16 virtuální jádra|**Zřízené výpočetní**prostředky:<br/>COMPUTE GEN4 – 1 až 24 virtuální jádra<br/>Gen5 2 až 80 virtuální jádra|**Zřízené výpočetní**prostředky:<br/>COMPUTE GEN4 – 1 až 24 virtuální jádra<br/>Gen5 2 až 80 virtuální jádra|
|Memory (Paměť)|**Zřízené výpočetní**prostředky:<br/>COMPUTE GEN4 – 7 GB na vCore<br/>Gen5 5,1 GB na vCore<br/>**Výpočetní**prostředí bez serveru:<br/>Gen5 Až 24 GB na vCore|**Zřízené výpočetní**prostředky:<br/>COMPUTE GEN4 – 7 GB na vCore<br/>Gen5 5,1 GB na vCore |**Zřízené výpočetní**prostředky:<br/>COMPUTE GEN4 – 7 GB na vCore<br/>Gen5 5,1 GB na vCore|
|Storage|Používá vzdálené úložiště.<br/>Izolovaná **databáze a elastický fond zřízený COMPUTE**:<br/>5 GB – 4 TB<br/>**Výpočetní**prostředí bez serveru:<br/>5 GB – 3 TB<br/>**Spravovaná instance**: 32 GB - 8 TB |Používá místní úložiště SSD.<br/>Izolovaná **databáze a elastický fond zřízený COMPUTE**:<br/>5 GB – 4 TB<br/>**Spravovaná instance**:<br/>32 GB - 4 TB |Flexibilní autogrow úložiště podle potřeby. Podporuje až 100 TB úložiště. Používá místní úložiště SSD pro místní mezipaměť fondu vyrovnávací paměti a místní úložiště dat. Používá vzdálené úložiště Azure jako konečné dlouhodobé úložiště dat. |
|Propustnost vstupně-výstupních operací (přibližná)|Izolovaná **databáze a elastický fond**: 500 vstupně-výstupních operací na vCore až 40000 maximálního počtu IOPS.<br/>**Spravovaná instance**: Závisí na [velikosti souboru](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes).|5000 IOPS za jádro až 200 000 maximální IOPS|Škálovatelná architektura je Vícevrstvá architektura s ukládáním do mezipaměti na více úrovních. Platnost IOPs bude záviset na zatížení.|
|Dostupnost|1 replika, žádné repliky na úrovni čtení|3 repliky, 1 [replika pro čtení a škálování](sql-database-read-scale-out.md)<br/>zóna – redundantní vysoká dostupnost (HA)|1 replika pro čtení i zápis a 0-4 replik v režimu [čtení a škálování](sql-database-read-scale-out.md)|
|Zálohování|[Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dní (ve výchozím nastavení 7 dnů)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dní (ve výchozím nastavení 7 dnů)|Zálohování na základě snímků ve vzdáleném úložišti Azure. Obnoví použití těchto snímků pro rychlé obnovení. Zálohy jsou okamžité a neovlivňují výkon vstupně-výstupních operací ve výpočetním prostředí. Obnovení je rychlé a nejedná se o datovou operaci (trvá to jen v minutách).|
|V paměti|Nepodporuje se|Podporováno|Nepodporuje se|
|||

> [!NOTE]
> V kombinaci s bezplatným účtem Azure můžete získat bezplatnou službu Azure SQL Database na úrovni služeb Basic. Další informace najdete v tématu [Vytvoření spravované cloudové databáze pomocí bezplatného účtu Azure](https://azure.microsoft.com/free/services/sql-database/).

- Další informace o omezeních prostředků vCore najdete v tématu omezení [prostředků Vcore v jedné databázi](sql-database-vcore-resource-limits-single-databases.md) a v [omezeních prostředků Vcore ve spravované instanci](sql-database-managed-instance.md#vcore-based-purchasing-model).
- Další informace o úrovních služeb pro obecné účely a pro důležité obchodní informace najdete v tématu [úrovně služeb pro obecné účely a pro důležité obchodní](sql-database-service-tiers-general-purpose-business-critical.md)informace.
- Další informace o vrstvě služby vCore v nákupním modelu založeném na najdete v tématu [úroveň služby pro škálování](sql-database-service-tier-hyperscale.md)na více úrovních.  

## <a name="azure-hybrid-benefit"></a>Zvýhodněné hybridní využití Azure

V zřízené výpočetní úrovni v rámci nákupního modelu založeném na vCore můžete vyměňovat stávající licence pro zvýhodněné sazby SQL Database pomocí [zvýhodněné hybridní využití Azure pro SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Tato výhoda Azure vám umožní ušetřit až 30 procent na Azure SQL Database pomocí místních licencí SQL Server se Software Assurance.

![Ceny](./media/sql-database-service-tiers/pricing.png)

Pomocí Zvýhodněné hybridní využití Azure se můžete rozhodnout platit jenom za základní infrastrukturu Azure pomocí vaší stávající licence SQL Server pro samotný stroj SQL Database (základní výpočetní ceny), nebo můžete platit jak z základní infrastruktury, tak i SQL Server licence (ceny včetně licencí).

Licenční model můžete vybrat nebo změnit pomocí Azure Portal nebo pomocí některého z následujících rozhraní API:

- Chcete-li nastavit nebo aktualizovat typ licence pomocí prostředí PowerShell:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- Nastavení nebo aktualizace typu licence pomocí Azure CLI:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [AZ SQL mi Create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [AZ SQL mi Update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- Chcete-li nastavit nebo aktualizovat typ licence pomocí REST API:

  - [Databáze – vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Databáze – aktualizace](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Spravované instance – vytvořit nebo aktualizovat](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Spravované instance – aktualizace](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migrace z modelu založeného na DTU na model založený na vCore

### <a name="migrate-a-database"></a>Migrace databáze

Migrace databáze z nákupního modelu založeného na DTU do nákupního modelu založeného na vCore se podobá upgradu nebo downgrade mezi úrovněmi služeb Standard a Premium v nákupním modelu založeném na DTU.

### <a name="migrate-databases-with-geo-replication-links"></a>Migrace databází pomocí odkazů geografické replikace

Migrace z modelu založeného na DTU do nákupního modelu založeného na vCore se podobá upgradu nebo downgrade vztahů geografické replikace mezi databázemi v úrovních služeb Standard a Premium. Během migrace nemusíte zastavit geografickou replikaci, ale musíte dodržovat tato pravidla sekvencování:

- Při upgradu musíte nejdřív upgradovat sekundární databázi a potom upgradovat primární.
- Když se downgrade, obrátí se pořadí: Nejdřív musíte nejprve downgradovat primární databázi a pak snížit její sekundární verzi.

Pokud používáte geografickou replikaci mezi dvěma elastickými fondy, doporučujeme, abyste jeden fond určili jako primární a druhý jako sekundární. V takovém případě byste měli při migraci elastických fondů použít stejné pokyny k sekvencování. Pokud ale máte elastické fondy, které obsahují jak primární, tak sekundární databáze, považovat fond s vyšším využitím jako primární a podle toho pravidla sekvencování řídí.  

Následující tabulka poskytuje pokyny pro konkrétní scénáře migrace:

|Aktuální úroveň služby|Cílová úroveň služby|Typ migrace|Akce uživatele|
|---|---|---|---|
|Standard|Pro obecné účely|Boku|Může migrovat v libovolném pořadí, ale musí zajistit odpovídající velikost vCore *|
|Premium|Pro důležité obchodní informace|Boku|Může migrovat v libovolném pořadí, ale musí zajistit odpovídající velikost vCore *|
|Standard|Pro důležité obchodní informace|Upgrade|Nejprve je třeba migrovat sekundární|
|Pro důležité obchodní informace|Standard|Downgradovat|Nejprve je třeba migrovat primární|
|Premium|Pro obecné účely|Downgradovat|Nejprve je třeba migrovat primární|
|Pro obecné účely|Premium|Upgrade|Nejprve je třeba migrovat sekundární|
|Pro důležité obchodní informace|Pro obecné účely|Downgradovat|Nejprve je třeba migrovat primární|
|Pro obecné účely|Pro důležité obchodní informace|Upgrade|Nejprve je třeba migrovat sekundární|
||||

\*Každé 100 DTU na úrovni Standard vyžaduje aspoň 1 vCore a každé 125 DTU úrovně Premium vyžaduje aspoň 1 vCore.

### <a name="migrate-failover-groups"></a>Migrace skupin převzetí služeb při selhání

Migrace skupin převzetí služeb při selhání s více databázemi vyžaduje samostatnou migraci primárních a sekundárních databází. Během tohoto procesu platí stejné podmínky a pravidla sekvence. Po převedení databází na nákupní model založený na vCore zůstane skupina převzetí služeb při selhání platná se stejnými nastaveními zásad.

### <a name="create-a-geo-replication-secondary-database"></a>Vytvoření sekundární databáze geografické replikace

Sekundární databázi geografické replikace můžete vytvořit pouze pomocí stejné úrovně služby, jakou jste použili pro primární databázi. Pro databáze s vysokou rychlostí generování protokolů doporučujeme vytvořit geografickou sekundární stejnou velikost služby COMPUTE jako primární.

Pokud vytváříte geograficky sekundární v elastickém fondu pro jednu primární databázi, ujistěte se `maxVCore` , že nastavení fondu odpovídá výpočetní velikosti primární databáze. Pokud vytváříte geografickou sekundární hodnotu pro primární objekt v jiném elastickém fondu, doporučujeme, aby fondy měly stejné `maxVCore` nastavení.

### <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Použití kopie databáze k převedení databáze založené na DTU na databázi založenou na vCore

Můžete zkopírovat jakoukoli databázi s výpočetní velikostí založenou na DTU do databáze s výpočetní velikostí založenou na vCore bez omezení nebo speciální sekvencování, pokud cílová výpočetní velikost podporuje maximální velikost databáze zdrojové databáze. Kopie databáze vytvoří snímek dat pro počáteční čas operace kopírování a nesynchronizuje data mezi zdrojem a cílem.

## <a name="next-steps"></a>Další kroky

- Konkrétní velikosti a možnosti velikosti úložiště, které jsou dostupné pro jednotlivé databáze, najdete v tématu [SQL Database omezení prostředků na základě Vcore pro jednotlivé databáze](sql-database-vcore-resource-limits-single-databases.md).
- Konkrétní velikosti a možnosti velikosti úložiště dostupné pro elastické fondy najdete v tématu [SQL Database omezení prostředků na základě Vcore pro elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes).
