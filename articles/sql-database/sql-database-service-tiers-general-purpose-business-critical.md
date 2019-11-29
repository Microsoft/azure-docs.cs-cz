---
title: Obecné účely a důležité pro podnikání
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
ms.date: 10/01/2019
ms.openlocfilehash: 5e3cc12351313b8fb1dedf795031202070ac7cf7
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2019
ms.locfileid: "74558992"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL Database úrovní služeb

Azure SQL Database vychází z SQL Server architektury databázového stroje, která je upravena pro cloudové prostředí, aby se zajistila 99,99% dostupnost, i když dojde k selhání infrastruktury. V Azure SQL Database se používají tři úrovně služeb, z nichž každý má jiný model architektury. Tyto úrovně služeb jsou:

- [Obecné účely](sql-database-service-tier-general-purpose.md), který je navržený pro úlohy orientované na rozpočet.
- [Škálování na úrovni](sql-database-service-tier-hyperscale.md), které je navržené pro většinu obchodních úloh a poskytuje vysoce škálovatelné úložiště, čtení na více instancí a rychlé možnosti obnovení databáze.
- [Důležité pro podnikání](sql-database-service-tier-business-critical.md), který je určený pro úlohy s nízkou latencí a vysokou odolností vůči chybám a rychlému převzetí služeb při selhání.

Tento článek pojednává o rozdílech, které se týkají porovnání úrovní služeb, úložiště a zálohování pro úrovně služeb pro obecné účely a důležité obchodní informace v rámci nákupního modelu založeného na vCore.

## <a name="service-tier-comparison"></a>Porovnání úrovně služeb

Následující tabulka popisuje klíčové rozdíly mezi úrovněmi služeb pro nejnovější generaci (Gen5). Všimněte si, že vlastnosti vrstvy služeb se můžou lišit v Izolovaná databáze a spravované instanci.

