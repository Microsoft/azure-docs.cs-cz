---
title: Úrovně služeb pro obecné účely a důležité pro podnikání
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Tento článek pojednává o úrovních služeb pro obecné účely a pro důležité obchodní informace v vCorem nákupním modelu, který používá Azure SQL Database a Azure SQL Managed instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 12/14/2020
ms.openlocfilehash: 95e11e98be8a58611a435de533ffcc16ec5ce357
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048552"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-service-tiers"></a>Azure SQL Database a úrovně služeb spravované instance Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database a Azure SQL Managed instance jsou založené na architektuře SQL Server databázového stroje, která je pro cloudové prostředí upravená, aby se zajistila 99,99% dostupnost, i když dojde k selhání infrastruktury. Služba Azure SQL Database a spravovaná instance Azure SQL používá dvě úrovně služeb, z nichž každý má jiný model architektury. Tyto úrovně služeb jsou:

- [Obecné účely](service-tier-general-purpose.md), který je navržený pro úlohy orientované na rozpočet.
- [Důležité pro podnikání](service-tier-business-critical.md), který je určený pro úlohy s nízkou latencí a vysokou odolností vůči chybám a rychlému převzetí služeb při selhání.

Azure SQL Database má další úroveň služby: 

- [Škálování na úrovni](service-tier-hyperscale.md), které je navržené pro většinu obchodních úloh a poskytuje vysoce škálovatelné úložiště, čtení na více instancí a rychlé možnosti obnovení databáze.

Tento článek popisuje rozdíly mezi úrovněmi služeb, požadavky na úložiště a zálohování pro úrovně služeb pro obecné účely a důležité obchodní informace v modelu nakupování založeném na vCore.

## <a name="service-tier-comparison"></a>Porovnání úrovně služeb

Následující tabulka popisuje klíčové rozdíly mezi úrovněmi služeb pro nejnovější generaci (Gen5). Všimněte si, že vlastnosti vrstvy služeb se můžou lišit v SQL Database a spravované instanci SQL.

|-| Typ prostředku | Pro obecné účely |  Hyperškálování | Pro důležité obchodní informace |
|:---:|:---:|:---:|:---:|:---:|
| **Nejvhodnější pro** | |  Nabízí možnosti pro vyvážené výpočty a úložiště s vyrovnanou rozpočtem. | Většina obchodních úloh. Automatické škálování velikosti úložiště až na 100 TB, škálování kapalinových vertikálních a horizontálních výpočetních škálování a rychlé obnovení databáze. | OLTP aplikace s vysokou mírou transakcí a nízkou latencí v/v. Nabízí nejvyšší odolnost proti chybám a rychlé převzetí služeb při selhání s využitím několika synchronně aktualizovaných replik.|
|  **K dispozici v typu prostředku:** ||SQL Database/spravované instance SQL | Jeden Azure SQL Database | SQL Database/spravované instance SQL |
| **Velikost výpočetního prostředí**| SQL Database | 1 až 80 virtuální jádra | 1 až 80 virtuální jádra | 1 až 80 virtuální jádra |
| | Spravovaná instance SQL | 4, 8, 16, 24, 32, 40, 64, 80 virtuální jádra | – | 4, 8, 16, 24, 32, 40, 64, 80 virtuální jádra |
| | Fondy spravované instance SQL | 2, 4, 8, 16, 24, 32, 40, 64, 80 virtuální jádra | N/A | N/A |
| **Typ úložiště** | Vše | Premium Remote Storage (na instanci) | Oddělené úložiště s místní mezipamětí SSD (na instanci) | Vysoce rychlé místní SSD úložiště (na instanci) |
| **Velikost databáze** | SQL Database | 5 GB – 4 TB | Až 100 TB | 5 GB – 4 TB |
| | Spravovaná instance SQL  | 32 GB – 8 TB | – | 32 GB – 4 TB |
| **Velikost úložiště** | SQL Database | 5 GB – 4 TB | Až 100 TB | 5 GB – 4 TB |
| | Spravovaná instance SQL  | 32 GB – 8 TB | – | 32 GB – 4 TB |
| **Velikost databáze TempDB** | SQL Database | [32 GB na vCore](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) | [32 GB na vCore](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5) | [32 GB na vCore](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Spravovaná instance SQL  | [24 GB na vCore](../managed-instance/resource-limits.md#service-tier-characteristics) | – | Až 4 TB – [omezeno velikostí úložiště](../managed-instance/resource-limits.md#service-tier-characteristics) |
| **Propustnost zápisu protokolu** | SQL Database | [1,875 MB/s na vCore (max. 30 MB/s)](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/s | [6 MB/s na vCore (max. 96 MB/s)](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Spravovaná instance SQL | [3 MB/s na vCore (max. 22 MB/s)](../managed-instance/resource-limits.md#service-tier-characteristics) | – | [4 MB/s na Vcore (max. 48 MB/s)](../managed-instance/resource-limits.md#service-tier-characteristics) |
|**Dostupnost**|Vše| 99,99 % |  [99,95% s jednou sekundární replikou, 99,99% s více replikami](service-tier-hyperscale-frequently-asked-questions-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99,99 % <br/> [99,995% s redundantní jedinou databází zóny](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Zálohování**|Vše|RA-GRS, 7-35 dní (ve výchozím nastavení 7 dnů). Maximální doba uchování úrovně Basic je 7 dní. | RA-GRS, 7 dní, časový interval pro obnovení v čase konstanty (PITR) | RA-GRS, 7-35 dní (ve výchozím nastavení 7 dnů) |
|**OLTP v paměti** | | N/A | N/A | K dispozici |
|**Repliky jen pro čtení**| | 0 – předdefinovaná <br> 0-4 použití [geografické replikace](active-geo-replication-overview.md) | 0-4 integrovaný | 1 Integrovaná, zahrnutá v ceně <br> 0-4 použití [geografické replikace](active-geo-replication-overview.md) |
|**Ceny a fakturace** | SQL Database | účtují se [Vcore, rezervované úložiště a úložiště záloh](https://azure.microsoft.com/pricing/details/sql-database/single/) . <br/>IOPS se neúčtuje. | účtují se [Vcore pro každou repliku a využité úložiště](https://azure.microsoft.com/pricing/details/sql-database/single/) . <br/>IOPS se ještě neúčtuje. | účtují se [Vcore, rezervované úložiště a úložiště záloh](https://azure.microsoft.com/pricing/details/sql-database/single/) . <br/>IOPS se neúčtuje. |
|| Spravovaná instance SQL | účtují se [Vcore, rezervované úložiště a úložiště zálohování](https://azure.microsoft.com/pricing/details/sql-database/managed/) . <br/>IOPS se neúčtuje.| – | účtují se [Vcore, rezervované úložiště a úložiště zálohování](https://azure.microsoft.com/pricing/details/sql-database/managed/) . <br/>IOPS se neúčtuje.| 
|**Modely slev**| | [Rezervované instance](reserved-capacity-overview.md)<br/>[Zvýhodněné hybridní využití Azure](../azure-hybrid-benefit.md) (není k dispozici v předplatných pro vývoj a testování)<br/>Předplatné [Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) a průběžné [platby podle](https://azure.microsoft.com/offers/ms-azr-0023p/) aktuálního využití pro vývoj/testování| [Zvýhodněné hybridní využití Azure](../azure-hybrid-benefit.md) (není k dispozici v předplatných pro vývoj a testování)<br/>Předplatné [Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) a průběžné [platby podle](https://azure.microsoft.com/offers/ms-azr-0023p/) aktuálního využití pro vývoj/testování| [Rezervované instance](reserved-capacity-overview.md)<br/>[Zvýhodněné hybridní využití Azure](../azure-hybrid-benefit.md) (není k dispozici v předplatných pro vývoj a testování)<br/>Předplatné [Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) a průběžné [platby podle](https://azure.microsoft.com/offers/ms-azr-0023p/) aktuálního využití pro vývoj/testování|

Další informace najdete v podrobných rozdílech mezi úrovněmi služeb v [Azure SQL Database (Vcore)](resource-limits-vcore-single-databases.md), [Single Azure SQL Database (DTU)](resource-limits-dtu-single-databases.md), ve [fondu Azure SQL Database (DTU)](resource-limits-dtu-single-databases.md)a na stránkách [spravované instance SQL Azure](../managed-instance/resource-limits.md) .

> [!NOTE]
> Informace o úrovni služby vCore v nákupním modelu založeném na najdete v tématu [úroveň služby pro škálování](service-tier-hyperscale.md)na více úrovních. Porovnání nákupního modelu založeného na DTU vCore s modelem nákupu založeném na DTU najdete v tématu [nakupování modelů a prostředků](purchasing-models.md).

## <a name="data-and-log-storage"></a>Data a úložiště protokolů

Následující faktory ovlivňují velikost úložiště používaného pro data a soubory protokolů a platí pro Pro obecné účely a Pro důležité obchodní informace. Podrobnosti o datech a úložištích protokolů v měřítku najdete v tématu [úroveň služby pro škálování](service-tier-hyperscale.md)na úrovni služby.

- Přidělené úložiště používají datové soubory (MDF) a soubory protokolu (LDF).
- Velikost výpočetní velikosti každé databáze podporuje maximální velikost databáze s výchozí maximální velikostí 32 GB.
- Když nakonfigurujete požadovanou velikost izolované databáze (velikost souboru MDF), k podpoře souborů LDF se automaticky přidá ještě 30% dalšího úložiště.
- Můžete vybrat libovolnou velikost databáze mezi 10 GB a podporovaným maximem.
  - Pro úložiště v úrovních služby Standard nebo pro obecné účely zvyšte nebo zmenšete velikost v přírůstcích po 10 GB.
  - V případě úložiště v úrovních služeb Premium nebo pro důležité obchodní hodnoty zvyšte nebo zmenšete velikost v 250 až GB.
- Na úrovni služby pro obecné účely se `tempdb` používá připojená jednotka SSD a náklady na úložiště jsou zahrnuté do vCoreové ceny.
- Na úrovni služby důležité pro podnikání `tempdb` sdílí připojenou jednotku SSD se soubory MDF a LDF a `tempdb` náklady na úložiště jsou zahrnuté v ceně Vcore.
- Ve vrstvě služeb DTU úrovně Premium `tempdb` sdílí připojené jednotky SSD se soubory MDF a LDF.
- Velikost úložiště pro spravovanou instanci SQL musí být zadaná v násobcích 32 GB.


> [!IMPORTANT]
> Účtuje se vám celkové přidělené úložiště pro soubory MDF a LDF.

Chcete-li monitorovat aktuální celkovou velikost souborů MDF a LDF, použijte [sp_spaceused](/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Chcete-li monitorovat aktuální velikost jednotlivých souborů MDF a LDF, použijte [Sys.database_files](/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi a uvolnit nevyužité místo. Další informace najdete v tématu [Správa prostoru souborů v Azure SQL Database](file-space-manage.md).

## <a name="backups-and-storage"></a>Zálohy a úložiště

Úložiště pro zálohy databáze je přiděleno pro podporu obnovení PITR (Point-in-time) a funkcí [dlouhodobého uchovávání (LTR)](long-term-retention-overview.md) SQL Database a SQL Managed instance. Toto úložiště se přiděluje samostatně pro každou databázi a účtuje se jako dvě samostatné poplatky za databázi.

- **PITR**: jednotlivé zálohy databáze se zkopírují do [úložiště s přístupem k geograficky redundantnímu (RA-GRS) s oprávněním pro čtení](../../storage/common/geo-redundant-design.md) . Velikost úložiště se dynamicky zvětšuje při vytváření nových záloh. Úložiště používá týdenní úplné zálohování, denní rozdílové zálohy a zálohy transakčních protokolů, které se zkopírují každých 5 minut. Spotřeba úložiště závisí na četnosti změn databáze a na době uchovávání záloh. Pro každou databázi můžete nastavit samostatné období uchování mezi 7 a 35 dny. Minimální velikost úložiště rovnající se 100% (1x) velikosti databáze se poskytuje bez dalších poplatků. U většiny databází je tato velikost dostačující pro ukládání 7 dnů zálohování.
- **Ltr**: máte také možnost konfigurovat dlouhodobé uchovávání úplných záloh po dobu až 10 let [pro spravovanou instanci SQL](long-term-retention-overview.md). Pokud nastavíte zásadu LTR, budou se tyto zálohy ukládat do úložiště RA-GRS automaticky, ale můžete určit, jak často se mají zálohy kopírovat. Pro splnění různých požadavků na dodržování předpisů můžete pro týdenní, měsíční nebo roční zálohy vybrat jiné doby uchování. Konfigurace, kterou zvolíte, určuje, kolik úložiště se bude používat pro zálohy LTR. K odhadu nákladů na úložiště LTR můžete použít cenovou kalkulačku LTR. Další informace najdete v tématu [SQL Database dlouhodobé uchovávání](long-term-retention-overview.md).

## <a name="next-steps"></a>Další kroky

Podrobnosti o specifických úložištích a velikostech úložiště dostupných v úrovních služeb pro obecné účely a důležité pro firmy najdete v těchto tématech: 

- [omezení prostředků založené na Vcore pro Azure SQL Database](resource-limits-vcore-single-databases.md).
- [omezení prostředků založené na Vcore pro databáze ve fondu v Azure SQL Database](resource-limits-vcore-elastic-pools.md).
- [omezení prostředků založené na Vcore pro spravovanou instanci Azure SQL](../managed-instance/resource-limits.md)
