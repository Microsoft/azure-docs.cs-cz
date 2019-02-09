---
title: Limity založený na virtuálních jádrech prostředků Azure SQL Database – elastických fondů | Dokumentace Microsoftu
description: Tato stránka popisuje některé běžné prostředek založený na virtuálních jádrech limity pro elastické fondy Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 02/072019
ms.openlocfilehash: 4f025680c1c637f8b8475be2d5784d08e9e2343b
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55962005"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-based-purchasing-model-limits"></a>Limity pro elastické fondy pomocí omezení založený na virtuálních jádrech nákupní model prostředků

Tento článek obsahuje podrobné prostředků limity pro elastické fondy Azure SQL Database a databáze ve fondu pomocí nákupní model založený na virtuálních jádrech.

Založený na DTU nákupní model omezení najdete v tématu [omezení prostředků založený na DTU databáze SQL – elastických fondů](sql-database-dtu-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md).

Můžete nastavit úroveň služby, výpočetního prostředí a pomocí velikost úložiště [webu Azure portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), [rozhraní příkazového řádku Azure](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases), nebo [rozhraní REST API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!NOTE]
> Omezení prostředků jednotlivých databází v elastických fondech jsou obvykle stejné jako u izolovaných databází mimo fondy, které má stejnou výpočetní velikost. Maximální počet souběžných pracovních procesů pro databázi GP_Gen4_1 je například 200 pracovních procesů. Maximální počet souběžných pracovních procesů pro databáze ve fondu GP_Gen4_1 je tedy také 200 pracovních procesů. Všimněte si, že se celkový počet souběžných pracovních procesů ve fondu GP_Gen4_1 je 210.

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>Obecné účely vrstvy služby: Velikosti úložiště a výpočty velikostí

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Obecné účely vrstvy služby: Výpočetní platforma běžící generace 4 (část 1)

|Vypočítat velikost|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Generování H/W|4|4|4|4|4|4|
|Virtuální jádra|1|2|3|4|5|6|
|Paměť (GB)|7|14|21|28|35|42|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Maximální velikost dat (GB)|512|756|756|1536|1536|1536|
|Maximální velikost protokolu|154|227|227|461|461|461|
|Velikost databáze TempDB (GB)|32|64|96|128|160|192|
|Typ úložiště|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|
|Vstupně-výstupní latence (přibližné)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|
|Cíl vstupně-výstupních operací (64 KB)|500|1000|1 500|2000|2500|3000|
|Maximální počet souběžných pracovních procesů na fond (požadavky) * |210|420|630|840|1050|1260|
|Maximální povolené relace|30000|30000|30000|30000|30000|30000|
|Maximální počet databází pro každý fond|100|200|300|500|500|500|
|Volby pro elastické fondy – vCore min/max na databázi|0, 0.25, 0.5, 1|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|Počet replik|1|1|1|1|1|1|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Přečtěte si horizontální navýšení kapacity|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Zahrnuté úložiště zálohování|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|

\* Maximální počet aktuálních pracovních procesů (požadavků) jednotlivé databáze, najdete v části [jedno omezení prostředků databáze](sql-database-vcore-resource-limits-single-databases.md)

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Obecné účely vrstvy služby: Výpočetní platforma běžící generace 4 (část 2)

|Vypočítat velikost|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generování H/W|4|4|4|4|4|4|
|Virtuální jádra|7|8|9|10|16|24|
|Paměť (GB)|49|56|63|70|112|168|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Maximální velikost dat (GB)|1536|2 048|2 048|2 048|3584|4 096|
|Maximální velikost protokolu (GB)|461|614|614|614|1075|1229|
|Velikost databáze TempDB (GB)|224|256|288|320|384|384|
|Typ úložiště|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|
|Vstupně-výstupní latence (přibližné)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|
|Cíl vstupně-výstupních operací (64 KB)|3500|4000|4500|5000|7000|7000|
|Maximální počet souběžných pracovních procesů na fond (požadavky) *|1470|1680|1890|2 100|3360|5040|
|Maximální povolené relace|30000|30000|30000|30000|30000|30000|
|Maximální počet databází pro každý fond|200|500|500|500|500|500|
|Volby pro elastické fondy – vCore min/max na databázi|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|Počet replik|1|1|1|1|1|1|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Přečtěte si horizontální navýšení kapacity|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Zahrnuté úložiště zálohování|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|

