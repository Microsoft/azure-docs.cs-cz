---
title: Omezení na základě vCore prostředků Azure SQL Database - jedné databáze | Microsoft Docs
description: Tato stránka popisuje některé běžné limitů na základě vCore prostředků pro jednu databázi v databázi SQL Azure.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 1a14e1a7c50f458067491a8605a0518056ac0aa8
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309894"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-a-single-database-preview"></a>Azure SQL Database vCore na základě nákupu modelu omezení pro jednu databázi (preview)

Tento článek obsahuje podrobné prostředků limity pro izolované databáze Azure SQL Database pomocí modelu na základě vCore nákupu.

Na základě DTU omezení nákupu modelu najdete v části [limitů prostředků na základě DTU databáze SQL](sql-database-dtu-resource-limits.md).

## <a name="single-database-storage-sizes-and-performance-levels"></a>Izolované databáze: velikosti úložiště a úrovně výkonu

Pro izolované databáze v následujících tabulkách prostředky k dispozici pro jednu databázi na úrovni jednotlivých služeb a výkonu. Můžete nastavit úroveň služby, úroveň výkonu a velikost úložiště pro jednu databázi pomocí [portál Azure](sql-database-servers-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [Transact-SQL](sql-database-servers-databases-manage.md#transact-sql-manage-logical-servers-and-databases), [prostředí PowerShell](sql-database-servers-databases-manage.md#powershell-manage-logical-servers-and-databases), [Rozhraní příkazového řádku azure](sql-database-servers-databases-manage.md#azure-cli-manage-logical-servers-and-databases), nebo [rozhraní REST API](sql-database-servers-databases-manage.md#rest-api-manage-logical-servers-and-databases).

### <a name="general-purpose-service-tier"></a>Obecné účely úroveň služby

#### <a name="generation-4-compute-platform"></a>Generování 4 výpočetní platforma
|Úroveň výkonu|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Generování H i zápis|4|4|4|4|4|4|
|Virtuální jádra|1|2|4|8|16|24|
|Paměť (GB)|7|14|28|56|112|168|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Typ úložiště|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|
|Latence vstupně-výstupní operace (přibližnou)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|
|Maximální velikost dat (GB)|1024|1024|1536|3072|4 096|4 096|
|Maximální velikost protokolu|307|307|461|922|1229|1229|
|Databáze TempDB size(DB)|32|64|128|256|384|384|
|Cíl IOPS (64 KB)|500|1000|2000|4000|7000|7000|
|Maximální počet souběžných pracovních procesů (počet požadavků)|200|400|800|1600|3200|4800|
|Maximální povolená relací|30000|30000|30000|30000|30000|30000|
|Počet replik|1|1|1|1|1|1|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|000
|Přečtěte si Škálováním na více systémů|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Zahrnuté úložiště záloh|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|
|||

#### <a name="generation-5-compute-platform"></a>Generování 5 výpočetní platforma
|Úroveň výkonu|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40| GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |
|Generování H i zápis|5|5|5|5|5|5|5|
|Virtuální jádra|2|4|8|16|24|32|40|80|
|Paměť (GB)|11|22|44|88|132|176|220|440|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Typ úložiště|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|
|Latence vstupně-výstupní operace (přibližnou)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|
|Maximální velikost dat (GB)|1024|1024|1536|3072|4 096|4 096|4 096|4 096|
|Maximální velikost protokolu|307|307|461|614|1229|1229|1229|1229|
|Databáze TempDB size(DB)|64|128|256|384|384|384|384|384|
|Cíl IOPS (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|Maximální počet souběžných pracovních procesů (počet požadavků)|200|400|800|1600|2400|3200|4000|8000|
|Maximální povolená relací|30000|30000|30000|30000|30000|30000|30000|30000|
|Počet replik|1|1|1|1|1|1|1|1|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Přečtěte si Škálováním na více systémů|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Zahrnuté úložiště záloh|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|
|||

### <a name="business-critical-service-tier"></a>Obchodní vrstva kritické služby

#### <a name="generation-4-compute-platform"></a>Generování 4 výpočetní platforma
|Úroveň výkonu|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generování H i zápis|4|4|4|4|4|4|
|Virtuální jádra|1|2|4|8|16|24|
|Paměť (GB)|7|14|28|56|112|168|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|1|2|4|8|20|36|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Maximální velikost dat (GB)|1024|1024|1024|1024|1024|1024|
|Maximální velikost protokolu|307|307|307|307|307|307|
|Databáze TempDB size(DB)|32|64|128|256|384|384|
|Cíl IOPS (64 KB)|5000|10000|20000|40000|80000|120000|
|Latence vstupně-výstupní operace (přibližnou)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|
|Maximální počet souběžných pracovních procesů (počet požadavků)|200|400|800|1600|3200|4800|
|Maximální povolená relací|30000|30000|30000|30000|30000|30000|
|Počet replik|3|3|3|3|3|3|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Přečtěte si Škálováním na více systémů|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště záloh|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|
|||

#### <a name="generation-5-compute-platform"></a>Generování 5 výpočetní platforma
|Úroveň výkonu|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |--: |
|Generování H i zápis|5|5|5|5|5|5|5|5|
|Virtuální jádra|2|4|8|16|24|32|40|80|
|Paměť (GB)|11|22|44|88|132|176|220|440|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|1.571|3,142|6.284|15.768|25.252|37.936|52.22|131.64|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Latence vstupně-výstupní operace (přibližnou)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|
|Maximální velikost dat (GB)|1024|1024|1024|1024|2 048|4 096|4 096|4 096|
|Maximální velikost protokolu|307|307|307|307|614|1229|1229|1229|
|Databáze TempDB size(DB)|64|128|256|384|384|384|384|384|
|Cíl IOPS (64 KB)|5000|10000|20000|40000|60000|80000|100000.|200000
|Maximální počet souběžných pracovních procesů (počet požadavků)|200|400|800|1600|2400|3200|4000|8000|
|Maximální povolená relací|30000|30000|30000|30000|30000|30000|30000|30000|
|Počet replik|3|3|3|3|3|3|3|3|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Přečtěte si Škálováním na více systémů|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Zahrnuté úložiště záloh|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|
|||

## <a name="next-steps"></a>Další postup

- V tématu [nejčastější dotazy k databázi SQL](sql-database-faq.md) odpovědi na nejčastější dotazy.
- Informace o obecných omezeních Azure najdete v tématu [předplatného Azure a omezení služby, kvóty a omezení](../azure-subscription-service-limits.md).
