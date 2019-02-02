---
title: Limity založený na virtuálních jádrech prostředků Azure SQL Database – izolované databáze | Dokumentace Microsoftu
description: Tato stránka popisuje některé běžné limity prostředků založený na virtuálních jádrech pro izolovanou databázi ve službě Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: standalone-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 02/01/2019
ms.openlocfilehash: 494b9b5d8be804842406aac98d3b328c7848570d
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55663974"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-a-single-database"></a>Založený na virtuálních jádrech zakoupení modelu omezení pro jednu databázi Azure SQL Database

Tento článek obsahuje podrobné prostředků limity pro izolované databáze Azure SQL Database pomocí nákupní model založený na virtuálních jádrech.

Založený na DTU nákupní model omezení pro izolované databáze na server služby SQL Database najdete v tématu [Přehled prostředků omezuje na server služby SQL Database](sql-database-resource-limits-database-server.md).

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md).

Můžete nastavit úroveň služby, výpočetního prostředí a velikost úložiště pro izolovanou databázi pomocí [webu Azure portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [příkazů jazyka Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), [ Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases), nebo [rozhraní REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>Obecné účely vrstvy služby: Velikosti úložiště a výpočty velikostí

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Obecné účely vrstvy služby: Výpočetní platforma běžící generace 4 (část 1)

|Vypočítat velikost|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Generování H/W|4|4|4|4|4|4|
|Virtuální jádra|1|2|3|4|5|6|
|Paměť (GB)|7|14|21|28|35|42|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Maximální velikost dat (GB)|1024|1024|1024|1536|1536|1536|
|Maximální velikost protokolu (GB)|307|307|307|461|461|461|
|Velikost databáze TempDB (GB)|32|64|96|128|160|192|
|Typ úložiště|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|
|Vstupně-výstupní latence (přibližné)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|
|Cíl vstupně-výstupních operací (64 KB)|500|1000|1 500|2000|2500|3000|
|Maximální počet souběžných pracovních procesů (požadavků)|200|400|600|800|1000|1200|
|Maximální povolené relace|30000|30000|30000|30000|30000|30000|
|Počet replik|1|1|1|1|1|1|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|000
|Přečtěte si horizontální navýšení kapacity|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Zahrnuté úložiště zálohování|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Obecné účely vrstvy služby: Výpočetní platforma běžící generace 4 (část 2)

|Vypočítat velikost|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Generování H/W|4|4|4|4|4|4|
|Virtuální jádra|7|8|9|10|16|24|
|Paměť (GB)|49|56|63|70|112|168|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Maximální velikost dat (GB)|1536|3072|3072|3072|4 096|4 096|
|Maximální velikost protokolu (GB)|461|922|922|922|1229|1229|
|Velikost databáze TempDB (GB)|224|256|288|320|384|384|
|Typ úložiště|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|
|Vstupně-výstupní latence (přibližné)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)
|Cíl vstupně-výstupních operací (64 KB)|3500|4000|4500|5000|7000|7000|
|Maximální počet souběžných pracovních procesů (požadavků)|1400|1600|1800|2000|3200|4800|
|Maximální povolené relace|30000|30000|30000|30000|30000|30000|
|Počet replik|1|1|1|1|1|1|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Přečtěte si horizontální navýšení kapacity|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Zahrnuté úložiště zálohování|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Obecné účely vrstvy služby: Výpočetní platforma běžící generace 5 (část 1)

|Vypočítat velikost|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Generování H/W|5|5|5|5|5|5|5|
|Virtuální jádra|2|4|6|8|10|12|14|
|Paměť (GB)|10.2|20.4|30.6|40.8|51|61.2|71.4|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Maximální velikost dat (GB)|1024|1024|1024|1536|1536|1536|1536|
|Maximální velikost protokolu (GB)|307|307|307|461|461|461|461|
|Velikost databáze TempDB (GB)|64|128|192|256|320|384|384|
|Typ úložiště|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|
|Vstupně-výstupní latence (přibližné)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|
|Cíl vstupně-výstupních operací (64 KB)|500|1000|1 500|2000|2500|3000|3500|
|Maximální počet souběžných pracovních procesů (požadavků)|200|400|600|800|1000|1200|1400|
|Maximální povolené relace|30000|30000|30000|30000|30000|30000|30000|
|Počet replik|1|1|1|1|1|1|1|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Přečtěte si horizontální navýšení kapacity|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Zahrnuté úložiště zálohování|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Obecné účely vrstvy služby: Výpočetní platforma běžící generace 5 (část 2)

|Vypočítat velikost|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Generování H/W|5|5|5|5|5|5|5|
|Virtuální jádra|16|18|20|24|32|40|80|
|Paměť (GB)|81.6|91.8|102|122.4|163.2|204|408|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Maximální velikost dat (GB)|3072|3072|3072|4 096|4 096|4 096|4 096|
|Maximální velikost protokolu (GB)|922|922|922|1229|1229|1229|1229|
|Velikost databáze TempDB (GB)|384|384|384|384|384|384|384|
|Typ úložiště|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|
|Vstupně-výstupní latence (přibližné)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|
|Cíl vstupně-výstupních operací (64 KB)|4000|4500|5000|6000|7000|7000|7000|
|Maximální počet souběžných pracovních procesů (požadavků)|1600|1800|2000|2400|3200|4000|8000|
|Maximální povolené relace|30000|30000|30000|30000|30000|30000|30000|
|Počet replik|1|1|1|1|1|1|1|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Přečtěte si horizontální navýšení kapacity|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Zahrnuté úložiště zálohování|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|

## <a name="business-critical-service-tier-storage-sizes-and-compute-sizes"></a>Obchodní vrstvy služby: Velikosti úložiště a výpočty velikostí

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Obchodní vrstvy služby: Výpočetní platforma běžící generace 4 (část 1)

|Vypočítat velikost|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Generování H/W|4|4|4|4|4|4|
|Virtuální jádra|1|2|3|4|5|6|
|Paměť (GB)|7|14|21|28|35|42|
|Podpora Columnstore|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Úložiště OLTP v paměti (GB)|1|2|3|4|5|6|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Maximální velikost dat (GB)|1024|1024|1024|1024|1024|1024|
|Maximální velikost protokolu (GB)|307|307|307|307|307|307|
|Velikost databáze TempDB (GB)|32|64|96|128|160|192|
|Vstupně-výstupní latence (přibližné)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|
|Cíl vstupně-výstupních operací (64 KB)|5000|10000|15 000|20000|25000|30000|
|Maximální počet souběžných pracovních procesů (požadavků)|200|400|600|800|1000|1200|
|Maximální povolené relace|30000|30000|30000|30000|30000|30000|
|Počet replik|4|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|Ano|
|Přečtěte si horizontální navýšení kapacity|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Obchodní vrstvy služby: Výpočetní platforma běžící generace 4 (část 2)

|Vypočítat velikost|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generování H/W|4|4|4|4|4|4|
|Virtuální jádra|7|8|9|10|16|24|
|Paměť (GB)|49|56|63|70|112|168|
|Podpora Columnstore|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Úložiště OLTP v paměti (GB)|7|8|9.5|11|20|36|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Maximální velikost dat (GB)|1024|1024|1024|1024|1024|1024|
|Maximální velikost protokolu (GB)|307|307|307|307|307|307|
|Velikost databáze TempDB (GB)|224|256|288|320|384|384|
|Vstupně-výstupní latence (přibližné)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|
|Cíl vstupně-výstupních operací (64 KB)|35000|40000|45000|50000|80000|120000|
|Maximální počet souběžných pracovních procesů (požadavků)|1400|1600|1800|2000|3200|4800|
|Maximální povolené relace|30000|30000|30000|30000|30000|30000|
|Počet replik|4|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|Ano|
|Přečtěte si horizontální navýšení kapacity|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Obchodní vrstvy služby: Výpočetní platforma běžící generace 5 (část 1)

|Vypočítat velikost|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |
|Generování H/W|5|5|5|5|5|5|5|
|Virtuální jádra|2|4|6|8|10|12|14|
|Paměť (GB)|10.2|20.4|30.6|40.8|51|61.2|71.4|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|1.571|3.142|4.713|6.284|8.655|11.026|13.397|
|Maximální velikost dat (GB)|1024|1024|1024|1536|1536|1536|1536|
|Maximální velikost protokolu (GB)|307|307|307|461|461|461|461|
|Velikost databáze TempDB (GB)|64|128|192|256|320|384|384|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Vstupně-výstupní latence (přibližné)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|
|Cíl vstupně-výstupních operací (64 KB)|5000|10000|15 000|20000|25000|30000|35000|
|Maximální počet souběžných pracovních procesů (požadavků)|200|400|600|800|1000|1200|1400|
|Maximální povolené relace|30000|30000|30000|30000|30000|30000|30000|
|Počet replik|4|4|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Přečtěte si horizontální navýšení kapacity|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Zahrnuté úložiště zálohování|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Obchodní vrstvy služby: Výpočetní platforma běžící generace 5 (část 2)

|Vypočítat velikost|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |
|Generování H/W|5|5|5|5|5|5|5|
|Virtuální jádra|16|18|20|24|32|40|80|
|Paměť (GB)|81.6|91.8|102|122.4|163.2|204|408|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|15.768|18.139|20.51|25.252|37.936|52.22|131.64|
|Maximální velikost dat (GB)|3072|3072|3072|4 096|4 096|4 096|4 096|
|Maximální velikost protokolu (GB)|922|922|922|1229|1229|1229|1229|
|Velikost databáze TempDB (GB)|384|384|384|384|384|384|384|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Vstupně-výstupní latence (přibližné)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|
|Cíl vstupně-výstupních operací (64 KB)|40000|45000|50000|60000|80000|100000|200000|
|Maximální počet souběžných pracovních procesů (požadavků)|1600|1800|2000|2400|3200|4000|8000|
|Maximální povolené relace|30000|30000|30000|30000|30000|30000|30000|
|Počet replik|4|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|Ano|
|Přečtěte si horizontální navýšení kapacity|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Zahrnuté úložiště zálohování|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|

## <a name="hyperscale-service-tier-preview"></a>Úroveň služeb Hyperškálovatelného (preview)

### <a name="generation-4-compute-platform-storage-sizes-and-compute-sizes"></a>Výpočetní generace 4 platforma: Velikosti úložiště a výpočty velikostí

|Úroveň výkonu|HS_Gen4_1|HS_Gen4_2|HS_Gen4_4|HS_Gen4_8|HS_Gen4_16|HS_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |--: |
|Generování H/W|4|4|4|4|4|4|
|Virtuální jádra|1|2|4|8|16|24|
|Paměť (GB)|7|14|28|56|112|168|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Maximální velikost dat (TB)|100 |100 |100 |100 |100 |100 |
|Maximální velikost protokolu (TB)|1 |1 |1 |1 |1 |1 |
|Velikost databáze TempDB (GB)|32|64|128|256|384|384|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Cíl vstupně-výstupních operací (64 KB)|Chcete-li určit|Chcete-li určit|Chcete-li určit|Chcete-li určit|Chcete-li určit|Chcete-li určit|
|Vstupně-výstupní latence (přibližné)|Chcete-li určit|Chcete-li určit|Chcete-li určit|Chcete-li určit|Chcete-li určit|Chcete-li určit|
|Maximální počet souběžných pracovních procesů (požadavků)|200|400|800|1600|3200|4800|
|Maximální povolené relace|30000|30000|30000|30000|30000|30000|
|Počet replik|2|2|2|2|2|2|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Přečtěte si horizontální navýšení kapacity|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|7|7|7|7|7|7|
|||

### <a name="generation-5-compute-platform"></a>Výpočetní platforma běžící generace 5

|Úroveň výkonu|HS_Gen5_2|HS_Gen5_4|HS_Gen5_8|HS_Gen5_16|HS_Gen5_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |--: |
|Generování H/W|5|5|5|5|5|5|5|5|
|Virtuální jádra|2|4|8|16|24|32|40|80|
|Paměť (GB)|10.2|20.4|40.8|81.6|122.4|163.2|204|408|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Maximální velikost dat (TB)|100 |100 |100 |100 |100 |100 |100 |100 |
|Maximální velikost protokolu (TB)|1 |1 |1 |1 |1 |1 |1 |1 |
|Velikost databáze TempDB (GB)|64|128|256|384|384|384|384|384|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Cíl vstupně-výstupních operací (64 KB)|Chcete-li určit|Chcete-li určit|Chcete-li určit|Chcete-li určit|Chcete-li určit|Chcete-li určit|Chcete-li určit|Chcete-li určit|
|Vstupně-výstupní latence (přibližné)|Chcete-li určit|Chcete-li určit|Chcete-li určit|Chcete-li určit|Chcete-li určit|Chcete-li určit|Chcete-li určit|Chcete-li určit|
|Maximální počet souběžných pracovních procesů (požadavků)|200|400|800|1600|2400|3200|4000|8000|
|Maximální povolené relace|30000|30000|30000|30000|30000|30000|30000|30000|
|Počet replik|2|2|2|2|2|2|2|2|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Přečtěte si horizontální navýšení kapacity|Ano|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování (omezeno na verzi preview)|7|7|7|7|7|7|7|7|
|||

## <a name="next-steps"></a>Další postup

- Zobrazit [nejčastější dotazy k SQL Database](sql-database-faq.md) odpovědi na nejčastější dotazy.
- Informace o obecných omezeních Azure najdete v tématu [předplatného Azure a limity, kvóty a omezení](../azure-subscription-service-limits.md).