\* Maximální počet aktuálních pracovních procesů (požadavků) jednotlivé databáze, najdete v části [jedno omezení prostředků databáze](sql-database-vcore-resource-limits-single-databases.md)

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Obecné účely vrstvy služby: Výpočetní platforma běžící generace 5 (část 1)

|Vypočítat velikost|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Generování H/W|5|5|5|5|5|5|5|
|Virtuální jádra|2|4|6|8|10|12|14|
|Paměť (GB)|10.2|20.4|30.6|40.8|51|61.2|71.4|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Maximální velikost dat (GB)|512|756|756|1536|1536|1536|
|Maximální velikost protokolu (GB)|154|227|227|461|461|461|461|
|Velikost databáze TempDB (GB)|64|128|192|256|320|384|384|
|Typ úložiště|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|
|Vstupně-výstupní latence (přibližné)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|
|Cíl vstupně-výstupních operací (64 KB)|500|1000|1 500|2000|2500|3000|3500|
|Maximální počet souběžných pracovních procesů na fond (požadavky) *|210|420|630|840|1050|1260|1470|
|Maximální povolené relace|30000|30000|30000|30000|30000|30000|30000|
|Maximální počet databází pro každý fond|200|500|500|500|500|500|500|
|Volby pro elastické fondy – vCore min/max na databázi|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|Počet replik|1|1|1|1|1|1|1|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Přečtěte si horizontální navýšení kapacity|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Zahrnuté úložiště zálohování|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|

\* Maximální počet aktuálních pracovních procesů (požadavků) jednotlivé databáze, najdete v části [jedno omezení prostředků databáze](sql-database-vcore-resource-limits-single-databases.md)

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Obecné účely vrstvy služby: Výpočetní platforma běžící generace 5 (část 2)

|Vypočítat velikost|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Generování H/W|5|5|5|5|5|5|5|
|Virtuální jádra|16|18|20|24|32|40|80|
|Paměť (GB)|81.6|91.8|102|122.4|163.2|204|408|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Maximální velikost dat (GB)|2 048|2 048|3072|3072|4 096|4 096|4 096|
|Maximální velikost protokolu (GB)|614|614|922|922|1229|1229|1229|
|Velikost databáze TempDB (GB)|384|384|384|384|384|384|384|
|Typ úložiště|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|
|Vstupně-výstupní latence (přibližné)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|
|Cíl vstupně-výstupních operací (64 KB)|4000|4500|5000|6000|7000|7000|7000|
|Maximální počet souběžných pracovních procesů na fond (požadavky) *|1680|1890|2 100|2520|33600|4200|8400|
|Maximální počet databází pro každý fond|500|500|500|500|500|500|500|
|Volby pro elastické fondy – vCore min/max na databázi|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...16, 24, 32, 40|0, 0.25, 0.5, 1...16, 24, 32, 40, 80|
|Počet replik|1|1|1|1|1|1|1|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Přečtěte si horizontální navýšení kapacity|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Zahrnuté úložiště zálohování|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|

\* Maximální počet aktuálních pracovních procesů (požadavků) jednotlivé databáze, najdete v části [jedno omezení prostředků databáze](sql-database-vcore-resource-limits-single-databases.md)

## <a name="business-critical-service-tier-storage-sizes-and-compute-sizes"></a>Obchodní vrstvy služby: Velikosti úložiště a výpočty velikostí

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Obchodní vrstvy služby: Výpočetní platforma běžící generace 4 (část 1)

