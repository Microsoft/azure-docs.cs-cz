---
title: omezení prostředků vCore – jedna databáze
description: Tato stránka popisuje některé běžné limity vCore prostředků pro izolovanou databázi v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 01/22/2020
ms.openlocfilehash: 267779afc749fccba41935741630a759576d6e77
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359829"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>Omezení prostředků pro izolované databáze pomocí modelu nákupu vCore

Tento článek poskytuje podrobné omezení prostředků pro Azure SQL Database izolované databáze pomocí modelu nákupu vCore.

Omezení nákupních modelů pro DTU pro izolované databáze na serveru SQL Database najdete v tématu [Přehled omezení prostředků na serveru SQL Database](sql-database-resource-limits-database-server.md).

Můžete nastavit úroveň služby, výpočetní velikost a množství úložiště pro jednu databázi pomocí [Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [jazyka Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShellu](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), rozhraní příkazového [řádku Azure](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)nebo [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Pokyny a požadavky pro škálování najdete v tématu [škálování izolované databáze](sql-database-single-database-scale.md).

## <a name="general-purpose---serverless-compute---gen5"></a>Obecné účely – COMPUTE bez serveru – Gen5

[Výpočetní vrstva bez serveru](sql-database-serverless.md) je aktuálně dostupná jenom na Gen5 hardwaru.

### <a name="gen5-compute-generation-part-1"></a>Generování výpočetních prostředků Gen5 (část 1)

|Velikost výpočetního prostředí|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|Gen5|
|Min – max – virtuální jádra|0,5 – 1|0,5 – 2|0,5 – 4|0,75-6|1,0 – 8|
|Minimální – maximální velikost paměti (GB)|2.02-3|2.05 – 6|2.10 – 12|2.25-18|3.00-24|
|Minimální prodleva automatického pozastavení (minuty)|60|60|60|60|60|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Maximální velikost dat (GB)|512|1024|1024|1024|1536|
|Maximální velikost protokolu (GB)|154|307|307|307|461|
|Maximální velikost dat databáze TempDB (GB)|32|64|128|192|256|
|Typ úložiště|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|
|Vstupně-výstupní latence (přibližné)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet datových IOPS *|320|640|1280|1920|2560|
|Maximální rychlost protokolu (MB/s)|3.8|7.5|15|22.5|30|
|Maximální počet souběžných pracovních procesů (požadavků)|75|150|300|450|600|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|
|Počet replik|1|1|1|1|1|
|Více AZ|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Škálování čtení|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* maximální hodnotu velikosti v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Generace COMPUTE Gen5 (část 2)

|Velikost výpočetního prostředí|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|
|Min – max – virtuální jádra|1,25 – 10|1.50-12|1,75 – 14|2,00-16|
|Minimální – maximální velikost paměti (GB)|3,75 – 30|4.50 – 36|5,25 – 42|6.00-48|
|Minimální prodleva automatického pozastavení (minuty)|60|60|60|60|
|Podpora columnstore|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Maximální velikost dat (GB)|1536|3072|3072|3072|
|Maximální velikost protokolu (GB)|461|461|461|922|
|Maximální velikost dat databáze TempDB (GB)|320|384|448|512|
|Typ úložiště|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|
|Vstupně-výstupní latence (přibližné)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet datových IOPS *|3 200|3840|4480|5120|
|Maximální rychlost protokolu (MB/s)|30|30|30|30|
|Maximální počet souběžných pracovních procesů (požadavků)|750|900|1050|1200|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|
|Počet replik|1|1|1|1|
|Více AZ|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Škálování čtení|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* maximální hodnotu velikosti v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="hyperscale---provisioned-compute---gen4"></a>Výpočetní prostředí s podporou škálování na více COMPUTE GEN4 –

### <a name="gen4-compute-generation-part-1"></a>Generování výpočetních prostředků COMPUTE GEN4 – (část 1)

|Úroveň výkonu|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|Generování výpočtů|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|
|virtuální jádra|1|2|3|4|5|6|
|Paměť (GB)|7|14|21|28|35|42|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Hodnota|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Maximální velikost dat (TB)|100 |100 |100 |100 |100 |100|
|Maximální velikost protokolu (TB)|1 |1 |1 |1 |1 |1 |
|Maximální velikost dat databáze TempDB (GB)|32|64|96|128|160|192|
|Typ úložiště| [Poznámka 1](#notes) |[Poznámka 1](#notes)|[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |
|Maximální počet datových IOPS *|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|
|Vstupně-výstupní latence (přibližné)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|
|Maximální počet souběžných pracovních procesů (požadavků)|200|400|600|800|1 000|1200|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|
|Sekundární repliky|0-4|0-4|0-4|0-4|0-4|0-4|
|Více AZ|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|
|Uchování úložiště záloh|7 dní|7 dní|7 dní|7 dní|7 dní|7 dní|
|||

### <a name="gen4-compute-generation-part-2"></a>Generace COMPUTE COMPUTE GEN4 – (část 2)

|Úroveň výkonu|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|Generování výpočtů|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|
|virtuální jádra|7|8|9|10|16|24|
|Paměť (GB)|49|56|63|70|112|159,5|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Hodnota|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Maximální velikost dat (TB)|100 |100 |100 |100 |100 |100 |
|Maximální velikost protokolu (TB)|1 |1 |1 |1 |1 |1 |
|Maximální velikost dat databáze TempDB (GB)|224|256|288|320|512|768|
|Typ úložiště| [Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |
|Maximální počet datových IOPS *|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|
|Vstupně-výstupní latence (přibližné)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|
|Maximální počet souběžných pracovních procesů (požadavků)|1400|1600|1800|2000|3 200|4800|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|
|Sekundární repliky|0-4|0-4|0-4|0-4|0-4|0-4|
|Více AZ|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|
|Uchování úložiště záloh|7 dní|7 dní|7 dní|7 dní|7 dní|7 dní|
|||

\* maximální hodnotu velikosti v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="hyperscale---provisioned-compute---gen5"></a>Výpočetní prostředí s podporou škálování na více Gen5

### <a name="gen5-compute-generation-part-1"></a>Generování výpočetních prostředků Gen5 (část 1)

|Úroveň výkonu|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|virtuální jádra|2|4|6|8|10|12|14|
|Paměť (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Hodnota|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Maximální velikost dat (TB)|100 |100 |100 |100 |100 |100 |100|
|Maximální velikost protokolu (TB)|1 |1 |1 |1 |1 |1 |1 |
|Maximální velikost dat databáze TempDB (GB)|64|128|192|256|320|384|448|
|Typ úložiště| [Poznámka 1](#notes) |[Poznámka 1](#notes)|[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |
|Maximální počet datových IOPS *|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|
|Vstupně-výstupní latence (přibližné)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|
|Maximální počet souběžných pracovních procesů (požadavků)|200|400|600|800|1 000|1200|1400|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Sekundární repliky|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Více AZ|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Uchování úložiště záloh|7 dní|7 dní|7 dní|7 dní|7 dní|7 dní|7 dní|
|||

\* maximální hodnotu velikosti v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Generace COMPUTE Gen5 (část 2)

|Úroveň výkonu|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|virtuální jádra|16|18|20|24|32|40|80|
|Paměť (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Hodnota|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Maximální velikost dat (TB)|100 |100 |100 |100 |100 |100 |100 |
|Maximální velikost protokolu (TB)|1 |1 |1 |1 |1 |1 |1 |
|Maximální velikost dat databáze TempDB (GB)|512|576|640|768|1024|1280|2560|
|Typ úložiště| [Poznámka 1](#notes) |[Poznámka 1](#notes)|[Poznámka 1](#notes)|[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |
|Maximální počet datových IOPS *|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|
|Vstupně-výstupní latence (přibližné)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|
|Maximální počet souběžných pracovních procesů (požadavků)|1600|1800|2000|2400|3 200|4 000|8000|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Sekundární repliky|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Více AZ|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Uchování úložiště záloh|7 dní|7 dní|7 dní|7 dní|7 dní|7 dní|7 dní|
|||

\* maximální hodnotu velikosti v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](sql-database-resource-limits-database-server.md#resource-governance).

#### <a name="notes"></a>Poznámky:

**Poznámka 1**: Škálovatelná architektura je Vícevrstvá architektura se samostatnými výpočetními a úložnými součástmi: [Architektura vrstvy služeb s vlastním škálováním](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)

**Poznámka 2**: Vícevrstvá architektura škálovatelné architektury má ukládání do mezipaměti na více úrovních. Platnost IOPS bude záviset na zatížení.

**Poznámka 3**: latence je 1-2 MS pro data v mezipaměti založené na SSD RBPEX na výpočetních replikách, které ukládá do mezipaměti nejčastěji používané datové stránky. Vyšší latence pro data získaná ze stránkových serverů.

## <a name="general-purpose---provisioned-compute---gen4"></a>Pro obecné účely zřízené COMPUTE – COMPUTE GEN4 –

> [!IMPORTANT]
> Nové databáze COMPUTE GEN4 – již nejsou podporovány v oblastech Austrálie – východ a Brazílie – jih.

### <a name="gen4-compute-generation-part-1"></a>Generování výpočetních prostředků COMPUTE GEN4 – (část 1)

|Velikost výpočetního prostředí|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Generování výpočtů|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|
|virtuální jádra|1|2|3|4|5|6|
|Paměť (GB)|7|14|21|28|35|42|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Maximální velikost dat (GB)|1024|1024|1536|1536|1536|3072|
|Maximální velikost protokolu (GB)|307|307|461|461|461|922|
|Maximální velikost dat databáze TempDB (GB)|32|64|96|128|160|192|
|Typ úložiště|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|
|Vstupně-výstupní latence (přibližné)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet datových IOPS *|320|640|960|1280|1600|1920|
|Maximální rychlost protokolu (MB/s)|3.75|7.5|11.25|15|18,75|22.5|
|Maximální počet souběžných pracovních procesů (požadavků)|200|400|600|800|1 000|1200|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|
|Počet replik|1|1|1|1|1|1|
|Více AZ|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Škálování čtení|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* maximální hodnotu velikosti v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Generace COMPUTE COMPUTE GEN4 – (část 2)

|Velikost výpočetního prostředí|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Generování výpočtů|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|
|virtuální jádra|7|8|9|10|16|24|
|Paměť (GB)|49|56|63|70|112|159,5|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Maximální velikost dat (GB)|3072|3072|3072|3072|4 096|4 096|
|Maximální velikost protokolu (GB)|922|922|922|922|1229|1229|
|Maximální velikost dat databáze TempDB (GB)|224|256|288|320|512|768|
|Typ úložiště|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|
|Vstupně-výstupní latence (přibližné)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)
|Maximální počet datových IOPS *|2240|2560|2880|3 200|5120|7680|
|Maximální rychlost protokolu (MB/s)|26,3|30|30|30|30|30|
|Maximální počet souběžných pracovních procesů (požadavků)|1400|1600|1800|2000|3 200|4800|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|
|Počet replik|1|1|1|1|1|1|
|Více AZ|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Škálování čtení|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* maximální hodnotu velikosti v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---gen5"></a>Pro obecné účely zřízené COMPUTE – Gen5

### <a name="gen5-compute-generation-part-1"></a>Generování výpočetních prostředků Gen5 (část 1)

|Velikost výpočetního prostředí|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|virtuální jádra|2|4|6|8|10|12|14|
|Paměť (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Maximální velikost dat (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maximální velikost protokolu (GB)|307|307|461|461|461|922|922|
|Maximální velikost dat databáze TempDB (GB)|64|128|192|256|320|384|384|
|Typ úložiště|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|
|Vstupně-výstupní latence (přibližné)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet datových IOPS *|640|1280|1920|2560|3 200|3840|4480|
|Maximální rychlost protokolu (MB/s)|7.5|15|22.5|30|30|30|30|
|Maximální počet souběžných pracovních procesů (požadavků)|200|400|600|800|1 000|1200|1400|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Počet replik|1|1|1|1|1|1|1|
|Více AZ|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Škálování čtení|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* maximální hodnotu velikosti v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Generace COMPUTE Gen5 (část 2)

|Velikost výpočetního prostředí|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|virtuální jádra|16|18|20|24|32|40|80|
|Paměť (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Maximální velikost dat (GB)|3072|3072|3072|4 096|4 096|4 096|4 096|
|Maximální velikost protokolu (GB)|922|922|922|1229|1229|1229|1229|
|Maximální velikost dat databáze TempDB (GB)|512|576|640|768|1024|1280|2560|
|Typ úložiště|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|
|Vstupně-výstupní latence (přibližné)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet datových IOPS *|5120|5760|6 400|7680|10240|12800|25600|
|Maximální rychlost protokolu (MB/s)|30|30|30|30|30|30|30|
|Maximální počet souběžných pracovních procesů (požadavků)|1600|1800|2000|2400|3 200|4 000|8000|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Počet replik|1|1|1|1|1|1|1|
|Více AZ|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Škálování čtení|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* maximální hodnotu velikosti v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Pro obecné účely zřízené COMPUTE-Fsv2-Series

### <a name="fsv2-series-compute-generation-preview"></a>Generování výpočetních prostředků Fsv2-Series (Preview)

|Velikost výpočetního prostředí|GP_Fsv2_72|
|:--- | --: |
|Generování výpočtů|Řada Fsv2|
|virtuální jádra|72|
|Paměť (GB)|136,2|
|Podpora columnstore|Ano|
|Úložiště OLTP v paměti (GB)|NEUŽÍVÁ SE.|
|Maximální velikost dat (GB)|4 096|
|Maximální velikost protokolu (GB)|1024|
|Maximální velikost dat databáze TempDB (GB)|333|
|Typ úložiště|Vzdálený disk SSD|
|Vstupně-výstupní latence (přibližné)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet datových IOPS *|12,800|
|Maximální rychlost protokolu (MB/s)|30|
|Maximální počet souběžných pracovních procesů (požadavků)|3600|
|Maximální počet souběžných přihlášení|3600|
|Maximální počet souběžných relací|30,000|
|Počet replik|1|
|Více AZ|NEUŽÍVÁ SE.|
|Škálování čtení|NEUŽÍVÁ SE.|
|Zahrnuté úložiště zálohování|velikost databáze 1X|

\* maximální hodnotu velikosti v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen4"></a>Uživatelsky důležité – zřízené COMPUTE – COMPUTE GEN4 –

> [!IMPORTANT]
> Nové databáze COMPUTE GEN4 – již nejsou podporovány v oblastech Austrálie – východ a Brazílie – jih.

### <a name="gen4-compute-generation-part-1"></a>Generování výpočetních prostředků COMPUTE GEN4 – (část 1)

|Velikost výpočetního prostředí|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Generování výpočtů|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|
|virtuální jádra|1|2|3|4|5|6|
|Paměť (GB)|7|14|21|28|35|42|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|1|2|3|4|5|6|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Maximální velikost dat (GB)|1024|1024|1024|1024|1024|1024|
|Maximální velikost protokolu (GB)|307|307|307|307|307|307|
|Maximální velikost dat databáze TempDB (GB)|32|64|96|128|160|192|
|Vstupně-výstupní latence (přibližné)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální počet datových IOPS *|4,000|8 000|12 000|16,000|20,000|24,000|
|Maximální rychlost protokolu (MB/s)|8|16|24|32|40|48|
|Maximální počet souběžných pracovních procesů (požadavků)|200|400|600|800|1 000|1200|
|Maximální počet souběžných přihlášení|200|400|600|800|1 000|1200|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|
|Počet replik|4|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* maximální hodnotu velikosti v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Generace COMPUTE COMPUTE GEN4 – (část 2)

|Velikost výpočetního prostředí|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generování výpočtů|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|
|virtuální jádra|7|8|9|10|16|24|
|Paměť (GB)|49|56|63|70|112|159,5|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|7|8|9.5|11|20|36|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Maximální velikost dat (GB)|1024|1024|1024|1024|1024|1024|
|Maximální velikost protokolu (GB)|307|307|307|307|307|307|
|Maximální velikost dat databáze TempDB (GB)|224|256|288|320|512|768|
|Vstupně-výstupní latence (přibližné)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální počet datových IOPS |28 000|32,000|36 000|40,000|64,000|76 800|
|Maximální rychlost protokolu (MB/s)|56|64|64|64|64|64|
|Maximální počet souběžných pracovních procesů (požadavků)|1400|1600|1800|2000|3 200|4800|
|Maximální počet souběžných přihlášení (požadavků)|1400|1600|1800|2000|3 200|4800|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|
|Počet replik|4|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* maximální hodnotu velikosti v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen5"></a>Uživatelsky důležité – zřízené COMPUTE – Gen5

### <a name="gen5-compute-generation-part-1"></a>Generování výpočetních prostředků Gen5 (část 1)

|Velikost výpočetního prostředí|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|virtuální jádra|2|4|6|8|10|12|14|
|Paměť (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|1,57|3,14|4,71|6,28|8,65|11,02|13,39|
|Maximální velikost dat (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maximální velikost protokolu (GB)|307|307|461|461|461|922|922|
|Maximální velikost dat databáze TempDB (GB)|64|128|192|256|320|384|448|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Vstupně-výstupní latence (přibližné)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální počet datových IOPS *|8000|16,000|24,000|32,000|40,000|48 000|56 000|
|Maximální rychlost protokolu (MB/s)|24|48|72|96|96|96|96|
|Maximální počet souběžných pracovních procesů (požadavků)|200|400|600|800|1 000|1200|1400|
|Maximální počet souběžných přihlášení|200|400|600|800|1 000|1200|1400|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Počet replik|4|4|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* maximální hodnotu velikosti v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](sql-database-resource-limits-database-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Generace COMPUTE Gen5 (část 2)

|Velikost výpočetního prostředí|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|virtuální jádra|16|18|20|24|32|40|80|
|Paměť (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|15,77|18,14|20,51|25,25|37,94|52,23|131,64|
|Maximální velikost dat (GB)|3072|3072|3072|4 096|4 096|4 096|4 096|
|Maximální velikost protokolu (GB)|922|922|922|1229|1229|1229|1229|
|Maximální velikost dat databáze TempDB (GB)|512|576|640|768|1024|1280|2560|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Vstupně-výstupní latence (přibližné)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální počet datových IOPS *|64,000|72 000|80,000|96 000|128 000|160 000|204 800|
|Maximální rychlost protokolu (MB/s)|96|96|96|96|96|96|96|
|Maximální počet souběžných pracovních procesů (požadavků)|1600|1800|2000|2400|3 200|4 000|8000|
|Maximální počet souběžných přihlášení|1600|1800|2000|2400|3 200|4 000|8000|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Počet replik|4|4|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* maximální hodnotu velikosti v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](sql-database-resource-limits-database-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---m-series"></a>Podnikové kritické pro výpočetní prostředí-M-Series

### <a name="m-series-compute-generation-preview"></a>Generování výpočetních prostředků řady M-Series (Preview)

|Velikost výpočetního prostředí|BC_M_128|
|:--- | --: |
|Generování výpočtů|Řada M|
|virtuální jádra|128|
|Paměť (GB)|3767,1|
|Podpora columnstore|Ano|
|Úložiště OLTP v paměti (GB)|1768|
|Maximální velikost dat (GB)|4 096|
|Maximální velikost protokolu (GB)|2 048|
|Maximální velikost dat databáze TempDB (GB)|4 096|
|Typ úložiště|Místní disk SSD|
|Vstupně-výstupní latence (přibližné)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální počet datových IOPS *|160 000|
|Maximální rychlost protokolu (MB/s)|264|
|Maximální počet souběžných pracovních procesů (požadavků)|12,800|
|Maximální počet souběžných přihlášení|12,800|
|Maximální počet souběžných relací|30000|
|Počet replik|4|
|Více AZ|Ano|
|Škálování čtení|Ano|
|Zahrnuté úložiště zálohování|velikost databáze 1X|

\* maximální hodnotu velikosti v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](sql-database-resource-limits-database-server.md#resource-governance).

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [Správa prostoru souborů v Azure SQL Database](sql-database-file-space-management.md).

## <a name="next-steps"></a>Další kroky

- Omezení prostředků DTU pro jednu databázi najdete v tématu [omezení prostředků pro izolované databáze pomocí modelu nákupu DTU](sql-database-dtu-resource-limits-single-databases.md) .
- Omezení prostředků vCore pro elastické fondy najdete v tématu [omezení prostředků pro elastické fondy pomocí modelu nákupu Vcore](sql-database-vcore-resource-limits-elastic-pools.md) .
- Omezení prostředků DTU pro elastické fondy najdete v tématu [omezení prostředků pro elastické fondy pomocí modelu nákupu DTU](sql-database-dtu-resource-limits-elastic-pools.md) .
- Omezení prostředků pro spravované instance najdete v tématu [omezení prostředků spravované instance](sql-database-managed-instance-resource-limits.md).
- Informace o obecných omezeních Azure najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Informace o omezeních prostředků na databázovém serveru najdete v tématu [Přehled omezení prostředků na serveru SQL Database](sql-database-resource-limits-database-server.md) , kde najdete informace o omezeních na úrovni serveru a předplatného.
