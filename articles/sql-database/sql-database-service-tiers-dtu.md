---
title: Služba Azure SQL Database - DTU | Microsoft Docs
description: Další informace o úrovních služeb pro jeden a zadejte velikost úložiště a úrovně výkonu databáze fondu.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 05/22/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 075e04db27006719ec7d6e08eb2696436d1010f4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34648645"
---
# <a name="dtu-based-purchasing-model-for-azure-sql-database"></a>Na základě DTU nákupní model pro databázi SQL Azure 


[Azure SQL Database](sql-database-technical-overview.md) nabízí dva modely nákupu pro výpočty, úložiště a vstupně-výstupní operace prostředky: nákupní model na základě DTU a nákupní model (preview) na základě vCore. Následující tabulku a graf porovnání a porovnejte tyto dva modely nákupu.

> [!IMPORTANT]
> Na základě vCore nákupní model (preview), najdete v části [na základě vCore nákupní model](sql-database-service-tiers-vcore.md)


|**Nákupní model**|**Popis**|**Nejvhodnější pro**|
|---|---|---|
|Na základě DTU modelu|Tento model je založen na připojené měření výpočty, úložiště a vstupně-výstupní operace prostředky. Úrovně výkonu se vyjadřují v jednotkách transakcí databáze (DTU) pro samostatné databáze a jednotkách transakcí elastické databáze (eDTU) pro elastické fondy. Další informace o Dtu a Edtu najdete v tématu [co jsou Dtu a Edtu](sql-database-what-is-a-dtu.md)?|Nejvhodnější pro zákazníky, kteří chtějí možnosti jednoduchý, předem nakonfigurovaných prostředků.| 
|na základě vCore modelu|Tento model můžete nezávisle škálovat výpočetní a úložnou kapacitu. Také umožňuje použít k získání úsporu nákladů Benefit hybridní Azure pro systém SQL Server.|Nejvhodnější pro zákazníky, kteří hodnota flexibilitu, řízení a průhlednost.|
||||  

![cenový model](./media/sql-database-service-tiers/pricing-model.png)

## <a name="dtu-based-purchasing-model"></a>Na základě DTU nákupní model

Jednotky propustnosti databáze (DTU) představuje kombinaci měření procesoru, paměti, čte a zapisuje. Na základě DTU nákupní model nabízí sadu předkonfigurované sady výpočetní prostředky a zahrnuty úložiště na jednotce různé úrovně výkonu aplikace. Zákazníci, kteří raději jednoduchost sady předkonfigurovaná a pevných plateb každý měsíc, možná modelu na základě DTU vhodnější pro své potřeby. V na základě DTU nákupu modelu, můžete zvolit zákazníci **základní**, **standardní**, a **Premium** úrovních služeb pro obě [jedna databáze](sql-database-single-database-resources.md) a [elastické fondy](sql-database-elastic-pool.md). Úrovně služeb jsou rozlišené pomocí rozsah úrovně výkonu s pevnou velikostí součástí úložiště, pevné dobu uchování záloh a pevné ceny. Všechny úrovně služeb poskytují flexibilitu Změna úrovně výkonu bez výpadků. Izolované databáze i elastické fondy se fakturuje po hodinách podle úrovně služeb a výkonu.

> [!IMPORTANT]
> Instance databáze serveru SQL spravované, aktuálně ve verzi public preview nepodporuje nákupní model na základě DTU. Další informace najdete v tématu [Azure SQL Database spravované Instance](sql-database-managed-instance.md). 

## <a name="choosing-a-service-tier-in-the-dtu-based-purchasing-model"></a>Volba úrovně služby v nákupu modelu na základě DTU

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

## <a name="performance-level-and-storage-size-limits-in-the-dtu-based-purchasing-model"></a>Výkon úroveň a úložiště omezení velikosti v nákupu modelu na základě DTU

Úrovně výkonu se vyjadřují v jednotkách transakcí databáze (DTU) pro samostatné databáze a jednotkách transakcí elastické databáze (eDTU) pro elastické fondy. Další informace o Dtu a Edtu najdete v tématu [co jsou Dtu a Edtu](sql-database-what-is-a-dtu.md)?

### <a name="single-databases"></a>Izolované databáze

||Basic|Standard|Premium|
| :-- | --: | --: | --: | --: |
| Maximální velikost úložiště velikost * | 2 GB | 1 TB | 4 TB  | 
| Maximální počet jednotek Dtu | 5 | 3000 | 4000 | |
||||||

Podrobnosti o konkrétní úrovně výkonu a možnosti velikosti úložiště k dispozici pro izolované databáze najdete v tématu [limitů prostředků na základě DTU databáze SQL pro izolované databáze](sql-database-dtu-resource-limits.md#single-database-storage-sizes-and-performance-levels).

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
> - Velikosti úložiště větší než velikost zahrnutého úložiště jsou ve verzi Preview a účtují se za ně další poplatky. Podrobnosti najdete na stránce s [cenami služby SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). 
> - Více než 1 TB místa v úrovni Premium je k dispozici ve všech oblastech s následující výjimkou: Spojené království – sever, – Západ střední USA, UK South2, východní Čína, USDoDCentral, Německo – střed, indiánský motiv verze pro státní správu USDoDEast, USA, nám verze pro státní správu – jih centrální, Německo – severovýchod, severní Čína, nám verze pro státní správu – Východ. Plánuje se širší rozšíření. V ostatních oblastech je úložiště na úrovni Premium omezeno na 1 TB. Viz [Aktuální omezení pro P11–P15](sql-database-dtu-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 
Podrobnosti o konkrétní úrovně výkonu a možnosti velikosti úložiště k dispozici pro elastické fondy najdete v tématu [limitů prostředků na základě DTU databáze SQL](sql-database-dtu-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).



## <a name="next-steps"></a>Další postup

- Podrobnosti o konkrétní úrovně výkonu a možnosti velikosti úložiště k dispozici v tématu [limitů prostředků na základě DTU databáze SQL](sql-database-dtu-resource-limits.md) a [limitů prostředků na základě vCore SQL Database](sql-database-vcore-resource-limits.md).
- V tématu [nejčastější dotazy k databázi SQL](sql-database-faq.md) odpovědi na nejčastější dotazy.
- Další informace o [předplatného Azure a omezení služby, kvóty a omezení](../azure-subscription-service-limits.md)
