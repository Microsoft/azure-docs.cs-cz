---
title: Azure SQL Database – obecný účel a důležité pro podnikání | Microsoft Docs
description: Článek popisuje úrovně služeb pro obecné účely a důležité pro firmy v rámci nákupního modelu založeného na vCore.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 02/23/2019
ms.openlocfilehash: 41acef4ebe13ac6152d795db4adfae5a6ae1ad91
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995427"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL Database úrovní služeb

Azure SQL Database vychází z SQL Server architektury databázového stroje, která je upravena pro cloudové prostředí, aby se zajistila 99,99% dostupnost, i když dojde k selhání infrastruktury. V Azure SQL Database se používají tři úrovně služeb, z nichž každý má jiný model architektury. Tyto úrovně služeb jsou:

- [Obecné účely](sql-database-service-tier-general-purpose.md), který je určený pro většinu obecných úloh.
- [Důležité pro podnikání](sql-database-service-tier-business-critical.md), který je určený pro úlohy s nízkou latencí s jednou čitelnou replikou.
- [Škálovatelné](sql-database-service-tier-hyperscale.md), navržené pro velmi velké databáze (až 100 TB) s několika replikami, které lze číst.

Tento článek pojednává o rozdílech, které se týkají porovnání úrovní služeb, úložiště a zálohování pro úrovně služeb pro obecné účely a důležité obchodní informace v rámci nákupního modelu založeného na vCore.

## <a name="service-tier-comparison"></a>Porovnání úrovně služeb

Následující tabulka popisuje klíčové rozdíly mezi úrovněmi služeb pro nejnovější generaci (Gen5). Všimněte si, že vlastnosti vrstvy služeb se můžou lišit v Izolovaná databáze a spravované instanci.

