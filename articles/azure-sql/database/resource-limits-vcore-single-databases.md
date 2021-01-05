---
title: Limity prostředků virtuálních jader izolované databáze
description: Tato stránka popisuje některé běžné limity vCore prostředků pro izolovanou databázi v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/15/2020
ms.openlocfilehash: 4ffe663c1a1651891af5f6e65ee231cbe3e8d650
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97882288"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>Limity prostředků pro jednoúčelové databáze využívající nákupní model založený na virtuálních jádrech
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Tento článek poskytuje podrobné omezení prostředků pro izolované databáze v Azure SQL Database pomocí modelu nákupu vCore.

Omezení nákupních modelů pro DTU pro jednotlivé databáze na serveru najdete v tématu [Přehled omezení prostředků na serveru](resource-limits-logical-server.md).

Můžete nastavit úroveň služby, výpočetní velikost (cíl služby) a množství úložiště pro izolovanou databázi pomocí [Azure Portal](single-database-manage.md#the-azure-portal), [jazyka Transact-SQL](single-database-manage.md#transact-sql-t-sql), [PowerShellu](single-database-manage.md#powershell), rozhraní příkazového [řádku Azure](single-database-manage.md#the-azure-cli)nebo [REST API](single-database-manage.md#rest-api).

> [!IMPORTANT]
> Pokyny a požadavky pro škálování najdete v tématu [škálování izolované databáze](single-database-scale.md).

## <a name="general-purpose---serverless-compute---gen5"></a>Obecné účely – COMPUTE bez serveru – Gen5

[Výpočetní vrstva bez serveru](serverless-tier-overview.md) je aktuálně dostupná jenom na Gen5 hardwaru.

### <a name="gen5-compute-generation-part-1"></a>Generování výpočetních prostředků Gen5 (část 1)

|Velikost výpočetních prostředků (cíl služby)|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|Gen5|
|Min – max – virtuální jádra|0.5-1|0.5-2|0,5 – 4|0,75-6|1.0-8|
|Minimální – maximální velikost paměti (GB)|2.02-3|2.05 – 6|2.10 – 12|2.25-18|3.00-24|
|Minimální prodleva automatického pozastavení (minuty) pro maximum|60-10080|60-10080|60-10080|60-10080|60-10080|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|N/A|N/A|N/A|N/A|N/A|
|Maximální velikost dat (GB)|512|1024|1024|1024|1536|
|Maximální velikost protokolu (GB)|154|307|307|307|461|
|Maximální velikost dat databáze TempDB (GB)|32|64|128|192|256|
|Typ úložiště|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|
|Latence v/v (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet datových IOPS *|320|640|1280|1920|2560|
|Maximální rychlost protokolu (MB/s)|4.5|9|18|27|36|
|Maximální počet souběžných pracovních procesů (požadavků)|75|150|300|450|600|
|Maximální počet souběžných relací|30 000|30 000|30 000|30 000|30 000|
|Počet replik|1|1|1|1|1|
|Více AZ|N/A|N/A|N/A|N/A|N/A|
|Škálování čtení|N/A|N/A|N/A|N/A|N/A|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* Maximální hodnota pro velikost v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Generace COMPUTE Gen5 (část 2)

|Velikost výpočetních prostředků (cíl služby)|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|
|Min – max – virtuální jádra|1,25 – 10|1.50-12|1,75 – 14|2,00-16|
|Minimální – maximální velikost paměti (GB)|3,75 – 30|4.50 – 36|5,25 – 42|6.00-48|
|Minimální prodleva automatického pozastavení (minuty) pro maximum|60-10080|60-10080|60-10080|60-10080|
|Podpora columnstore|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|N/A|N/A|N/A|N/A|
|Maximální velikost dat (GB)|1536|3072|3072|3072|
|Maximální velikost protokolu (GB)|461|461|461|922|
|Maximální velikost dat databáze TempDB (GB)|320|384|448|512|
|Typ úložiště|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|
|Latence v/v (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet datových IOPS *|3200|3840|4480|5120|
|Maximální rychlost protokolu (MB/s)|36|36|36|36|
|Maximální počet souběžných pracovních procesů (požadavků)|750|900|1050|1200|
|Maximální počet souběžných relací|30 000|30 000|30 000|30 000|
|Počet replik|1|1|1|1|
|Více AZ|N/A|N/A|N/A|N/A|
|Škálování čtení|N/A|N/A|N/A|N/A|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* Maximální hodnota pro velikost v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-3"></a>Generování výpočtů Gen5 (část 3)

|Velikost výpočetních prostředků (cíl služby)|GP_S_Gen5_18|GP_S_Gen5_20|GP_S_Gen5_24|GP_S_Gen5_32|GP_S_Gen5_40|
|:--- | --: |--: |--: |--: |--:|
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|Gen5|
|Min – max – virtuální jádra|2.25-18|2,5 – 20|3–24|4-32|5-40|
|Minimální – maximální velikost paměti (GB)|6.75-54|7,5 – 60|9-72|12-96|15-120|
|Minimální prodleva automatického pozastavení (minuty) pro maximum|60-10080|60-10080|60-10080|60-10080|60-10080|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|N/A|N/A|N/A|N/A|N/A|
|Maximální velikost dat (GB)|3072|3072|4 096|4 096|4 096|
|Maximální velikost protokolu (GB)|922|922|1024|1024|1024|
|Maximální velikost dat databáze TempDB (GB)|576|640|768|1024|1280|
|Typ úložiště|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|
|Latence v/v (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet datových IOPS *|5760|6400|7680|10240|12800|
|Maximální rychlost protokolu (MB/s)|36|36|36|36|36|
|Maximální počet souběžných pracovních procesů (požadavků)|1350|1 500|1800|2400|3000|
|Maximální počet souběžných relací|30 000|30 000|30 000|30 000|30 000|
|Počet replik|1|1|1|1|1|
|Více AZ|N/A|N/A|N/A|N/A|N/A|
|Škálování čtení|N/A|N/A|N/A|N/A|N/A|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* Maximální hodnota pro velikost v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](resource-limits-logical-server.md#resource-governance).


## <a name="hyperscale---provisioned-compute---gen4"></a>Výpočetní prostředí s podporou škálování na více COMPUTE GEN4 –

### <a name="gen4-compute-generation-part-1"></a>Generování výpočetních prostředků COMPUTE GEN4 – (část 1)

|Velikost výpočetních prostředků (cíl služby)|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|Generování výpočtů|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|
|Virtuální jádra|1|2|3|4|5|6|
|Paměť (GB)|7|14|21|28|35|42|
|[RBPEX](service-tier-hyperscale.md#compute) Hodnota|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximální velikost dat (TB)|100 |100 |100 |100 |100 |100|
|Maximální velikost protokolu (TB)|Unlimited |Unlimited |Unlimited |Unlimited |Unlimited |Unlimited |
|Maximální velikost dat databáze TempDB (GB)|32|64|96|128|160|192|
|Typ úložiště| [Poznámka 1](#notes) |[Poznámka 1](#notes)|[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |
|Maximální místní SSD IOPS *|4000 |8000 |12000 |16000 |20000 |24000 |
|Maximální rychlost protokolu (MB/s)|100 |100 |100 |100 |100 |100 |
|Latence v/v (přibližná)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|
|Maximální počet souběžných pracovních procesů (požadavků)|200|400|600|800|1000|1200|
|Maximální počet souběžných relací|30 000|30 000|30 000|30 000|30 000|30 000|
|Sekundární repliky|0-4|0-4|0-4|0-4|0-4|0-4|
|Více AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|
|Uchování úložiště záloh|7 dní|7 dní|7 dní|7 dní|7 dní|7 dní|
|||

\* Kromě místní jednotky SSD v/v budou úlohy používat v/v vzdálené [stránky](service-tier-hyperscale.md#page-server) . Platnost IOPS bude záviset na zatížení. Podrobnosti najdete v tématu zásady [správy vstupně-výstupních operací](resource-limits-logical-server.md#resource-governance)a [data ve statistice využití prostředků](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

### <a name="gen4-compute-generation-part-2"></a>Generace COMPUTE COMPUTE GEN4 – (část 2)

|Velikost výpočetních prostředků (cíl služby)|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|Generování výpočtů|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|
|Virtuální jádra|7|8|9|10|16|24|
|Paměť (GB)|49|56|63|70|112|159,5|
|[RBPEX](service-tier-hyperscale.md#compute) Hodnota|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximální velikost dat (TB)|100 |100 |100 |100 |100 |100 |
|Maximální velikost protokolu (TB)|Unlimited |Unlimited |Unlimited |Unlimited |Unlimited |Unlimited |
|Maximální velikost dat databáze TempDB (GB)|224|256|288|320|512|768|
|Typ úložiště| [Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |
|Maximální místní SSD IOPS *|28000 |32000 |36000 |40000 |64000 |76800 |
|Maximální rychlost protokolu (MB/s)|100 |100 |100 |100 |100 |100 |
|Latence v/v (přibližná)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|
|Maximální počet souběžných pracovních procesů (požadavků)|1400|1600|1800|2000|3200|4800|
|Maximální počet souběžných relací|30 000|30 000|30 000|30 000|30 000|30 000|
|Sekundární repliky|0-4|0-4|0-4|0-4|0-4|0-4|
|Více AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|
|Uchování úložiště záloh|7 dní|7 dní|7 dní|7 dní|7 dní|7 dní|
|||

\* Kromě místní jednotky SSD v/v budou úlohy používat v/v vzdálené [stránky](service-tier-hyperscale.md#page-server) . Platnost IOPS bude záviset na zatížení. Podrobnosti najdete v tématu zásady [správy vstupně-výstupních operací](resource-limits-logical-server.md#resource-governance)a [data ve statistice využití prostředků](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

## <a name="hyperscale---provisioned-compute---gen5"></a>Výpočetní prostředí s podporou škálování na více Gen5

### <a name="gen5-compute-generation-part-1"></a>Generování výpočetních prostředků Gen5 (část 1)

|Velikost výpočetních prostředků (cíl služby)|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuální jádra|2|4|6|8|10|12|14|
|Paměť (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|[RBPEX](service-tier-hyperscale.md#compute) Hodnota|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximální velikost dat (TB)|100 |100 |100 |100 |100 |100 |100|
|Maximální velikost protokolu (TB)|Unlimited |Unlimited |Unlimited |Unlimited |Unlimited |Unlimited |Unlimited |
|Maximální velikost dat databáze TempDB (GB)|64|128|192|256|320|384|448|
|Typ úložiště| [Poznámka 1](#notes) |[Poznámka 1](#notes)|[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |
|Maximální místní SSD IOPS *|8000 |16000 |24000 |32000 |40000 |48000 |56000 |
|Maximální rychlost protokolu (MB/s)|100 |100 |100 |100 |100 |100 |100 |
|Latence v/v (přibližná)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|
|Maximální počet souběžných pracovních procesů (požadavků)|200|400|600|800|1000|1200|1400|
|Maximální počet souběžných relací|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Sekundární repliky|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Více AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Uchování úložiště záloh|7 dní|7 dní|7 dní|7 dní|7 dní|7 dní|7 dní|
|||

\* Kromě místní jednotky SSD v/v budou úlohy používat v/v vzdálené [stránky](service-tier-hyperscale.md#page-server) . Platnost IOPS bude záviset na zatížení. Podrobnosti najdete v tématu zásady [správy vstupně-výstupních operací](resource-limits-logical-server.md#resource-governance)a [data ve statistice využití prostředků](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

### <a name="gen5-compute-generation-part-2"></a>Generace COMPUTE Gen5 (část 2)

|Velikost výpočetních prostředků (cíl služby)|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuální jádra|16|18|20|24|32|40|80|
|Paměť (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|[RBPEX](service-tier-hyperscale.md#compute) Hodnota|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|Paměť 3X|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximální velikost dat (TB)|100 |100 |100 |100 |100 |100 |100 |
|Maximální velikost protokolu (TB)|Unlimited |Unlimited |Unlimited |Unlimited |Unlimited |Unlimited |Unlimited |
|Maximální velikost dat databáze TempDB (GB)|512|576|640|768|1024|1280|2560|
|Typ úložiště| [Poznámka 1](#notes) |[Poznámka 1](#notes)|[Poznámka 1](#notes)|[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |
|Maximální místní SSD IOPS *|64000 |72000 |80000 |96000 |160000 |192000 |204800 |
|Maximální rychlost protokolu (MB/s)|100 |100 |100 |100 |100 |100 |100 |
|Latence v/v (přibližná)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|
|Maximální počet souběžných pracovních procesů (požadavků)|1600|1800|2000|2400|3200|4000|8000|
|Maximální počet souběžných relací|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Sekundární repliky|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Více AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Uchování úložiště záloh|7 dní|7 dní|7 dní|7 dní|7 dní|7 dní|7 dní|
|||

\* Kromě místní jednotky SSD v/v budou úlohy používat v/v vzdálené [stránky](service-tier-hyperscale.md#page-server) . Platnost IOPS bude záviset na zatížení. Podrobnosti najdete v tématu zásady [správy vstupně-výstupních operací](resource-limits-logical-server.md#resource-governance)a [data ve statistice využití prostředků](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

#### <a name="notes"></a>Poznámky

**Poznámka 1**: Škálovatelná architektura je Vícevrstvá architektura se samostatnými výpočetními a úložnými součástmi: [Architektura vrstvy služeb s vlastním škálováním](service-tier-hyperscale.md#distributed-functions-architecture)

**Poznámka 2**: latence je 1-2 MS pro data na místní sadě prostředků SSD služby COMPUTE, která ukládá do mezipaměti nejčastěji používané datové stránky. Vyšší latence pro data získaná ze stránkových serverů.

## <a name="general-purpose---provisioned-compute---gen4"></a>Pro obecné účely zřízené COMPUTE – COMPUTE GEN4 –

> [!IMPORTANT]
> Nové databáze COMPUTE GEN4 – již nejsou podporovány v oblastech Austrálie – východ a Brazílie – jih.

### <a name="gen4-compute-generation-part-1"></a>Generování výpočetních prostředků COMPUTE GEN4 – (část 1)

|Velikost výpočetních prostředků (cíl služby)|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Generování výpočtů|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|
|Virtuální jádra|1|2|3|4|5|6|
|Paměť (GB)|7|14|21|28|35|42|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximální velikost dat (GB)|1024|1024|1536|1536|1536|3072|
|Maximální velikost protokolu (GB)|307|307|461|461|461|922|
|Maximální velikost dat databáze TempDB (GB)|32|64|96|128|160|192|
|Typ úložiště|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|
|Latence v/v (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet datových IOPS *|320|640|960|1280|1600|1920|
|Maximální rychlost protokolu (MB/s)|4.5|9|13,5|18|22,5|27|
|Maximální počet souběžných pracovních procesů (požadavků)|200|400|600|800|1000|1200|
|Maximální počet souběžných relací|30 000|30 000|30 000|30 000|30 000|30 000|
|Počet replik|1|1|1|1|1|1|
|Více AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Škálování čtení|N/A|N/A|N/A|N/A|N/A|N/A|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* Maximální hodnota pro velikost v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](resource-limits-logical-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Generace COMPUTE COMPUTE GEN4 – (část 2)

|Velikost výpočetních prostředků (cíl služby)|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Generování výpočtů|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|
|Virtuální jádra|7|8|9|10|16|24|
|Paměť (GB)|49|56|63|70|112|159,5|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximální velikost dat (GB)|3072|3072|3072|3072|4 096|4 096|
|Maximální velikost protokolu (GB)|922|922|922|922|1229|1229|
|Maximální velikost dat databáze TempDB (GB)|224|256|288|320|512|768|
|Typ úložiště|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|
|Latence v/v (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)
|Maximální počet datových IOPS *|2240|2560|2880|3200|5120|7680|
|Maximální rychlost protokolu (MB/s)|31,5|36|36|36|36|36|
|Maximální počet souběžných pracovních procesů (požadavků)|1400|1600|1800|2000|3200|4800|
|Maximální počet souběžných relací|30 000|30 000|30 000|30 000|30 000|30 000|
|Počet replik|1|1|1|1|1|1|
|Více AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Škálování čtení|N/A|N/A|N/A|N/A|N/A|N/A|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* Maximální hodnota pro velikost v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](resource-limits-logical-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---gen5"></a>Pro obecné účely zřízené COMPUTE – Gen5

### <a name="gen5-compute-generation-part-1"></a>Generování výpočetních prostředků Gen5 (část 1)

|Velikost výpočetních prostředků (cíl služby)|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuální jádra|2|4|6|8|10|12|14|
|Paměť (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximální velikost dat (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maximální velikost protokolu (GB)|307|307|461|461|461|922|922|
|Maximální velikost dat databáze TempDB (GB)|64|128|192|256|320|384|384|
|Typ úložiště|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|
|Latence v/v (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet datových IOPS *|640|1280|1920|2560|3200|3840|4480|
|Maximální rychlost protokolu (MB/s)|9|18|27|36|36|36|36|
|Maximální počet souběžných pracovních procesů (požadavků)|200|400|600|800|1000|1200|1400|
|Maximální počet souběžných relací|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Počet replik|1|1|1|1|1|1|1|
|Více AZ|[K dispozici ve verzi Preview](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[K dispozici ve verzi Preview](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[K dispozici ve verzi Preview](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[K dispozici ve verzi Preview](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[K dispozici ve verzi Preview](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[K dispozici ve verzi Preview](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[K dispozici ve verzi Preview](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|
|Škálování čtení|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* Maximální hodnota pro velikost v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Generace COMPUTE Gen5 (část 2)

|Velikost výpočetních prostředků (cíl služby)|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuální jádra|16|18|20|24|32|40|80|
|Paměť (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximální velikost dat (GB)|3072|3072|3072|4 096|4 096|4 096|4 096|
|Maximální velikost protokolu (GB)|922|922|922|1024|1024|1024|1024|
|Maximální velikost dat databáze TempDB (GB)|512|576|640|768|1024|1280|2560|
|Typ úložiště|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|
|Latence v/v (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet datových IOPS *|5120|5760|6400|7680|10240|12800|12800|
|Maximální rychlost protokolu (MB/s)|36|36|36|36|36|36|36|
|Maximální počet souběžných pracovních procesů (požadavků)|1600|1800|2000|2400|3200|4000|8000|
|Maximální počet souběžných relací|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Počet replik|1|1|1|1|1|1|1|
|Více AZ|[K dispozici ve verzi Preview](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[K dispozici ve verzi Preview](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[K dispozici ve verzi Preview](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[K dispozici ve verzi Preview](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[K dispozici ve verzi Preview](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[K dispozici ve verzi Preview](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|[K dispozici ve verzi Preview](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)|
|Škálování čtení|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* Maximální hodnota pro velikost v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](resource-limits-logical-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Pro obecné účely zřízené COMPUTE-Fsv2-Series

### <a name="fsv2-series-compute-generation-part-1"></a>Výpočet výpočetní úrovně Fsv2-Series (část 1)

|Velikost výpočetních prostředků (cíl služby)|GP_Fsv2_8|GP_Fsv2_10|GP_Fsv2_12|GP_Fsv2_14| GP_Fsv2_16|
|:---| ---:|---:|---:|---:|---:|
|Generování výpočtů|Řada Fsv2|Řada Fsv2|Řada Fsv2|Řada Fsv2|Řada Fsv2|
|Virtuální jádra|8|10|12|14|16|
|Paměť (GB)|15,1|18,9|22,7|26,5|30,2|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|N/A|N/A|N/A|N/A|N/A|
|Maximální velikost dat (GB)|1024|1024|1024|1024|1536|
|Maximální velikost protokolu (GB)|336|336|336|336|512|
|Maximální velikost dat databáze TempDB (GB)|333|333|333|333|333|
|Typ úložiště|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|
|Latence v/v (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet datových IOPS *|2560|3200|3840|4480|5120|
|Maximální rychlost protokolu (MB/s)|36|36|36|36|36|
|Maximální počet souběžných pracovních procesů (požadavků)|400|500|600|700|800|
|Maximální počet souběžných přihlášení|800|1000|1200|1400|1600|
|Maximální počet souběžných relací|30 000|30 000|30 000|30 000|30 000|
|Počet replik|1|1|1|1|1|
|Více AZ|N/A|N/A|N/A|N/A|N/A|
|Škálování čtení|N/A|N/A|N/A|N/A|N/A|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* Maximální hodnota pro velikost v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](resource-limits-logical-server.md#resource-governance).

### <a name="fsv2-series-compute-generation-part-2"></a>Generace COMPUTE Fsv2-Series (část 2)

|Velikost výpočetních prostředků (cíl služby)|GP_Fsv2_18|GP_Fsv2_20|GP_Fsv2_24|GP_Fsv2_32| GP_Fsv2_36|GP_Fsv2_72|
|:---| ---:|---:|---:|---:|---:|---:|
|Generování výpočtů|Řada Fsv2|Řada Fsv2|Řada Fsv2|Řada Fsv2|Řada Fsv2|Řada Fsv2|
|Virtuální jádra|18|20|24|32|36|72|
|Paměť (GB)|34,0|37,8|45,4|60,5|68,0|136,0|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximální velikost dat (GB)|1536|1536|1536|3072|3072|4 096|
|Maximální velikost protokolu (GB)|512|512|512|1024|1024|1024|
|Maximální velikost dat databáze TempDB (GB)|83,25|92,5|111|148|166,5|333|
|Typ úložiště|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|
|Latence v/v (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet datových IOPS *|5760|6400|7680|10240|11520|23040|
|Maximální rychlost protokolu (MB/s)|36|36|36|36|36|36|
|Maximální počet souběžných pracovních procesů (požadavků)|900|1000|1200|1600|1800|3600|
|Maximální počet souběžných přihlášení|1800|2000|2400|3200|3600|7200|
|Maximální počet souběžných relací|30 000|30 000|30 000|30 000|30 000|30 000|
|Počet replik|1|1|1|1|1|1|
|Více AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Škálování čtení|N/A|N/A|N/A|N/A|N/A|N/A|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* Maximální hodnota pro velikost v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](resource-limits-logical-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen4"></a>Uživatelsky důležité – zřízené COMPUTE – COMPUTE GEN4 –

> [!IMPORTANT]
> Nové databáze COMPUTE GEN4 – již nejsou podporovány v oblastech Austrálie – východ a Brazílie – jih.

### <a name="gen4-compute-generation-part-1"></a>Generování výpočetních prostředků COMPUTE GEN4 – (část 1)

|Velikost výpočetních prostředků (cíl služby)|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Generování výpočtů|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|
|Virtuální jádra|1|2|3|4|5|6|
|Paměť (GB)|7|14|21|28|35|42|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|1|2|3|4|5|6|
|Typ úložiště|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|
|Maximální velikost dat (GB)|1024|1024|1024|1024|1024|1024|
|Maximální velikost protokolu (GB)|307|307|307|307|307|307|
|Maximální velikost dat databáze TempDB (GB)|32|64|96|128|160|192|
|Latence v/v (přibližná)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální počet datových IOPS *|4 000|8 000|12 000|16 000|20 000|24 000|
|Maximální rychlost protokolu (MB/s)|8|16|24|32|40|48|
|Maximální počet souběžných pracovních procesů (požadavků)|200|400|600|800|1000|1200|
|Maximální počet souběžných přihlášení|200|400|600|800|1000|1200|
|Maximální počet souběžných relací|30 000|30 000|30 000|30 000|30 000|30 000|
|Počet replik|4|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* Maximální hodnota pro velikost v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](resource-limits-logical-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Generace COMPUTE COMPUTE GEN4 – (část 2)

|Velikost výpočetních prostředků (cíl služby)|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generování výpočtů|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|
|Virtuální jádra|7|8|9|10|16|24|
|Paměť (GB)|49|56|63|70|112|159,5|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|7|8|9,5|11|20|36|
|Typ úložiště|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|
|Maximální velikost dat (GB)|1024|1024|1024|1024|1024|1024|
|Maximální velikost protokolu (GB)|307|307|307|307|307|307|
|Maximální velikost dat databáze TempDB (GB)|224|256|288|320|512|768|
|Latence v/v (přibližná)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální počet datových IOPS |28 000|32 000|36 000|40,000|64 000|76 800|
|Maximální rychlost protokolu (MB/s)|56|64|64|64|64|64|
|Maximální počet souběžných pracovních procesů (požadavků)|1400|1600|1800|2000|3200|4800|
|Maximální počet souběžných přihlášení (požadavků)|1400|1600|1800|2000|3200|4800|
|Maximální počet souběžných relací|30 000|30 000|30 000|30 000|30 000|30 000|
|Počet replik|4|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* Maximální hodnota pro velikost v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](resource-limits-logical-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen5"></a>Uživatelsky důležité – zřízené COMPUTE – Gen5

### <a name="gen5-compute-generation-part-1"></a>Generování výpočetních prostředků Gen5 (část 1)

|Velikost výpočetních prostředků (cíl služby)|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuální jádra|2|4|6|8|10|12|14|
|Paměť (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|1,57|3,14|4,71|6,28|8,65|11,02|13,39|
|Maximální velikost dat (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maximální velikost protokolu (GB)|307|307|461|461|461|922|922|
|Maximální velikost dat databáze TempDB (GB)|64|128|192|256|320|384|448|
|Typ úložiště|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|
|Latence v/v (přibližná)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální počet datových IOPS *|8000|16 000|24 000|32 000|40,000|48 000|56 000|
|Maximální rychlost protokolu (MB/s)|24|48|72|96|96|96|96|
|Maximální počet souběžných pracovních procesů (požadavků)|200|400|600|800|1000|1200|1400|
|Maximální počet souběžných přihlášení|200|400|600|800|1000|1200|1400|
|Maximální počet souběžných relací|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Počet replik|4|4|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* Maximální hodnota pro velikost v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Generace COMPUTE Gen5 (část 2)

|Velikost výpočetních prostředků (cíl služby)|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuální jádra|16|18|20|24|32|40|80|
|Paměť (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|15.77|18,14|20,51|25,25|37,94|52,23|131,64|
|Maximální velikost dat (GB)|3072|3072|3072|4 096|4 096|4 096|4 096|
|Maximální velikost protokolu (GB)|922|922|922|1024|1024|1024|1024|
|Maximální velikost dat databáze TempDB (GB)|512|576|640|768|1024|1280|2560|
|Typ úložiště|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|
|Latence v/v (přibližná)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální počet datových IOPS *|64 000|72 000|80,000|96 000|128 000|160 000|204 800|
|Maximální rychlost protokolu (MB/s)|96|96|96|96|96|96|96|
|Maximální počet souběžných pracovních procesů (požadavků)|1600|1800|2000|2400|3200|4000|8000|
|Maximální počet souběžných přihlášení|1600|1800|2000|2400|3200|4000|8000|
|Maximální počet souběžných relací|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Počet replik|4|4|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* Maximální hodnota pro velikost v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](resource-limits-logical-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---m-series"></a>Podnikové kritické pro výpočetní prostředí-M-Series

### <a name="m-series-compute-generation-part-1"></a>Generace výpočetních prostředků řady M-Series (část 1)

|Velikost výpočetních prostředků (cíl služby)|BC_M_8|BC_M_10|BC_M_12|BC_M_14|BC_M_16|BC_M_18|
|:---| ---:|---:|---:|---:|---:|---:|
|Generování výpočtů|Řada M|Řada M|Řada M|Řada M|Řada M|Řada M|
|Virtuální jádra|8|10|12|14|16|18|
|Paměť (GB)|235,4|294,3|353,2|412,0|470,9|529,7|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|64|80|96|112|128|150|
|Maximální velikost dat (GB)|512|640|768|896|1024|1152|
|Maximální velikost protokolu (GB)|171|213|256|299|341|384|
|Maximální velikost dat databáze TempDB (GB)|256|320|384|448|512|576|
|Typ úložiště|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|
|Latence v/v (přibližná)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální počet datových IOPS *|12 499|15 624|18 748|21 873|24 998|28 123|
|Maximální rychlost protokolu (MB/s)|48|60|72|84|96|108|
|Maximální počet souběžných pracovních procesů (požadavků)|800|1 000|1 200|1 400|1 600|1 800|
|Maximální počet souběžných přihlášení|800|1 000|1 200|1 400|1 600|1 800|
|Maximální počet souběžných relací|30000|30000|30000|30000|30000|30000|
|Počet replik|4|4|4|4|4|4|
|Více AZ|Ne|Ne|Ne|Ne|Ne|Ne|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* Maximální hodnota pro velikost v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](resource-limits-logical-server.md#resource-governance).

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi a uvolnit nevyužité místo. Další informace najdete v tématu [Správa prostoru souborů v Azure SQL Database](file-space-manage.md).

### <a name="m-series-compute-generation-part-2"></a>Generace COMPUTE řady M-Series (část 2)

|Velikost výpočetních prostředků (cíl služby)|BC_M_20|BC_M_24|BC_M_32|BC_M_64|BC_M_128|
|:---| ---:|---:|---:|---:|---:|
|Generování výpočtů|Řada M|Řada M|Řada M|Řada M|Řada M|
|Virtuální jádra|20|24|32|64|128|
|Paměť (GB)|588,6|706,3|941,8|1883,5|3767,0|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|172|216|304|704|1768|
|Maximální velikost dat (GB)|1280|1536|2 048|4 096|4 096|
|Maximální velikost protokolu (GB)|427|512|683|1024|1024|
|Maximální velikost dat databáze TempDB (GB)|4 096|2 048|1024|768|640|
|Typ úložiště|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|
|Latence v/v (přibližná)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální počet datových IOPS *|31 248|37 497|49 996|99 993|160 000|
|Maximální rychlost protokolu (MB/s)|120|144|192|264|264|
|Maximální počet souběžných pracovních procesů (požadavků)|2 000|2 400|3 200|6 400|12 800|
|Maximální počet souběžných přihlášení|2 000|2 400|3 200|6 400|12 800|
|Maximální počet souběžných relací|30000|30000|30000|30000|30000|
|Počet replik|4|4|4|4|4|
|Více AZ|Ne|Ne|Ne|Ne|Ne|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* Maximální hodnota pro velikost v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](resource-limits-logical-server.md#resource-governance).

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi a uvolnit nevyužité místo. Další informace najdete v tématu [Správa prostoru souborů v Azure SQL Database](file-space-manage.md).



## <a name="next-steps"></a>Další kroky

- Omezení prostředků DTU pro jednu databázi najdete v tématu [omezení prostředků pro izolované databáze pomocí modelu nákupu DTU](resource-limits-dtu-single-databases.md) .
- Omezení prostředků vCore pro elastické fondy najdete v tématu [omezení prostředků pro elastické fondy pomocí modelu nákupu Vcore](resource-limits-vcore-elastic-pools.md) .
- Omezení prostředků DTU pro elastické fondy najdete v tématu [omezení prostředků pro elastické fondy pomocí modelu nákupu DTU](resource-limits-dtu-elastic-pools.md) .
- Omezení prostředků pro spravovanou instanci SQL najdete v tématu [omezení prostředků spravované instance SQL](../managed-instance/resource-limits.md).
- Informace o obecných omezeních Azure najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- Informace o omezeních prostředků na serveru najdete v tématu [Přehled omezení prostředků na serveru](resource-limits-logical-server.md) pro informace o omezeních na úrovni serveru a předplatného.
