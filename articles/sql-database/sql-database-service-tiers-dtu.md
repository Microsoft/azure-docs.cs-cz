---
title: Úrovně služeb Azure SQL Database - DTU | Microsoft Docs
description: Další informace o úrovních služeb pro jeden a zadejte velikost úložiště a úrovně výkonu databáze fondu.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 143bbde2da990c146e63b8f0216388c03ddd5852
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36307753"
---
# <a name="choosing-a-dtu-based-service-tier-performance-level-and-storage-resources"></a>Výběr vrstvy služeb na základě DTU, úroveň výkonu a prostředků úložiště 

Úrovně služeb jsou rozlišené pomocí rozsah úrovně výkonu s pevnou velikostí součástí úložiště, pevné dobu uchování záloh a pevné ceny. Všechny úrovně služeb poskytují flexibilitu Změna úrovně výkonu bez výpadků. Izolované databáze i elastické fondy se fakturuje po hodinách podle úrovně služeb a výkonu.

> [!IMPORTANT]
> Instance databáze serveru SQL spravované, aktuálně ve verzi public preview nepodporuje nákupní model na základě DTU. Další informace najdete v tématu [Azure SQL Database spravované Instance](sql-database-managed-instance.md). 

## <a name="choosing-a-dtu-based-service-tier"></a>Volba úrovně služeb na základě DTU

Volba úrovně služby závisí hlavně na kontinuity podnikových procesů, úložiště a požadavky na výkon.
||Basic|Standard|Premium|
| :-- | --: |--:| --:| --:| 
|Cíl pracovního vytížení|Vývoj a výroby|Vývoj a výroby|Vývoj a výroby||
|Smlouva SLA o provozuschopnosti|99,99 %|99,99 %|99,99 %|Není k dispozici při ve verzi preview|
|Uchování záloh|7 dní|35 dní|35 dní|
|Procesor|Nízká|Nízká, střední, vysoká|Střední, vysoká|
|Propustnost vstupně-výstupní operace (přibližnou) |2.5 IOPS na DTU| 2.5 IOPS na DTU | 48 IOPS na DTU|
|Latence vstupně-výstupní operace (přibližnou)|5 ms (načíst), 10 ms (zápisu)|5 ms (načíst), 10 ms (zápisu)|2 ms (čtení a zápis)|
|Indexu Columnstore |neuvedeno|S3 a novější|Podporováno|
|OLTP v paměti|neuvedeno|neuvedeno|Podporováno|
|||||

## <a name="single-database-dtu-and-storage-limits"></a>Omezení jednotek DTU a úložiště jedné databáze

Úrovně výkonu se vyjadřují v jednotkách transakcí databáze (DTU) pro samostatné databáze a jednotkách transakcí elastické databáze (eDTU) pro elastické fondy. Další informace o Dtu a Edtu najdete v tématu [co jsou Dtu a Edtu](sql-database-what-is-a-dtu.md)?

||Basic|Standard|Premium|
| :-- | --: | --: | --: | --: |
| Maximální velikost úložiště | 2 GB | 1 TB | 4 TB  | 
| Maximální počet jednotek Dtu | 5 | 3000 | 4000 | |
||||||

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>Elastický fond eDTU, úložiště a omezení databáze ve fondu

| | **Basic** | **Standard** | **Premium** | 
| :-- | --: | --: | --: | --: |
| Maximální velikost úložiště pro databázi  | 2 GB | 1 TB | 1 TB | 
| Maximální velikost úložiště pro fond | 156 GB | 4 TB | 4 TB | 
| Maximální počet jednotek Edtu na databázi | 5 | 3000 | 4000 | 
| Maximální počet jednotek Edtu na fond | 1600 | 3000 | 4000 | 
| Maximální počet databází na každý fond | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> Více než 1 TB místa v úrovni Premium je nyní k dispozici ve všech oblastech s následující výjimkou: Spojené království – sever, – Západ střední USA, UK South2, východní Čína, USDoDCentral, Německo – střed, indiánský motiv verze pro státní správu USDoDEast, USA, nám verze pro státní správu – jih centrální, Německo – severovýchod, Čína Severní, verze pro státní správu USA – východ. V ostatních oblastech je úložiště na úrovni Premium omezeno na 1 TB. Viz [Aktuální omezení pro P11–P15](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  

## <a name="next-steps"></a>Další postup

- Podrobnosti o konkrétní úrovně výkonu a možnosti velikosti úložiště k dispozici pro izolované databáze najdete v tématu [limitů prostředků na základě DTU databáze SQL pro izolované databáze](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-performance-levels).
- Podrobnosti o konkrétní úrovně výkonu a možnosti velikosti úložiště k dispozici pro elastické fondy najdete v tématu [limitů prostředků na základě DTU databáze SQL](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels).