---
title: "Služba Azure SQL Database | Microsoft Docs"
description: "Další informace o úrovních služeb pro jeden a zadejte velikost úložiště a úrovně výkonu databáze fondu."
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 03/15/2018
ms.author: carlrab
ms.openlocfilehash: de04e54c290657bc4e2ca20bbf10ba03f883dd42
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="what-are-azure-sql-database-service-tiers"></a>Co je Azure SQL Database úrovně služeb?

[Azure SQL Database](sql-database-technical-overview.md) nabízí **základní**, **standardní**, a **Premium** úrovních služeb pro obě [jedna databáze](sql-database-single-database-resources.md) a [elastické fondy](sql-database-elastic-pool.md). SQL Database nabízí vrstvy služeb pro obecné účely [Azure SQL Database spravované Instance](sql-database-managed-instance.md#managed-instance-service-tier). Úrovně služeb jsou primárně rozlišené pomocí celou řadu úroveň výkonu a možnosti velikosti úložiště a ceny.  Všechny úrovně služeb poskytují flexibilitu při změně velikosti výkonu úroveň a úložiště.  Izolované databáze i elastické fondy se fakturuje po hodinách podle úrovně služby, úroveň výkonu a velikost úložiště.   

> [!IMPORTANT]
> Instance databáze serveru SQL spravované, aktuálně ve verzi public preview, nabízí vrstvu služby jednotného pro obecné účely. Další informace najdete v tématu [Azure SQL Database spravované Instance](sql-database-managed-instance.md). Zbývající část tohoto článku se nevztahuje na spravované Instance.

## <a name="choosing-a-service-tier"></a>Výběr úrovně služeb

Volba úrovně služby závisí hlavně na kontinuity podnikových procesů, úložiště a požadavky na výkon.
| | **Basic** | **Standard** |**Premium**  |
| :-- | --: |--:| --:| --:| 
|Cíl pracovního vytížení|Vývoj a výroby|Vývoj a výroby|Vývoj a výroby||
|Smlouva SLA o provozuschopnosti|99,99 %|99,99 %|99,99 %|Není k dispozici při ve verzi preview|
|Uchování záloh|7 dní|35 dní|35 dní|
|Procesor|Nízká|Nízká, střední, vysoká|Střední, vysoká|
|Propustnost vstupně-výstupní operace (přibližnou) |2.5 IOPS na DTU  | 2.5 IOPS na DTU | 48 IOPS na DTU|
|Latence vstupně-výstupní operace (přibližnou)|5 ms (načíst), 10 ms (zápisu)|5 ms (načíst), 10 ms (zápisu)|2 ms (čtení a zápis)|
|Indexu Columnstore a OLTP v paměti|neuvedeno|neuvedeno|Podporováno|
|||||

## <a name="performance-level-and-storage-size-limits"></a>Omezení velikosti úroveň výkonu a úložiště

Úrovně výkonu se vyjadřují v jednotkách transakcí databáze (DTU) pro samostatné databáze a jednotkách transakcí elastické databáze (eDTU) pro elastické fondy. Další informace o Dtu a Edtu najdete v tématu [co jsou Dtu a Edtu?](sql-database-what-is-a-dtu.md)

### <a name="single-databases"></a>Izolované databáze

|  | **Basic** | **Standard** | **Premium** | 
| :-- | --: | --: | --: | --: |
| Maximální velikost úložiště velikost * | 2 GB | 1 TB | 4 TB  | 
| Maximální počet jednotek Dtu | 5 | 3000 | 4000 | |
||||||

### <a name="elastic-pools"></a>Elastické fondy

| | **Basic** | **Standard** | **Premium** | 
| :-- | --: | --: | --: | --: |
| Maximální velikost úložiště pro databázi *  | 2 GB | 1 TB | 1 TB | 
| Maximální velikost úložiště pro fond * | 156 GB | 4 TB | 4 TB | 
| Maximální počet jednotek Edtu na databázi | 5 | 3000 | 4000 | 
| Maximální počet jednotek Edtu na fond | 1600 | 3000 | 4000 | 
| Maximální počet databází na každý fond | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> \* Velikosti úložiště větší než velikost zahrnutého úložiště jsou ve verzi Preview a účtují se za ně další poplatky. Podrobnosti najdete na stránce s [cenami služby SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). 
>
> \* Ve vrstvě | Premium je více než 1 TB úložiště aktuálně k dispozici v následujících oblastech: Brazílie – Jih, Střední Kanada, Východní Kanada, střed USA, Francie centrální, Německo centrální, Japonsko – východ, Japonsko – Západ, Korejská centrální, – Sever střední USA, Severní Evropa, – jih Střed USA, jihovýchodní Asie, Spojené království – Jih, Spojené království – Západ, East2 USA, západ USA, Virginia verze pro státní správu USA a západní Evropa. Viz [Aktuální omezení pro P11–P15](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 

Podrobnosti o konkrétní úrovně výkonu a možnosti velikosti úložiště k dispozici v tématu [limitů prostředků databáze SQL](sql-database-resource-limits.md).


## <a name="next-steps"></a>Další postup

- Další informace o [jedna databáze prostředků](sql-database-single-database-resources.md).
- Další informace o elastické fondy najdete v tématu [elastické fondy](sql-database-elastic-pool.md).
- Další informace o [předplatného Azure a omezení služby, kvóty a omezení](../azure-subscription-service-limits.md)
* Další informace o [Dtu a Edtu](sql-database-what-is-a-dtu.md).
* Další informace o sledování využití v jednotkách DTU najdete v tématu [monitorování a optimalizace výkonu](sql-database-troubleshoot-performance.md).

