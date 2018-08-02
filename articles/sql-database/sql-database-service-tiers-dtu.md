---
title: Úrovně služby Azure SQL Database – DTU | Dokumentace Microsoftu
description: Další informace o úrovních služeb pro zadání jedné a fond databází do úrovní výkonu a velikosti úložiště.
services: sql-database
author: sachinpMSFT
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 5d16763fc8f3331082b98216d25190b945d95b60
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39411816"
---
# <a name="choosing-a-dtu-based-service-tier-performance-level-and-storage-resources"></a>Výběr úrovně služeb založený na DTU, úroveň výkonu a prostředků úložiště 

Úrovně služby jsou rozlišené pomocí rozsahu úrovní výkonu s pevnou velikost zahrnutého úložiště, pevné období uchování zálohy a pevnou cenu. Všechny úrovně služby poskytují flexibilitu změna úrovní výkonu bez výpadků. Izolované databáze a elastické fondy se účtují po hodinách podle služby vrstvu a úroveň výkonu.

> [!IMPORTANT]
> SQL Database Managed Instance aktuálně ve verzi public preview nepodporuje nákupní model založený na DTU. Další informace najdete v tématu [Azure SQL Database Managed Instance](sql-database-managed-instance.md). 

## <a name="choosing-a-dtu-based-service-tier"></a>Výběr úrovně služeb na základě DTU

Výběr úrovně služeb závisí primárně na obchodní kontinuity podnikových procesů, úložiště a požadavky na výkon.
||Basic|Standard|Premium|
| :-- | --: |--:| --:| --:| 
|Cílové úlohy|Vývoj a provoz|Vývoj a provoz|Vývoj a provoz||
|Smlouva SLA o provozuschopnosti|99,99 %|99,99 %|99,99 %|Není k dispozici ve verzi preview|
|Uchování záloh|7 dní|po dobu 35 dní|po dobu 35 dní|
|Procesor|Nízká|Nízká, střední, vysoká|Střední, vysoká|
|Vstupně-výstupní propustnost (přibližné) |2.5 vstupně-výstupních operací za DTU| 2.5 vstupně-výstupních operací za DTU | 48 vstupně-výstupních operací za DTU|
|Vstupně-výstupní latence (přibližné)|5 ms (čtení), 10 ms (zápis)|5 ms (čtení), 10 ms (zápis)|2 ms (čtení a zápis)|
|Indexu Columnstore |neuvedeno|S3 a novější|Podporováno|
|OLTP v paměti|neuvedeno|neuvedeno|Podporováno|
|||||

## <a name="single-database-dtu-and-storage-limits"></a>Omezení úložiště a jednotek DTU izolované databáze

Úrovně výkonu se vyjadřují v jednotkách transakcí databáze (DTU) pro samostatné databáze a jednotkách transakcí elastické databáze (eDTU) pro elastické fondy. Další informace o jednotkách Dtu a Edtu najdete v tématu [co jsou jednotky Dtu a Edtu](sql-database-service-tiers.md#what-are-database-transaction-units-dtus)?

||Basic|Standard|Premium|
| :-- | --: | --: | --: | --: |
| Maximální velikost úložiště | 2 GB | 1 TB | 4 TB  | 
| Maximální počet jednotek Dtu | 5 | 3000 | 4000 | |
||||||

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>Omezení databázi ve fondu, úložiště a eDTU pro elastický fond

| | **Basic** | **Standard** | **Premium** | 
| :-- | --: | --: | --: | --: |
| Maximální velikost úložiště na databázi  | 2 GB | 1 TB | 1 TB | 
| Maximální velikost úložiště na fond | 156 GB | 4 TB | 4 TB | 
| Maximální počet Edtu na databázi | 5 | 3000 | 4000 | 
| Maximální počet Edtu na fond | 1600 | 3000 | 4000 | 
| Maximální počet databází na fond | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> Více než 1 TB úložiště na úrovni Premium je aktuálně k dispozici ve všech oblastech kromě následujících: západní USA – střed, Čína – východ, USDoDCentral, Německo – střed, USDoDEast, USA (gov) – jihozápad, USGov Iowa, Německo-severovýchod, Čína – sever. V ostatních oblastech je úložiště na úrovni Premium omezeno na 1 TB. Viz [Aktuální omezení pro P11–P15](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md).

## <a name="next-steps"></a>Další postup

- Podrobnosti o konkrétní úrovně výkonu a možnosti velikosti úložiště dostupné pro izolované databáze, najdete v části [omezení prostředků založený na DTU databáze SQL pro izolované databáze](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-performance-levels).
- Podrobnosti o konkrétní úrovně výkonu a možnosti velikosti úložiště dostupné pro elastické fondy najdete v tématu [omezení prostředků na základě DTU databáze SQL](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels).