| | Typ prostředku | Obecné použití |  Hyperškálování | Pro důležité obchodní informace |
|:---:|:---:|:---:|:---:|:---:|
| **Nejlepší pro** | |  Nabízí možnosti pro vyvážené výpočty a úložiště s vyrovnanou rozpočtem. | Většina obchodních úloh. Automatické škálování velikosti úložiště až na 100 TB, škálování kapalinových vertikálních a horizontálních výpočetních škálování a rychlé obnovení databáze. | OLTP aplikace s vysokou mírou transakcí a nízkou latencí v/v. Nabízí nejvyšší odolnost proti chybám a rychlé převzetí služeb při selhání s využitím několika synchronně aktualizovaných replik.|
|  **K dispozici v typu prostředku:** ||Jedna databáze/elastický fond/spravovaná instance | Izolovaná databáze | Jedna databáze/elastický fond/spravovaná instance |
| **Velikost výpočetního prostředí**|Izolovaná databáze/elastický fond | 1 až 80 virtuální jádra | 1 až 80 virtuální jádra | 1 až 80 virtuální jádra |
| | Spravovaná instance | 4, 8, 16, 24, 32, 40, 64, 80 virtuální jádra | Nevztahuje se | 4, 8, 16, 24, 32, 40, 64, 80 virtuální jádra |
| | Fondy spravovaných instancí | 2, 4, 8, 16, 24, 32, 40, 64, 80 virtuální jádra | Nevztahuje se | Nevztahuje se |
| **Typ úložiště** | Všechno | Premium Remote Storage (na instanci) | Oddělené úložiště s místní mezipamětí SSD (na instanci) | Vysoce rychlé místní SSD úložiště (na instanci) |
| **Velikost databáze** | Izolovaná databáze/elastický fond | 5 GB – 4 TB | Až 100 TB | 5 GB – 4 TB |
| | Spravovaná instance  | 32 GB – 8 TB | Nevztahuje se | 32 GB – 4 TB |
| **Velikost úložiště** | Izolovaná databáze/elastický fond | 5 GB – 4 TB | Až 100 TB | 5 GB – 4 TB |
| | Spravovaná instance  | 32 GB – 8 TB | Nevztahuje se | 32 GB – 4 TB |
| **Velikost databáze TempDB** | Izolovaná databáze/elastický fond | [32 GB na vCore](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | [32 GB na vCore](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5) | [32 GB na vCore](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Spravovaná instance  | [24 GB na vCore](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | Nevztahuje se | Až 4 TB – [omezeno velikostí úložiště](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
| **Propustnost zápisu protokolu** | Izolovaná databáze | [1,875 MB/s na vCore (max. 30 MB/s)](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/s | [6 MB/s na vCore (max. 96 MB/s)](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Spravovaná instance | [3 MB/s na vCore (max. 22 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | Nevztahuje se | [4 MB/s na Vcore (max. 48 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
|**Dostupnost**|Všechno| 99,99 % |  [99,95% s jednou sekundární replikou, 99,99% s více replikami](sql-database-service-tier-hyperscale-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99,99 % <br/> [99,995% s redundantní jedinou databází zóny](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Vytvářet**|Všechno|RA-GRS, 7-35 dní (ve výchozím nastavení 7 dnů)| RA-GRS, 7 dní, časový interval pro obnovení v čase konstanty (PITR) | RA-GRS, 7-35 dní (ve výchozím nastavení 7 dnů) |
|**OLTP v paměti** | | Nevztahuje se | Nevztahuje se | Dostupné |
|**Repliky jen pro čtení**| | 0  | 0 - 4 | 1 (integrovaná, zahrnutá v ceně) |
|**Ceny a fakturace** | Izolovaná databáze | účtují se [Vcore, rezervované úložiště a úložiště záloh](https://azure.microsoft.com/pricing/details/sql-database/single/) . <br/>IOPS se neúčtuje. | účtují se [Vcore pro každou repliku a využité úložiště](https://azure.microsoft.com/pricing/details/sql-database/single/) . <br/>IOPS se ještě neúčtuje. | účtují se [Vcore, rezervované úložiště a úložiště záloh](https://azure.microsoft.com/pricing/details/sql-database/single/) . <br/>IOPS se neúčtuje. |
|| Managed Instance | účtuje se [Vcore a rezervované úložiště](https://azure.microsoft.com/pricing/details/sql-database/managed/) . <br/>IOPS se neúčtuje.<br/>Úložiště zálohování se ještě neúčtuje. | Nevztahuje se | účtuje se [Vcore a rezervované úložiště](https://azure.microsoft.com/pricing/details/sql-database/managed/) . <br/>IOPS se neúčtuje.<br/>Úložiště zálohování se ještě neúčtuje. | 
|**Modely slev**| | [Rezervované instance](sql-database-reserved-capacity.md)<br/>[Zvýhodněné hybridní využití Azure](sql-database-azure-hybrid-benefit.md) (není k dispozici v předplatných pro vývoj a testování)<br/>Předplatné [Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) a průběžné [platby podle](https://azure.microsoft.com/offers/ms-azr-0023p/) aktuálního využití pro vývoj/testování| [Zvýhodněné hybridní využití Azure](sql-database-azure-hybrid-benefit.md) (není k dispozici v předplatných pro vývoj a testování)<br/>Předplatné [Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) a průběžné [platby podle](https://azure.microsoft.com/offers/ms-azr-0023p/) aktuálního využití pro vývoj/testování| [Rezervované instance](sql-database-reserved-capacity.md)<br/>[Zvýhodněné hybridní využití Azure](sql-database-azure-hybrid-benefit.md) (není k dispozici v předplatných pro vývoj a testování)<br/>Předplatné [Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) a průběžné [platby podle](https://azure.microsoft.com/offers/ms-azr-0023p/) aktuálního využití pro vývoj/testování|

Další informace najdete v podrobných rozdílech mezi úrovněmi služby v izolovaných [databázích (Vcore)](sql-database-vcore-resource-limits-single-databases.md), [fondech izolovaných databází (Vcore)](sql-database-dtu-resource-limits-single-databases.md), izolovanými databázemi [(DTU)](sql-database-dtu-resource-limits-single-databases.md), fondy izolovaných [databází (DTU)](sql-database-dtu-resource-limits-single-databases.md)a stránkami [spravované instance](sql-database-managed-instance-resource-limits.md) .

> [!NOTE]
> Informace o úrovni služby vCore v nákupním modelu založeném na najdete v tématu [úroveň služby pro škálování](sql-database-service-tier-hyperscale.md)na více úrovních. Porovnání nákupního modelu založeného na DTU v vCore s nákupním modelem založeným na DTU najdete v tématu [Azure SQL Database nákupu modelů a prostředků](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Data a úložiště protokolů

Následující faktory ovlivňují velikost úložiště používaného pro data a soubory protokolů a platí pro Pro obecné účely a Pro důležité obchodní informace. Podrobnosti o datech a úložištích protokolů v měřítku najdete v tématu [úroveň služby pro škálování](sql-database-service-tier-hyperscale.md)na úrovni služby.

- Přidělené úložiště používají datové soubory (MDF) a soubory protokolu (LDF).
- Velikost výpočetní velikosti každé databáze podporuje maximální velikost databáze s výchozí maximální velikostí 32 GB.
- Když nakonfigurujete požadovanou velikost izolované databáze (velikost souboru MDF), k podpoře souborů LDF se automaticky přidá ještě 30% dalšího úložiště.
- Velikost úložiště pro SQL Database spravovanou instanci musí být zadaná v násobcích 32 GB.
- Můžete vybrat libovolnou velikost databáze mezi 10 GB a podporovaným maximem.
  - Pro úložiště v úrovních služby Standard nebo pro obecné účely zvyšte nebo zmenšete velikost v přírůstcích po 10 GB.
  - V případě úložiště v úrovních služeb Premium nebo pro důležité obchodní hodnoty zvyšte nebo zmenšete velikost v 250 až GB.
- V úrovni služby pro obecné účely `tempdb` používá připojenou jednotku SSD a náklady na úložiště jsou zahrnuté v ceně vCore.
- V podnikové úrovni služby `tempdb` sdílet připojené jednotky SSD se soubory MDF a LDF a náklady na úložiště `tempdb` jsou zahrnuté v ceně vCore.

> [!IMPORTANT]
> Účtuje se vám celkové přidělené úložiště pro soubory MDF a LDF.

Chcete-li monitorovat aktuální celkovou velikost souborů MDF a LDF, použijte [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Chcete-li monitorovat aktuální velikost jednotlivých souborů MDF a LDF, použijte [Sys. database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi a uvolnit nevyužité místo. Další informace najdete v tématu [Správa prostoru souborů v Azure SQL Database](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Zálohy a úložiště

Úložiště pro zálohy databáze je přiděleno pro podporu obnovení PITR (Point-in-time) a [dlouhodobého uchování (LTR)](sql-database-long-term-retention.md) SQL Database. Toto úložiště se přiděluje samostatně pro každou databázi a účtuje se jako dvě samostatné poplatky za databázi.

- **PITR**: jednotlivé zálohy databáze se zkopírují do [úložiště s přístupem k geograficky redundantnímu (RA-GRS) s oprávněním pro čtení](../storage/common/storage-designing-ha-apps-with-ragrs.md) . Velikost úložiště se dynamicky zvětšuje při vytváření nových záloh. Úložiště používá týdenní úplné zálohování, denní rozdílové zálohy a zálohy transakčních protokolů, které se zkopírují každých 5 minut. Spotřeba úložiště závisí na četnosti změn databáze a na době uchovávání záloh. Pro každou databázi můžete nastavit samostatné období uchování mezi 7 a 35 dny. Minimální velikost úložiště rovnající se 100% (1x) velikosti databáze se poskytuje bez dalších poplatků. U většiny databází je tato velikost dostačující pro ukládání 7 dnů zálohování.
- **Ltr**: SQL Database nabízí možnost konfigurace dlouhodobého uchovávání úplných záloh po dobu až 10 let. Pokud nastavíte zásadu LTR, budou se tyto zálohy ukládat do úložiště RA-GRS automaticky, ale můžete určit, jak často se mají zálohy kopírovat. Pro splnění různých požadavků na dodržování předpisů můžete pro týdenní, měsíční nebo roční zálohy vybrat jiné doby uchování. Konfigurace, kterou zvolíte, určuje, kolik úložiště se bude používat pro zálohy LTR. K odhadu nákladů na úložiště LTR můžete použít cenovou kalkulačku LTR. Další informace najdete v tématu [SQL Database dlouhodobé uchovávání](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Další kroky

- Podrobnosti o specifických velikostech a velikostech úložiště, které jsou dostupné pro izolovanou databázi, najdete v tématu [SQL Database omezení prostředků na základě Vcore pro jednotlivé databáze](sql-database-vcore-resource-limits-single-databases.md).
- Podrobnosti o specifických velikostech a velikostech úložiště, které jsou dostupné pro elastické fondy, najdete v tématu [SQL Database omezení prostředků na základě Vcore pro elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md).