|Vypočítat velikost|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Generování H/W|4|4|4|4|4|4|
|Virtuální jádra|1|2|3|4|5|6|
|Paměť (GB)|10.2|20.4|30.6|40.8|51|61.2|71.4|
|Podpora Columnstore|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Úložiště OLTP v paměti (GB)|1|2|3|4|5|6|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Maximální velikost dat (GB)|1024|1024|1024|1024|1024|1024|
|Maximální velikost protokolu (GB)|307|307|307|307|307|307|
|Velikost databáze TempDB (GB)|32|64|96|128|160|192|
|Vstupně-výstupní latence (přibližné)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|
|Cíl vstupně-výstupních operací (64 KB)|5000|10000|15 000|20000|25000|30000|
|Maximální počet souběžných pracovních procesů na fond (požadavky) *|210|420|630|840|1050|1260|
|Maximální povolené relace|30000|30000|30000|30000|30000|30000|
|Maximální počet databází pro každý fond|Pro tato velikost výpočetních jsou podporovány pouze jedné databáze|50|100|100|100|100|
|Volby pro elastické fondy – vCore min/max na databázi|neuvedeno|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|Počet replik|4|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|Ano|
|Přečtěte si horizontální navýšení kapacity|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|

\* Maximální počet aktuálních pracovních procesů (požadavků) jednotlivé databáze, najdete v části [jedno omezení prostředků databáze](sql-database-vcore-resource-limits-single-databases.md)

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Obchodní vrstvy služby: Výpočetní platforma běžící generace 4 (část 2)

|Vypočítat velikost|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generování H/W|4|4|4|4|4|4|
|Virtuální jádra|7|8|9|10|16|24|
|Paměť (GB)|81.6|91.8|102|122.4|163.2|204|408|
|Podpora Columnstore|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Úložiště OLTP v paměti (GB)|7|8|9.5|11|20|36|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Maximální velikost dat (GB)|1024|1024|1024|1024|1024|1024|
|Maximální velikost protokolu (GB)|307|307|307|307|307|307|
|Velikost databáze TempDB (GB)|224|256|288|320|384|384|
|Vstupně-výstupní latence (přibližné)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|
|Cíl vstupně-výstupních operací (64 KB)|35000|40000|45000|50000|80000|120000|
|Maximální počet souběžných pracovních procesů na fond (požadavky) *|1470|1680|1890|2 100|3360|5040|
|Maximální povolené relace|30000|30000|30000|30000|30000|30000|
|Maximální počet databází pro každý fond|100|100|100|100|100|100|
|Volby pro elastické fondy – vCore min/max na databázi|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|Počet replik|4|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|Ano|
|Přečtěte si horizontální navýšení kapacity|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|

\* Maximální počet aktuálních pracovních procesů (požadavků) jednotlivé databáze, najdete v části [jedno omezení prostředků databáze](sql-database-vcore-resource-limits-single-databases.md)

#### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Obchodní vrstvy služby: Výpočetní platforma běžící generace 5 (část 1)

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
|Maximální počet souběžných pracovních procesů na fond (požadavky) *|210|420|630|840|1050|1260|1470|
|Maximální povolené relace|30000|30000|30000|30000|30000|30000|30000|
|Maximální počet databází pro každý fond|Pro tato velikost výpočetních jsou podporovány pouze jedné databáze|50|100|100|100|100|100|
|Volby pro elastické fondy – vCore min/max na databázi|neuvedeno|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|Počet replik|4|4|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|Ano|
|Přečtěte si horizontální navýšení kapacity|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|

\* Maximální počet aktuálních pracovních procesů (požadavků) jednotlivé databáze, najdete v části [jedno omezení prostředků databáze](sql-database-vcore-resource-limits-single-databases.md)

#### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Obchodní vrstvy služby: Výpočetní platforma běžící generace 5 (část 2)

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
|Maximální počet souběžných pracovních procesů na fond (požadavky) *|1680|1890|2 100|2520|3360|4200|8400|
|Maximální povolené relace|30000|30000|30000|30000|30000|30000|30000|
|Maximální počet databází pro každý fond|100|100|100|100|100|100|100|
|Volby pro elastické fondy – vCore min/max na databázi|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...20, 24, 32, 40|0, 0.25, 0.5, 1...20, 24, 32, 40, 80|
|Počet replik|4|4|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|Ano|
|Přečtěte si horizontální navýšení kapacity|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|

\* Maximální počet aktuálních pracovních procesů (požadavků) jednotlivé databáze, najdete v části [jedno omezení prostředků databáze](sql-database-vcore-resource-limits-single-databases.md)

Pokud se všechny virtuální jádra pro elastický fond je zaneprázdněný, každá databáze ve fondu obdrží stejné množství výpočetních prostředků pro zpracování dotazů. Služba SQL Database poskytuje spravedlivé sdílení prostředků mezi databázemi tím, že zajišťuje rovnoměrné rozdělení výpočetního času. Spravedlivé sdílení prostředků elastického fondu je kromě libovolným množstvím prostředků jinak zaručena pro každou databázi, pokud je minimální počet virtuálních jader na databázi nastaven na nenulovou hodnotu.

## <a name="database-properties-for-pooled-databases"></a>Vlastnosti databáze pro databáze ve fondu

Následující tabulka popisuje vlastnosti pro databáze ve fondu.

| Vlastnost | Popis |
|:--- |:--- |
| Maximální počet virtuálních jader na databázi |Maximální počet virtuálních jader, které může použít libovolnou databázi ve fondu, pokud jsou dostupné v závislosti na využití ostatními databázemi ve fondu. Maximální počet virtuálních jader na databázi není garancí prostředků pro databázi. Toto nastavení je globální a platí pro všechny databáze ve fondu. Nastavit maximální počet virtuálních jader na databázi dostatečně vysoký, aby se pro zpracování špičky využití databáze. Určitý stupeň over-pass-the potvrzování očekává se, protože fond obecně předpokládá využití horké a studené vzory pro databáze kde nejsou všechny databáze peaking současně.|
| Min virtuálních jader na databázi |Minimální počet virtuálních jader, které je zaručeno, že všechny databáze ve fondu. Toto nastavení je globální a platí pro všechny databáze ve fondu. Min virtuálních jader na databázi může být nastaven na hodnotu 0 a výchozí hodnota je také. Tato vlastnost nastavena na libovolné místo mezi 0 a využití průměrné virtuálních jader na databázi. Součinu počtu databází ve fondu a minimální virtuálních jader na databázi nesmí přesáhnout virtuálních jader na fond.|
| Max. úložiště na databázi |Maximální velikosti databáze nastavena podle uživatele pro databázi ve fondu. Databáze ve fondu sdílejí úložiště přidělené fondu, proto je velikost databáze můžete oslovit omezena na menší zbývající úložiště fondu a velikost databáze. Maximální velikost databáze odkazuje na maximální velikost datových souborů a nezahrnuje místo, které využívají soubory protokolu. |
|||

## <a name="next-steps"></a>Další postup

- VCore omezení prostředků pro izolovanou databázi, naleznete v tématu [omezení prostředků pro izolované databáze pomocí nákupní model založený na virtuálních jádrech](sql-database-vcore-resource-limits-single-databases.md)
- Omezení prostředků DTU pro izolovanou databázi, naleznete v tématu [omezení prostředků pro izolované databáze pomocí nákupní model založený na DTU](sql-database-dtu-resource-limits-single-databases.md)
- Omezení prostředků DTU pro elastické fondy najdete v tématu [limity pro elastické fondy pomocí nákupní model založený na DTU prostředků](sql-database-dtu-resource-limits-elastic-pools.md)
- Omezení prostředků pro spravované instance najdete v tématu [managed instance omezení prostředků](sql-database-managed-instance-resource-limits.md).
- Informace o obecných omezeních Azure najdete v tématu [předplatného Azure a limity, kvóty a omezení](../azure-subscription-service-limits.md).
- Informace o omezení prostředků na databázovém serveru najdete v tématu [přehled omezení prostředků na serveru služby SQL Database](sql-database-resource-limits-database-server.md) informace o omezeních na úrovni serveru a předplatné.