---
title: Obecné účely a důležité obchodní
description: Článek popisuje úrovně služeb pro obecné účely a důležité obchodní služby v nákupním modelu založeném na virtuálních jádrech.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 01/30/2020
ms.openlocfilehash: 09cc9e1475616700aa77cdf92fd7ca808cd4290c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937845"
---
# <a name="azure-sql-database-service-tiers"></a>Úrovně služeb Azure SQL Database

Azure SQL Database je založen na architektuře databázového stroje SQL Serveru, která je upravená pro cloudové prostředí, aby byla zajištěna 99,99% dostupnost, i když dojde k selhání infrastruktury. V Azure SQL Database se používají tři úrovně služeb, každá s jiným architektonickým modelem. Tyto úrovně služeb jsou:

- [Obecný účel](sql-database-service-tier-general-purpose.md), který je určen pro rozpočtově orientované úlohy.
- [Hyperscale](sql-database-service-tier-hyperscale.md), který je určen pro většinu obchodních úloh, poskytuje vysoce škálovatelné úložiště, horizontální navýšení kapacity pro čtení a rychlé možnosti obnovení databáze.
- [Důležité pro podnikání](sql-database-service-tier-business-critical.md), který je určen pro úlohy s nízkou latencí s vysokou odolností proti selhání a rychlé převzetí služeb při selhání.

Tento článek popisuje rozdíly, než jsou úrovně služeb, aspekty úložiště a zálohování pro obecné účely a důležité úrovně služeb pro důležité pro podnikání v nákupním modelu založeném na virtuálních jádrech.

## <a name="service-tier-comparison"></a>Porovnání úrovně služby

Následující tabulka popisuje klíčové rozdíly mezi úrovněmi služeb pro nejnovější generaci (Gen5). Všimněte si, že charakteristiky vrstvy služby se mohou lišit v jedné databázi a spravované instanci.

| | Typ prostředku | Pro obecné účely |  Hyperškálování | Kritické pro podnikání |
|:---:|:---:|:---:|:---:|:---:|
| **Nejlepší pro** | |  Nabízí rozpočtově orientované vyvážené výpočetní a úložné možnosti. | Většina obchodních úloh. Automatické škálování velikosti úložiště až 100 TB, plynulé vertikální a horizontální výpočetní škálování, rychlé obnovení databáze. | OLTP aplikace s vysokou transakční frekvencí a nízkou latencí vi. Nabízí nejvyšší odolnost proti selhání a rychlé převzetí služeb při selhání pomocí více synchronně aktualizovaných replik.|
|  **K dispozici v typu prostředku:** ||Jedna databáze / elastický fond / spravovaná instance | Izolovaná databáze | Jedna databáze / elastický fond / spravovaná instance |
| **Velikost výpočetních prostředků**|Jednotná databáze / elastický fond | 1 až 80 virtuálních jader | 1 až 80 virtuálních jader | 1 až 80 virtuálních jader |
| | Spravovaná instance | 4, 8, 16, 24, 32, 40, 64, 80 virtuálních jader | Není dostupné. | 4, 8, 16, 24, 32, 40, 64, 80 virtuálních jader |
| | Fondy spravovaných instancí | 2, 4, 8, 16, 24, 32, 40, 64, 80 virtuálních jader | Není dostupné. | Není dostupné. |
| **Typ úložiště** | Všechny | Vzdálené úložiště Premium (na instanci) | Odstranění spřažené úložiště s místní mezipamětí SSD (na instanci) | Super rychlé místní úložiště SSD (na instanci) |
| **Velikost databáze** | Jednotná databáze / elastický fond | 5 GB – 4 TB | Až 100 TB | 5 GB – 4 TB |
| | Spravovaná instance  | 32 GB – 8 TB | Není dostupné. | 32 GB – 4 TB |
| **Velikost úložiště** | Jednotná databáze / elastický fond | 5 GB – 4 TB | Až 100 TB | 5 GB – 4 TB |
| | Spravovaná instance  | 32 GB – 8 TB | Není dostupné. | 32 GB – 4 TB |
| **Velikost TempDB** | Jednotná databáze / elastický fond | [32 GB na virtuální jádro](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | [32 GB na virtuální jádro](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5) | [32 GB na virtuální jádro](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Spravovaná instance  | [24 GB na virtuální jádro](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | Není dostupné. | Až 4 TB – [omezeno velikostí úložiště](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
| **Propustnost zápisu protokolu** | Izolovaná databáze | [1,875 MB/s na virtuální jádro (max. 30 MB/s)](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/s | [6 MB/s na virtuální jádro (max. 96 MB/s)](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Spravovaná instance | [3 MB/s na virtuální jádro (max. 22 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | Není dostupné. | [4 MB/s na vcore (max. 48 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
|**Dostupnost**|Všechny| 99,99 % |  [99.95% s jednou sekundární replikou, 99.99% s více replikami](sql-database-service-tier-hyperscale-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99,99 % <br/> [99,995 % díky zónově redundantní jednotné databázi](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Zálohování**|Všechny|RA-GRS, 7-35 dní (ve výchozím nastavení 7 dní)| RA-GRS, 7 dní, konstantní časové bodové zotavení v čase (PITR) | RA-GRS, 7-35 dní (ve výchozím nastavení 7 dní) |
|**OLTP v paměti** | | Není dostupné. | Není dostupné. | K dispozici. |
|**Repliky jen pro čtení**| | 0 vestavěný <br> 0 - 4 pomocí [geografické replikace](sql-database-active-geo-replication.md) | 0 - 4 vestavěné | 1 vestavěný, v ceně <br> 0 - 4 pomocí [geografické replikace](sql-database-active-geo-replication.md) |
|**Ceny/fakturace** | Izolovaná databáze | [virtuálního jádra, vyhrazeného úložiště a úložiště záloh](https://azure.microsoft.com/pricing/details/sql-database/single/) se účtují. <br/>Vops se neúčtuje. | [virtuální jádro pro každou repliku a použité úložiště](https://azure.microsoft.com/pricing/details/sql-database/single/) se účtuje. <br/>IOPS ještě není nabitá. | [virtuálního jádra, vyhrazeného úložiště a úložiště záloh](https://azure.microsoft.com/pricing/details/sql-database/single/) se účtují. <br/>Vops se neúčtuje. |
|| MI | [virtuálního jádra, rezervovaného úložiště a úložiště záloh](https://azure.microsoft.com/pricing/details/sql-database/managed/) se účtuje. <br/>Vops se neúčtuje| Není dostupné. | [virtuálního jádra, rezervovaného úložiště a úložiště záloh](https://azure.microsoft.com/pricing/details/sql-database/managed/) se účtuje. <br/>Vops se neúčtuje.| 
|**Modely slev**| | [Rezervované instance](sql-database-reserved-capacity.md)<br/>[Hybridní výhoda Azure](sql-database-azure-hybrid-benefit.md) (není k dispozici u předplatných pro vývoj a testování)<br/>[Podniková](https://azure.microsoft.com/offers/ms-azr-0148p/) a [průběžná](https://azure.microsoft.com/offers/ms-azr-0023p/) dev/testovací předplatná| [Hybridní výhoda Azure](sql-database-azure-hybrid-benefit.md) (není k dispozici u předplatných pro vývoj a testování)<br/>[Podniková](https://azure.microsoft.com/offers/ms-azr-0148p/) a [průběžná](https://azure.microsoft.com/offers/ms-azr-0023p/) dev/testovací předplatná| [Rezervované instance](sql-database-reserved-capacity.md)<br/>[Hybridní výhoda Azure](sql-database-azure-hybrid-benefit.md) (není k dispozici u předplatných pro vývoj a testování)<br/>[Podniková](https://azure.microsoft.com/offers/ms-azr-0148p/) a [průběžná](https://azure.microsoft.com/offers/ms-azr-0023p/) dev/testovací předplatná|

Další informace naleznete v podrobných rozdílech mezi úrovněmi služeb v [single database (vCore)](sql-database-vcore-resource-limits-single-databases.md), [Single database pools (vCore)](sql-database-dtu-resource-limits-single-databases.md), [Single database (DTU)](sql-database-dtu-resource-limits-single-databases.md), [Single database pools (DTU)](sql-database-dtu-resource-limits-single-databases.md)a [Managed Instance](sql-database-managed-instance-resource-limits.md) pages.

> [!NOTE]
> Informace o vrstvě služby hyperškálování v nákupním modelu založeném na virtuálních jádrech naleznete v [tématu vrstva služby hyperškálování](sql-database-service-tier-hyperscale.md). Porovnání nákupního modelu založeného na virtuálních jádrech s nákupním modelem založeným na DTU najdete v [tématu Nákupní modely a prostředky azure SQL Database](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Ukládání dat a protokolů

Následující faktory ovlivňují velikost úložiště používaného pro datové soubory a soubory protokolu a platí pro důležité pro obecné účely a důležité pro podnikání. Podrobnosti o datech a úložišti protokolů ve velkém měřítku najdete v [tématu Vrstva služby Hyperscale](sql-database-service-tier-hyperscale.md).

- Přidělené úložiště využívají datové soubory (MDF) a soubory protokolu (LDF).
- Každá výpočetní velikost jedné databáze podporuje maximální velikost databáze s výchozí maximální velikostí 32 GB.
- Při konfiguraci požadované velikosti jedné databáze (velikost souboru MDF) se automaticky přidá další 30 procent úložiště pro podporu souborů LDF.
- Velikost úložiště pro spravovanou instanci databáze SQL musí být zadána v násobcích 32 GB.
- Můžete vybrat libovolnou velikost jedné databáze mezi 10 GB a podporovaným maximem.
  - Pro úložiště ve standardních nebo obecných úrovních služeb zvyšte nebo zmenšete velikost v krocích po 10 GB.
  - Pro úložiště v úrovních premium nebo business kritické služby, zvýšit nebo snížit velikost v 250 GB krocích.
- Ve vrstvě služby `tempdb` pro obecné účely používá připojený ssd džem a tyto náklady na úložiště jsou zahrnuty v ceně virtuálních jader.
- Ve vrstvě důležité `tempdb` služby podniku sdílí připojený ssd diod `tempdb` se soubory MDF a LDF a náklady na úložiště jsou zahrnuty v ceně virtuálního jádra.

> [!IMPORTANT]
> Bude vám účtováno celkové úložiště přidělené pro soubory MDF a LDF.

Chcete-li sledovat aktuální celkovou velikost souborů MDF a LDF, použijte [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Chcete-li sledovat aktuální velikost jednotlivých souborů MDF a LDF, použijte [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi, aby bylo možné uvolnit nevyužité místo. Další informace najdete [v tématu Správa místa v souborech v Azure SQL Database](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Zálohy a úložiště

Úložiště pro zálohování databáze je přiděleno pro podporu funkcí obnovení v čase (PITR) a [dlouhodobého uchovávání (LTR)](sql-database-long-term-retention.md) databáze SQL. Toto úložiště je přiděleno samostatně pro každou databázi a fakturováno jako dva samostatné poplatky za databázi.

- **PITR**: Jednotlivé zálohy databáze jsou automaticky zkopírovány do [geograficky redundantního úložiště pro čtení (RA-GRS).](../storage/common/storage-designing-ha-apps-with-ragrs.md) Velikost úložiště se dynamicky zvětšuje při vytváření nových záloh. Úložiště se používá týdenní úplné zálohy, denní rozdílové zálohy a zálohy transakční protokol, které jsou zkopírovány každých 5 minut. Spotřeba úložiště závisí na rychlosti změny databáze a dobu uchování pro zálohování. Můžete nakonfigurovat samostatné období uchovávání pro každou databázi mezi 7 a 35 dny. Minimální velikost úložiště rovnající se 100 procent (1x) velikosti databáze je poskytována bez dalších poplatků. Pro většinu databází je tato částka dostatečná pro uložení 7 dní záloh.
- **LTR**: SQL Database vám nabízí možnost konfigurace dlouhodobého uchovávání úplných záloh po dobu až 10 let. Pokud nastavíte zásadu LTR, tyto zálohy jsou automaticky uloženy v úložišti RA-GRS, ale můžete určit, jak často se zálohy kopírují. Chcete-li splnit různé požadavky na dodržování předpisů, můžete vybrat různé doby uchovávání pro týdenní, měsíční nebo roční zálohy. Konfigurace, kterou zvolíte, určuje, kolik úložiště bude použito pro zálohy LTR. Chcete-li odhadnout náklady na úložiště LTR, můžete použít cenovou kalkulačku LTR. Další informace naleznete v [tématu SQL Database dlouhodobé uchovávání](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Další kroky

- Podrobnosti o konkrétnívelikosti výpočetních prostředků a velikosti úložiště, které jsou k dispozici pro jednu databázi v obecnéúčely a obchodní kritické úrovně služeb, naleznete v [tématu SQL Database virtuálních jader omezení pro jednotlivé databáze](sql-database-vcore-resource-limits-single-databases.md).
- Podrobnosti o konkrétních velikostech výpočetních prostředků a velikostech úložiště, které jsou k dispozici pro elastické fondy v úrovních služeb pro obecné účely a důležité pro podniky, naleznete [v tématu limity prostředků založených na virtuálních jádrech SQL Database pro elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md).
