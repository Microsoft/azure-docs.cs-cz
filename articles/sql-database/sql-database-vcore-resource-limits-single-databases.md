---
title: omezení prostředků virtuálních jader – jedna databáze
description: Tato stránka popisuje některé běžné omezení prostředků virtuálních jader pro jednu databázi v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/11/2020
ms.openlocfilehash: 38d44f5a2ce22de15cb14ea5aa7a9ca1ea7c03cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481061"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>Omezení prostředků pro jednotlivé databáze pomocí nákupního modelu virtuálních jader

Tento článek obsahuje podrobné limity prostředků pro azure SQL database jednotlivé databáze pomocí nákupního modelu virtuálních jader.

Omezení nákupního modelu DTU pro jednotlivé databáze na serveru SQL Database naleznete v [tématu Přehled omezení prostředků na serveru SQL Database](sql-database-resource-limits-database-server.md).

Můžete nastavit úroveň služby, velikost výpočetních prostředků a velikost úložiště pro jednu databázi pomocí [portálu Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShellu](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), [rozhraní API Azure](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)nebo rozhraní REST [API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Pokyny a důležité informace o škálování najdete v [tématu Škálování jedné databáze](sql-database-single-database-scale.md).

## <a name="general-purpose---serverless-compute---gen5"></a>Obecné účely - bezserverové výpočty - Gen5

[Výpočetní úroveň bez serveru](sql-database-serverless.md) je aktuálně dostupná pouze na hardwaru Gen5.

### <a name="gen5-compute-generation-part-1"></a>Generace výpočetních prostředků Gen5 (část 1)

|Velikost výpočetních prostředků|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|Výpočetní generace|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|
|Virtuální jádra Min-max|0.5-1|0.5-2|0.5-4|0.75-6|1.0-8|
|Min-max paměť (GB)|2.02-3|2.05-6|2.10-12|2.25-18|3.00-24|
|Min automatické pozastavení zpoždění (minuty)|60|60|60|60|60|
|Podpora pro Columnstore|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (CZ)|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Maximální velikost dat (GB)|512|1024|1024|1024|1536|
|Maximální velikost protokolu (GB)|154|307|307|307|461|
|Maximální velikost dat TempDB (GB)|32|64|128|192|256|
|Typ úložiště|Vzdálený SSD|Vzdálený SSD|Vzdálený SSD|Vzdálený SSD|Vzdálený SSD|
|Latence vi (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální datové vipos *|320|640|1280|1920|2560|
|Maximální rychlost protokolů (Mb/s)|3.8|7,5|15|22.5|30|
|Maximální počet souběžných pracovníků (požadavky)|75|150|300|450|600|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|
|Počet replik|1|1|1|1|1|
|Multi-AZ|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Škálování čtení|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Zahrnuté úložiště záloh|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|

\*Maximální hodnota pro velikosti vi v rozsahu od 8 KB do 64 KB. Skutečné viposlužby jsou závislé na pracovní zátěži. Podrobnosti naleznete v [tématu Zásadsprávné správy vi.](sql-database-resource-limits-database-server.md#resource-governance)

### <a name="gen5-compute-generation-part-2"></a>Gen5 výpočetní generace (část 2)

|Velikost výpočetních prostředků|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|Výpočetní generace|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|
|Virtuální jádra Min-max|1.25-10|1.50-12|1.75-14|2.00-16|
|Min-max paměť (GB)|3.75-30|4.50-36|5.25-42|6.00-48|
|Min automatické pozastavení zpoždění (minuty)|60|60|60|60|
|Podpora pro Columnstore|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (CZ)|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Maximální velikost dat (GB)|1536|3072|3072|3072|
|Maximální velikost protokolu (GB)|461|461|461|922|
|Maximální velikost dat TempDB (GB)|320|384|448|512|
|Typ úložiště|Vzdálený SSD|Vzdálený SSD|Vzdálený SSD|Vzdálený SSD|
|Latence vi (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální datové vipos *|3200|3840|4480|5120|
|Maximální rychlost protokolů (Mb/s)|30|30|30|30|
|Maximální počet souběžných pracovníků (požadavky)|750|900|1050|1200|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|
|Počet replik|1|1|1|1|
|Multi-AZ|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Škálování čtení|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Zahrnuté úložiště záloh|1X db velikost|1X db velikost|1X db velikost|1X db velikost|

\*Maximální hodnota pro velikosti vi v rozsahu od 8 KB do 64 KB. Skutečné viposlužby jsou závislé na pracovní zátěži. Podrobnosti naleznete v [tématu Zásadsprávné správy vi.](sql-database-resource-limits-database-server.md#resource-governance)

## <a name="hyperscale---provisioned-compute---gen4"></a>Hyperscale – zřízené výpočetní prostředky – Gen4

### <a name="gen4-compute-generation-part-1"></a>Gen4 výpočetní generace (část 1)

|Úroveň výkonu|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|Výpočetní generace|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|
|virtuální jádra|1|2|3|4|5|6|
|Paměť (GB)|7|14|21|28|35|42|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Velikost|3X paměť|3X paměť|3X paměť|3X paměť|3X paměť|3X paměť|
|Podpora pro Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (CZ)|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Maximální velikost dat (TB)|100 |100 |100 |100 |100 |100|
|Maximální velikost protokolu (TB)|1 |1 |1 |1 |1 |1 |
|Maximální velikost dat TempDB (GB)|32|64|96|128|160|192|
|Typ úložiště| [Poznámka 1](#notes) |[Poznámka 1](#notes)|[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |
|Maximální datové vipos *|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|
|Maximální rychlost protokolů (Mb/s)|100 |100 |100 |100 |100 |100 |
|Latence vi (přibližná)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|
|Maximální počet souběžných pracovníků (požadavky)|200|400|600|800|1000|1200|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|
|Sekundární repliky|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|
|Uchovávání úložiště záloh|7 dní|7 dní|7 dní|7 dní|7 dní|7 dní|
|||

### <a name="gen4-compute-generation-part-2"></a>Gen4 výpočetní generace (část 2)

|Úroveň výkonu|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|Výpočetní generace|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|
|virtuální jádra|7|8|9|10|16|24|
|Paměť (GB)|49|56|63|70|112|159.5|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Velikost|3X paměť|3X paměť|3X paměť|3X paměť|3X paměť|3X paměť|
|Podpora pro Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (CZ)|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Maximální velikost dat (TB)|100 |100 |100 |100 |100 |100 |
|Maximální velikost protokolu (TB)|1 |1 |1 |1 |1 |1 |
|Maximální velikost dat TempDB (GB)|224|256|288|320|512|768|
|Typ úložiště| [Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |
|Maximální datové vipos *|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|
|Maximální rychlost protokolů (Mb/s)|100 |100 |100 |100 |100 |100 |
|Latence vi (přibližná)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|
|Maximální počet souběžných pracovníků (požadavky)|1400|1600|1800|2000|3200|4800|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|
|Sekundární repliky|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|
|Uchovávání úložiště záloh|7 dní|7 dní|7 dní|7 dní|7 dní|7 dní|
|||

\*Maximální hodnota pro velikosti vi v rozsahu od 8 KB do 64 KB. Skutečné viposlužby jsou závislé na pracovní zátěži. Podrobnosti naleznete v [tématu Zásadsprávné správy vi.](sql-database-resource-limits-database-server.md#resource-governance)

## <a name="hyperscale---provisioned-compute---gen5"></a>Hyperscale – zřízené výpočetní prostředky – Gen5

### <a name="gen5-compute-generation-part-1"></a>Generace výpočetních prostředků Gen5 (část 1)

|Úroveň výkonu|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Výpočetní generace|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|
|virtuální jádra|2|4|6|8|10|12|14|
|Paměť (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Velikost|3X paměť|3X paměť|3X paměť|3X paměť|3X paměť|3X paměť|3X paměť|
|Podpora pro Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (CZ)|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Maximální velikost dat (TB)|100 |100 |100 |100 |100 |100 |100|
|Maximální velikost protokolu (TB)|1 |1 |1 |1 |1 |1 |1 |
|Maximální velikost dat TempDB (GB)|64|128|192|256|320|384|448|
|Typ úložiště| [Poznámka 1](#notes) |[Poznámka 1](#notes)|[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |
|Maximální datové vipos *|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|
|Maximální rychlost protokolů (Mb/s)|100 |100 |100 |100 |100 |100 |100 |
|Latence vi (přibližná)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|
|Maximální počet souběžných pracovníků (požadavky)|200|400|600|800|1000|1200|1400|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Sekundární repliky|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Uchovávání úložiště záloh|7 dní|7 dní|7 dní|7 dní|7 dní|7 dní|7 dní|
|||

\*Maximální hodnota pro velikosti vi v rozsahu od 8 KB do 64 KB. Skutečné viposlužby jsou závislé na pracovní zátěži. Podrobnosti naleznete v [tématu Zásadsprávné správy vi.](sql-database-resource-limits-database-server.md#resource-governance)

### <a name="gen5-compute-generation-part-2"></a>Gen5 výpočetní generace (část 2)

|Úroveň výkonu|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|Výpočetní generace|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|
|virtuální jádra|16|18|20|24|32|40|80|
|Paměť (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Velikost|3X paměť|3X paměť|3X paměť|3X paměť|3X paměť|3X paměť|3X paměť|
|Podpora pro Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (CZ)|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Maximální velikost dat (TB)|100 |100 |100 |100 |100 |100 |100 |
|Maximální velikost protokolu (TB)|1 |1 |1 |1 |1 |1 |1 |
|Maximální velikost dat TempDB (GB)|512|576|640|768|1024|1280|2560|
|Typ úložiště| [Poznámka 1](#notes) |[Poznámka 1](#notes)|[Poznámka 1](#notes)|[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |[Poznámka 1](#notes) |
|Maximální datové vipos *|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|[Poznámka 2](#notes)|
|Maximální rychlost protokolů (Mb/s)|100 |100 |100 |100 |100 |100 |100 |
|Latence vi (přibližná)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|[Poznámka 3](#notes)|
|Maximální počet souběžných pracovníků (požadavky)|1600|1800|2000|2400|3200|4000|8000|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Sekundární repliky|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Uchovávání úložiště záloh|7 dní|7 dní|7 dní|7 dní|7 dní|7 dní|7 dní|
|||

\*Maximální hodnota pro velikosti vi v rozsahu od 8 KB do 64 KB. Skutečné viposlužby jsou závislé na pracovní zátěži. Podrobnosti naleznete v [tématu Zásadsprávné správy vi.](sql-database-resource-limits-database-server.md#resource-governance)

#### <a name="notes"></a>Poznámky

**Poznámka 1:** Hyperscale je vícevrstvá architektura se samostatnými výpočetními a úložnými komponentami: [Architektura úrovně služeb hyperškálování](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)

**Poznámka 2:** Hyperscale vícevrstvé architektury má ukládání do mezipaměti na více úrovních. Efektivní vipos bude záviset na zatížení.

**Poznámka 3**: Latence je 1-2 ms pro data v mezipaměti založené na RBPEX SSD na výpočetních replikách, které ukládá nejpoužívanější datové stránky do mezipaměti. Vyšší latence pro data načtená ze stránkových serverů.

## <a name="general-purpose---provisioned-compute---gen4"></a>Obecný účel – zřízené výpočetní prostředky – Gen4

> [!IMPORTANT]
> Nové databáze Gen4 již nejsou podporovány v oblastech Austrálie – východ nebo Brazílie – jih.

### <a name="gen4-compute-generation-part-1"></a>Gen4 výpočetní generace (část 1)

|Velikost výpočetních prostředků|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Výpočetní generace|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|
|virtuální jádra|1|2|3|4|5|6|
|Paměť (GB)|7|14|21|28|35|42|
|Podpora pro Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (CZ)|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Maximální velikost dat (GB)|1024|1024|1536|1536|1536|3072|
|Maximální velikost protokolu (GB)|307|307|461|461|461|922|
|Maximální velikost dat TempDB (GB)|32|64|96|128|160|192|
|Typ úložiště|Vzdálený SSD|Vzdálený SSD|Vzdálený SSD|Vzdálený SSD|Vzdálený SSD|Vzdálený SSD|
|Latence vi (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální datové vipos *|320|640|960|1280|1600|1920|
|Maximální rychlost protokolů (Mb/s)|3,75|7,5|11.25|15|18.75|22.5|
|Maximální počet souběžných pracovníků (požadavky)|200|400|600|800|1000|1200|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|
|Počet replik|1|1|1|1|1|1|
|Multi-AZ|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Škálování čtení|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Zahrnuté úložiště záloh|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|

\*Maximální hodnota pro velikosti vi v rozsahu od 8 KB do 64 KB. Skutečné viposlužby jsou závislé na pracovní zátěži. Podrobnosti naleznete v [tématu Zásadsprávné správy vi.](sql-database-resource-limits-database-server.md#resource-governance)

### <a name="gen4-compute-generation-part-2"></a>Gen4 výpočetní generace (část 2)

|Velikost výpočetních prostředků|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Výpočetní generace|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|
|virtuální jádra|7|8|9|10|16|24|
|Paměť (GB)|49|56|63|70|112|159.5|
|Podpora pro Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (CZ)|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Maximální velikost dat (GB)|3072|3072|3072|3072|4 096|4 096|
|Maximální velikost protokolu (GB)|922|922|922|922|1229|1229|
|Maximální velikost dat TempDB (GB)|224|256|288|320|512|768|
|Typ úložiště|Vzdálený SSD|Vzdálený SSD|Vzdálený SSD|Vzdálený SSD|Vzdálený SSD|Vzdálený SSD|
|Latence vi (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)
|Maximální datové vipos *|2240|2560|2880|3200|5120|7680|
|Maximální rychlost protokolů (Mb/s)|26.3|30|30|30|30|30|
|Maximální počet souběžných pracovníků (požadavky)|1400|1600|1800|2000|3200|4800|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|
|Počet replik|1|1|1|1|1|1|
|Multi-AZ|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Škálování čtení|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Zahrnuté úložiště záloh|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|

\*Maximální hodnota pro velikosti vi v rozsahu od 8 KB do 64 KB. Skutečné viposlužby jsou závislé na pracovní zátěži. Podrobnosti naleznete v [tématu Zásadsprávné správy vi.](sql-database-resource-limits-database-server.md#resource-governance)

## <a name="general-purpose---provisioned-compute---gen5"></a>Obecný účel – zřízené výpočetní prostředky – Gen5

### <a name="gen5-compute-generation-part-1"></a>Generace výpočetních prostředků Gen5 (část 1)

|Velikost výpočetních prostředků|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Výpočetní generace|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|
|virtuální jádra|2|4|6|8|10|12|14|
|Paměť (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|Podpora pro Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (CZ)|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Maximální velikost dat (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maximální velikost protokolu (GB)|307|307|461|461|461|922|922|
|Maximální velikost dat TempDB (GB)|64|128|192|256|320|384|384|
|Typ úložiště|Vzdálený SSD|Vzdálený SSD|Vzdálený SSD|Vzdálený SSD|Vzdálený SSD|Vzdálený SSD|Vzdálený SSD|
|Latence vi (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální datové vipos *|640|1280|1920|2560|3200|3840|4480|
|Maximální rychlost protokolů (Mb/s)|7,5|15|22.5|30|30|30|30|
|Maximální počet souběžných pracovníků (požadavky)|200|400|600|800|1000|1200|1400|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Počet replik|1|1|1|1|1|1|1|
|Multi-AZ|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Škálování čtení|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Zahrnuté úložiště záloh|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|

\*Maximální hodnota pro velikosti vi v rozsahu od 8 KB do 64 KB. Skutečné viposlužby jsou závislé na pracovní zátěži. Podrobnosti naleznete v [tématu Zásadsprávné správy vi.](sql-database-resource-limits-database-server.md#resource-governance)

### <a name="gen5-compute-generation-part-2"></a>Gen5 výpočetní generace (část 2)

|Velikost výpočetních prostředků|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Výpočetní generace|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|
|virtuální jádra|16|18|20|24|32|40|80|
|Paměť (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Podpora pro Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (CZ)|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Maximální velikost dat (GB)|3072|3072|3072|4 096|4 096|4 096|4 096|
|Maximální velikost protokolu (GB)|922|922|922|1229|1229|1229|1229|
|Maximální velikost dat TempDB (GB)|512|576|640|768|1024|1280|2560|
|Typ úložiště|Vzdálený SSD|Vzdálený SSD|Vzdálený SSD|Vzdálený SSD|Vzdálený SSD|Vzdálený SSD|Vzdálený SSD|
|Latence vi (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální datové vipos *|5120|5760|6400|7680|10240|12800|25600|
|Maximální rychlost protokolů (Mb/s)|30|30|30|30|30|30|30|
|Maximální počet souběžných pracovníků (požadavky)|1600|1800|2000|2400|3200|4000|8000|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Počet replik|1|1|1|1|1|1|1|
|Multi-AZ|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Škálování čtení|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Zahrnuté úložiště záloh|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|

\*Maximální hodnota pro velikosti vi v rozsahu od 8 KB do 64 KB. Skutečné viposlužby jsou závislé na pracovní zátěži. Podrobnosti naleznete v [tématu Zásadsprávné správy vi.](sql-database-resource-limits-database-server.md#resource-governance)

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Obecný účel – zřízené výpočetní prostředky – řada Fsv2

### <a name="fsv2-series-compute-generation-preview"></a>Výpočetní generace řady Fsv2 (náhled)

|Velikost výpočetních prostředků|GP_Fsv2_72|
|:--- | --: |
|Výpočetní generace|Řada Fsv2|
|virtuální jádra|72|
|Paměť (GB)|136.2|
|Podpora pro Columnstore|Ano|
|Úložiště OLTP v paměti (CZ)|Není dostupné.|
|Maximální velikost dat (GB)|4 096|
|Maximální velikost protokolu (GB)|1024|
|Maximální velikost dat TempDB (GB)|333|
|Typ úložiště|Vzdálený SSD|
|Latence vi (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální datové vipos *|12,800|
|Maximální rychlost protokolů (Mb/s)|30|
|Maximální počet souběžných pracovníků (požadavky)|3600|
|Maximální počet souběžných přihlášení|3600|
|Maximální počet souběžných relací|30,000|
|Počet replik|1|
|Multi-AZ|Není dostupné.|
|Škálování čtení|Není dostupné.|
|Zahrnuté úložiště záloh|1X db velikost|

\*Maximální hodnota pro velikosti vi v rozsahu od 8 KB do 64 KB. Skutečné viposlužby jsou závislé na pracovní zátěži. Podrobnosti naleznete v [tématu Zásadsprávné správy vi.](sql-database-resource-limits-database-server.md#resource-governance)

## <a name="business-critical---provisioned-compute---gen4"></a>Kritické pro podnikání – zřízené výpočetní prostředky – Gen4

> [!IMPORTANT]
> Nové databáze Gen4 již nejsou podporovány v oblastech Austrálie – východ nebo Brazílie – jih.

### <a name="gen4-compute-generation-part-1"></a>Gen4 výpočetní generace (část 1)

|Velikost výpočetních prostředků|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Výpočetní generace|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|
|virtuální jádra|1|2|3|4|5|6|
|Paměť (GB)|7|14|21|28|35|42|
|Podpora pro Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (CZ)|1|2|3|4|5|6|
|Typ úložiště|Místní SSD|Místní SSD|Místní SSD|Místní SSD|Místní SSD|Místní SSD|
|Maximální velikost dat (GB)|1024|1024|1024|1024|1024|1024|
|Maximální velikost protokolu (GB)|307|307|307|307|307|307|
|Maximální velikost dat TempDB (GB)|32|64|96|128|160|192|
|Latence vi (přibližná)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální datové vipos *|4 000|8 000|12 000|16 000|20 000|24,000|
|Maximální rychlost protokolů (Mb/s)|8|16|24|32|40|48|
|Maximální počet souběžných pracovníků (požadavky)|200|400|600|800|1000|1200|
|Maximální počet souběžných přihlášení|200|400|600|800|1000|1200|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|
|Počet replik|4|4|4|4|4|4|
|Multi-AZ|Ano|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště záloh|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|

\*Maximální hodnota pro velikosti vi v rozsahu od 8 KB do 64 KB. Skutečné viposlužby jsou závislé na pracovní zátěži. Podrobnosti naleznete v [tématu Zásadsprávné správy vi.](sql-database-resource-limits-database-server.md#resource-governance)

### <a name="gen4-compute-generation-part-2"></a>Gen4 výpočetní generace (část 2)

|Velikost výpočetních prostředků|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Výpočetní generace|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|
|virtuální jádra|7|8|9|10|16|24|
|Paměť (GB)|49|56|63|70|112|159.5|
|Podpora pro Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (CZ)|7|8|9.5|11|20|36|
|Typ úložiště|Místní SSD|Místní SSD|Místní SSD|Místní SSD|Místní SSD|Místní SSD|
|Maximální velikost dat (GB)|1024|1024|1024|1024|1024|1024|
|Maximální velikost protokolu (GB)|307|307|307|307|307|307|
|Maximální velikost dat TempDB (GB)|224|256|288|320|512|768|
|Latence vi (přibližná)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální datové vipoje |28,000|32,000|36,000|40,000|64,000|76 800|
|Maximální rychlost protokolů (Mb/s)|56|64|64|64|64|64|
|Maximální počet souběžných pracovníků (požadavky)|1400|1600|1800|2000|3200|4800|
|Maximální počet souběžných přihlášení (požadavků)|1400|1600|1800|2000|3200|4800|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|
|Počet replik|4|4|4|4|4|4|
|Multi-AZ|Ano|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště záloh|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|

\*Maximální hodnota pro velikosti vi v rozsahu od 8 KB do 64 KB. Skutečné viposlužby jsou závislé na pracovní zátěži. Podrobnosti naleznete v [tématu Zásadsprávné správy vi.](sql-database-resource-limits-database-server.md#resource-governance)

## <a name="business-critical---provisioned-compute---gen5"></a>Kritické pro podnikání – zřízené výpočetní prostředky – Gen5

### <a name="gen5-compute-generation-part-1"></a>Generace výpočetních prostředků Gen5 (část 1)

|Velikost výpočetních prostředků|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Výpočetní generace|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|
|virtuální jádra|2|4|6|8|10|12|14|
|Paměť (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|Podpora pro Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (CZ)|1.57|3.14|4.71|6.28|8.65|11.02|13.39|
|Maximální velikost dat (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maximální velikost protokolu (GB)|307|307|461|461|461|922|922|
|Maximální velikost dat TempDB (GB)|64|128|192|256|320|384|448|
|Typ úložiště|Místní SSD|Místní SSD|Místní SSD|Místní SSD|Místní SSD|Místní SSD|Místní SSD|
|Latence vi (přibližná)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální datové vipos *|8000|16 000|24,000|32,000|40,000|48,000|56,000|
|Maximální rychlost protokolů (Mb/s)|24|48|72|96|96|96|96|
|Maximální počet souběžných pracovníků (požadavky)|200|400|600|800|1000|1200|1400|
|Maximální počet souběžných přihlášení|200|400|600|800|1000|1200|1400|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Počet replik|4|4|4|4|4|4|4|
|Multi-AZ|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště záloh|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|

\*Maximální hodnota pro velikosti vi v rozsahu od 8 KB do 64 KB. Skutečné viposlužby jsou závislé na pracovní zátěži. Podrobnosti naleznete v [tématu Zásadsprávné správy vi.](sql-database-resource-limits-database-server.md#resource-governance)

### <a name="gen5-compute-generation-part-2"></a>Gen5 výpočetní generace (část 2)

|Velikost výpočetních prostředků|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Výpočetní generace|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|
|virtuální jádra|16|18|20|24|32|40|80|
|Paměť (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Podpora pro Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (CZ)|15.77|18.14|20.51|25.25|37.94|52.23|131.64|
|Maximální velikost dat (GB)|3072|3072|3072|4 096|4 096|4 096|4 096|
|Maximální velikost protokolu (GB)|922|922|922|1229|1229|1229|1229|
|Maximální velikost dat TempDB (GB)|512|576|640|768|1024|1280|2560|
|Typ úložiště|Místní SSD|Místní SSD|Místní SSD|Místní SSD|Místní SSD|Místní SSD|Místní SSD|
|Latence vi (přibližná)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální datové vipos *|64,000|72,000|80,000|96,000|128,000|160 000|204,800|
|Maximální rychlost protokolů (Mb/s)|96|96|96|96|96|96|96|
|Maximální počet souběžných pracovníků (požadavky)|1600|1800|2000|2400|3200|4000|8000|
|Maximální počet souběžných přihlášení|1600|1800|2000|2400|3200|4000|8000|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Počet replik|4|4|4|4|4|4|4|
|Multi-AZ|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště záloh|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|

\*Maximální hodnota pro velikosti vi v rozsahu od 8 KB do 64 KB. Skutečné viposlužby jsou závislé na pracovní zátěži. Podrobnosti naleznete v [tématu Zásadsprávné správy vi.](sql-database-resource-limits-database-server.md#resource-governance)

## <a name="business-critical---provisioned-compute---m-series"></a>Kritické pro podnikání – zřízené výpočetní prostředky – řada M

### <a name="m-series-compute-generation-preview"></a>Výpočetní generace řady M (náhled)

|Velikost výpočetních prostředků|BC_M_128|
|:--- | --: |
|Výpočetní generace|Řada M|
|virtuální jádra|128|
|Paměť (GB)|3767.1|
|Podpora pro Columnstore|Ano|
|Úložiště OLTP v paměti (CZ)|1768|
|Maximální velikost dat (GB)|4 096|
|Maximální velikost protokolu (GB)|2 048|
|Maximální velikost dat TempDB (GB)|4 096|
|Typ úložiště|Místní SSD|
|Latence vi (přibližná)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální datové vipos *|160 000|
|Maximální rychlost protokolů (Mb/s)|264|
|Maximální počet souběžných pracovníků (požadavky)|12,800|
|Maximální počet souběžných přihlášení|12,800|
|Maximální počet souběžných relací|30000|
|Počet replik|4|
|Multi-AZ|Ano|
|Škálování čtení|Ano|
|Zahrnuté úložiště záloh|1X db velikost|

\*Maximální hodnota pro velikosti vi v rozsahu od 8 KB do 64 KB. Skutečné viposlužby jsou závislé na pracovní zátěži. Podrobnosti naleznete v [tématu Zásadsprávné správy vi.](sql-database-resource-limits-database-server.md#resource-governance)

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi, aby bylo možné uvolnit nevyužité místo. Další informace najdete [v tématu Správa místa v souborech v Azure SQL Database](sql-database-file-space-management.md).

## <a name="next-steps"></a>Další kroky

- Omezení prostředků DTU pro jednu databázi naleznete v [tématu omezení prostředků pro jednotlivé databáze pomocí nákupního modelu DTU](sql-database-dtu-resource-limits-single-databases.md)
- Omezení prostředků virtuálních jader pro elastické fondy najdete v [tématu omezení prostředků pro elastické fondy pomocí nákupního modelu virtuálních jader.](sql-database-vcore-resource-limits-elastic-pools.md)
- Omezení prostředků DTU pro elastické fondy najdete v [tématu omezení prostředků pro elastické fondy pomocí nákupního modelu DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Omezení prostředků pro spravované instance naleznete v tématu [limity prostředků spravované instance](sql-database-managed-instance-resource-limits.md).
- Informace o obecných omezeních Azure najdete v tématu [Omezení předplatného a služeb Azure, kvóty a omezení](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Informace o omezení prostředků na databázovém serveru naleznete v [tématu přehled omezení prostředků na serveru DATABÁZE SQL](sql-database-resource-limits-database-server.md) informace o omezení na serveru a odběr úrovně.