| | Typ prostředku | Obecné použití |  Hyperškálování | Obchodně klíčové |
|:---:|:---:|:---:|:---:|:---:|
| **Nejlepší pro** | |  Většina obchodních úloh. Nabízí možnosti pro vyvážené výpočty a úložiště s vyrovnanou rozpočtem. | Datové aplikace s požadavky na velké objemy dat, schopnost automatického škálování úložiště až na 100 TB a škálování výpočetních kapalin. | OLTP aplikace s vysokou mírou transakcí a nejnižší latencí v/v. Nabízí nejvyšší odolnost proti chybám při použití několika izolovaných replik.|
|  **K dispozici v typu prostředku:** ||Jedna databáze/elastický fond/spravovaná instance | Izolovaná databáze | Jedna databáze/elastický fond/spravovaná instance |
| **Velikost výpočetního prostředí**|Izolovaná databáze/elastický fond | 1 až 80 virtuální jádra | 1 až 80 virtuální jádra | 1 až 80 virtuální jádra |
| | Spravovaná instance | 4, 8, 16, 24, 32, 40, 64, 80 virtuální jádra | Není k dispozici | 4, 8, 16, 24, 32, 40, 64, 80 virtuální jádra |
| | Fondy spravovaných instancí | 2, 4, 8, 16, 24, 32, 40, 64, 80 virtuální jádra | Není k dispozici | Není k dispozici |
| **Typ úložiště** | Vše | Premium Remote Storage (na instanci) | Oddělené úložiště s místní mezipamětí SSD (na instanci) | Vysoce rychlé místní SSD úložiště (na instanci) |
| **Velikost databáze** | Izolovaná databáze/elastický fond | 5 GB – 4 TB | Až 100 TB | 5 GB – 4 TB |
| | Spravovaná instance  | 32 GB – 8 TB | Není k dispozici | 32 GB – 4 TB |
| **Velikost úložiště** | Izolovaná databáze/elastický fond | 5 GB – 4 TB | Až 100 TB | 5 GB – 4 TB |
| | Spravovaná instance  | 32 GB – 8 TB | Není k dispozici | 32 GB – 4 TB |
| **Velikost databáze TempDB** | Izolovaná databáze/elastický fond | [32 GB na vCore](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-for-provisioned-compute) | [32 GB na vCore](sql-database-vcore-resource-limits-single-databases.md#hyperscale-service-tier-for-provisioned-compute) | [32 GB na vCore](sql-database-vcore-resource-limits-single-databases.md#business-critical-service-tier-for-provisioned-compute) |
| | Spravovaná instance  | [24 GB na vCore](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | Není k dispozici | Až 4 TB – [omezeno velikostí úložiště](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
| **Propustnost zápisu protokolu** | Izolovaná databáze | [1,875 MB/s na vCore (max. 30 MB/s)](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-for-provisioned-compute) | 100 MB/s | [6 MB/s na vCore (max. 96 MB/s)](sql-database-vcore-resource-limits-single-databases.md#business-critical-service-tier-for-provisioned-compute) |
| | Spravovaná instance | [3 MB/s na vCore (max. 22 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | Není k dispozici | [4 MB/s na Vcore (max. 48 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
|**Dostupnost**|Vše| 99,99 % |  [99,95% s jednou sekundární replikou, 99,99% s více replikami](sql-database-service-tier-hyperscale-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99,99 % <br/> [99,995% s redundantní jedinou databází zóny](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Vytvářet**|Vše|RA-GRS, 7-35 dní (ve výchozím nastavení 7 dnů)| RA-GRS, 7 dní, časový interval pro obnovení v čase konstanty (PITR) | RA-GRS, 7-35 dní (ve výchozím nastavení 7 dnů) |
|**OLTP v paměti** | | Není k dispozici | Není k dispozici | K dispozici |
|**Repliky jen pro čtení**| | 0  | 0 - 4 | 1 (integrovaná, zahrnutá v ceně) |
|**Ceny a fakturace** | Izolovaná databáze | účtují se [Vcore, rezervované úložiště a úložiště záloh](https://azure.microsoft.com/pricing/details/sql-database/single/) . <br/>IOPS se neúčtuje. | účtují se [Vcore pro každou repliku a využité úložiště](https://azure.microsoft.com/pricing/details/sql-database/single/) . <br/>IOPS se neúčtuje.<br/>Úložiště zálohování se ještě neúčtuje. | účtují se [Vcore, rezervované úložiště a úložiště záloh](https://azure.microsoft.com/pricing/details/sql-database/single/) . <br/>IOPS se neúčtuje. |
|| MI | účtuje se [Vcore a rezervované úložiště](https://azure.microsoft.com/pricing/details/sql-database/managed/) . <br/>IOPS se neúčtuje.<br/>Úložiště zálohování se ještě neúčtuje. | Není k dispozici | účtuje se [Vcore a rezervované úložiště](https://azure.microsoft.com/pricing/details/sql-database/managed/) . <br/>IOPS se neúčtuje.<br/>Úložiště zálohování se ještě neúčtuje. | 
|**Modely slev**| | [Rezervované instance](sql-database-reserved-capacity.md)<br/>[Zvýhodněné hybridní využití Azure](sql-database-service-tiers-vcore.md#azure-hybrid-benefit) (není k dispozici pro odběry pro vývoj a testování)<br/>Předplatné [Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) a průběžné [platby podle](https://azure.microsoft.com/offers/ms-azr-0023p/) aktuálního využití pro vývoj/testování| [Zvýhodněné hybridní využití Azure](sql-database-service-tiers-vcore.md#azure-hybrid-benefit) (není k dispozici pro odběry pro vývoj a testování)<br/>Předplatné [Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) a průběžné [platby podle](https://azure.microsoft.com/offers/ms-azr-0023p/) aktuálního využití pro vývoj/testování| [Rezervované instance](sql-database-reserved-capacity.md)<br/>[Zvýhodněné hybridní využití Azure](sql-database-service-tiers-vcore.md#azure-hybrid-benefit) (není k dispozici pro odběry pro vývoj a testování)<br/>Předplatné [Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) a průběžné [platby podle](https://azure.microsoft.com/offers/ms-azr-0023p/) aktuálního využití pro vývoj/testování|

Další informace najdete v podrobných rozdílech mezi úrovněmi služby v izolovaných [databázích (Vcore)](sql-database-vcore-resource-limits-single-databases.md), [fondech s izolovanými databázemi (Vcore)](sql-database-dtu-resource-limits-single-databases.md), izolovanými databázemi [(DTU)](sql-database-dtu-resource-limits-single-databases.md), fondy izolovaných [databází (DTU)](sql-database-dtu-resource-limits-single-databases.md)a [spravovanou instancí](sql-database-managed-instance-resource-limits.md) . stránky.

> [!NOTE]
> Informace o úrovni služby vCore v nákupním modelu založeném na najdete v tématu [úroveň služby pro škálování](sql-database-service-tier-hyperscale.md)na více úrovních. Porovnání nákupního modelu založeného na DTU v vCore s nákupním modelem založeným na DTU najdete v tématu [Azure SQL Database nákupu modelů a prostředků](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Data a úložiště protokolů

Velikost úložiště využitého pro data a soubory protokolu ovlivňují následující faktory:

- Přidělené úložiště používají datové soubory (MDF) a soubory protokolu (LDF).
- Velikost výpočetní velikosti každé databáze podporuje maximální velikost databáze s výchozí maximální velikostí 32 GB.
- Když nakonfigurujete požadovanou velikost izolované databáze (velikost souboru MDF), k podpoře souborů LDF se automaticky přidá ještě 30% dalšího úložiště.
- Velikost úložiště pro SQL Database spravovanou instanci musí být zadaná v násobcích 32 GB.
- Můžete vybrat libovolnou velikost databáze mezi 10 GB a podporovaným maximem.
  - Pro úložiště v úrovních služby Standard nebo pro obecné účely zvyšte nebo zmenšete velikost v přírůstcích po 10 GB.
  - V případě úložiště v úrovních služeb Premium nebo pro důležité obchodní hodnoty zvyšte nebo zmenšete velikost v 250 až GB.
- Na úrovni `tempdb` služby pro obecné účely se používá připojená jednotka SSD a náklady na úložiště jsou zahrnuté do vCoreové ceny.
- Na úrovni `tempdb` služby důležité pro podnikání sdílí připojenou jednotku SSD se soubory MDF a LDF `tempdb` a náklady na úložiště jsou zahrnuté v ceně Vcore.

> [!IMPORTANT]
> Účtuje se vám celkové přidělené úložiště pro soubory MDF a LDF.

Chcete-li monitorovat aktuální celkovou velikost souborů MDF a LDF, použijte [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Chcete-li monitorovat aktuální velikost jednotlivých souborů MDF a LDF, použijte [Sys. database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Zálohy a úložiště

Úložiště pro zálohy databáze je přiděleno pro podporu obnovení PITR (Point-in-time) a [dlouhodobého uchování (LTR)](sql-database-long-term-retention.md) SQL Database. Toto úložiště se přiděluje samostatně pro každou databázi a účtuje se jako dvě samostatné poplatky za databázi.

- **PITR**: Jednotlivé zálohy databáze se zkopírují do [úložiště s přístupem pro čtení geograficky redundantního úložiště (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md) . Velikost úložiště se dynamicky zvětšuje při vytváření nových záloh. Úložiště používá týdenní úplné zálohování, denní rozdílové zálohy a zálohy transakčních protokolů, které se zkopírují každých 5 minut. Spotřeba úložiště závisí na četnosti změn databáze a na době uchovávání záloh. Pro každou databázi můžete nastavit samostatné období uchování mezi 7 a 35 dny. Minimální velikost úložiště rovnající se 100% (1x) velikosti databáze se poskytuje bez dalších poplatků. U většiny databází je tato velikost dostačující pro ukládání 7 dnů zálohování.
- **LTR**: SQL Database nabízí možnost konfigurace dlouhodobého uchovávání úplných záloh po dobu až 10 let. Pokud nastavíte zásadu LTR, budou se tyto zálohy ukládat do úložiště RA-GRS automaticky, ale můžete určit, jak často se mají zálohy kopírovat. Pro splnění různých požadavků na dodržování předpisů můžete pro týdenní, měsíční nebo roční zálohy vybrat jiné doby uchování. Konfigurace, kterou zvolíte, určuje, kolik úložiště se bude používat pro zálohy LTR. K odhadu nákladů na úložiště LTR můžete použít cenovou kalkulačku LTR. Další informace najdete v tématu [SQL Database dlouhodobé uchovávání](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Další kroky

- Podrobnosti o specifických velikostech a velikostech úložiště, které jsou dostupné pro izolovanou databázi, najdete v tématu [SQL Database omezení prostředků na základě Vcore pro jednotlivé databáze](sql-database-vcore-resource-limits-single-databases.md).
- Podrobnosti o specifických velikostech a velikostech úložiště, které jsou dostupné pro elastické fondy, najdete v tématu [SQL Database omezení prostředků na základě Vcore pro elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md).
