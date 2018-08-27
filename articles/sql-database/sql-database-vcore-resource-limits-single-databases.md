---
title: Limity založený na virtuálních jádrech prostředků Azure SQL Database – izolované databáze | Dokumentace Microsoftu
description: Tato stránka popisuje některé běžné limity prostředků založený na virtuálních jádrech pro izolovanou databázi ve službě Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/24/2018
ms.author: carlrab
ms.openlocfilehash: 8e7af24133b7e034bb7bc1898609cebbb860b738
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42919074"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-a-single-database"></a>Založený na virtuálních jádrech zakoupení modelu omezení pro jednu databázi Azure SQL Database

Tento článek obsahuje podrobné prostředků limity pro izolované databáze Azure SQL Database pomocí nákupní model založený na virtuálních jádrech.

Založený na DTU nákupní model omezení najdete v tématu [omezení prostředků na základě DTU databáze SQL](sql-database-dtu-resource-limits.md).

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md).


## <a name="single-database-storage-sizes-and-performance-levels"></a>Izolované databáze: velikosti úložiště a úrovně výkonu

Pro izolované databáze následující tabulky popisují prostředky dostupné pro izolované databáze na každé úrovni úrovni a výkonu služby. Můžete nastavit úroveň služby, úroveň výkonu a velikost úložiště pro izolovanou databázi pomocí [webu Azure portal](sql-database-single-databases-manage.md#azure-portal-manage-logical-servers-and-databases), [příkazů jazyka Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-logical-servers-and-databases), [Powershellu](sql-database-single-databases-manage.md#powershell-manage-logical-servers-and-databases), [Rozhraní příkazového řádku azure](sql-database-single-databases-manage.md#azure-cli-manage-logical-servers-and-databases), nebo [rozhraní REST API](sql-database-single-databases-manage.md#rest-api-manage-logical-servers-and-databases).

### <a name="general-purpose-service-tier"></a>Obecné účely úrovně služeb

#### <a name="generation-4-compute-platform"></a>Výpočetní platforma běžící generace 4
|Úroveň výkonu|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Generování H/W|4|4|4|4|4|4|
|Virtuální jádra|1|2|4|8|16|24|
|Paměť (GB)|7|14|28|56|112|168|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Typ úložiště|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|
|Vstupně-výstupní latence (přibližné)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|
|Maximální velikost dat (GB)|1024|1024|1536|3072|4 096|4 096|
|Maximální velikost protokolu (GB)|307|307|461|922|1229|1229|
|Velikost databáze TempDB (GB)|32|64|128|256|384|384|
|Cíl vstupně-výstupních operací (64 KB)|500|1000|2000|4000|7000|7000|
|Maximální počet souběžných pracovních procesů (požadavků)|200|400|800|1600|3200|4800|
|Maximální povolené relace|30000|30000|30000|30000|30000|30000|
|Počet replik|1|1|1|1|1|1|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|000
|Přečtěte si horizontální navýšení kapacity|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Zahrnuté úložiště zálohování|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|
|||

#### <a name="generation-5-compute-platform"></a>Výpočetní platforma běžící generace 5
|Úroveň výkonu|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40| GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |
|Generování H/W|5|5|5|5|5|5|5|
|Virtuální jádra|2|4|8|16|24|32|40|80|
|Paměť (GB)|11|22|44|88|132|176|220|440|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Typ úložiště|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|Storage úrovně Premium (vzdálené)|
|Vstupně-výstupní latence (přibližné)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|5 – 7 ms (zápis)<br>5 až 10 ms (čtení)|
|Maximální velikost dat (GB)|1024|1024|1536|3072|4 096|4 096|4 096|4 096|
|Maximální velikost protokolu (GB)|307|307|461|614|1229|1229|1229|1229|
|Velikost databáze TempDB (GB)|64|128|256|384|384|384|384|384|
|Cíl vstupně-výstupních operací (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|Maximální počet souběžných pracovních procesů (požadavků)|200|400|800|1600|2400|3200|4000|8000|
|Maximální povolené relace|30000|30000|30000|30000|30000|30000|30000|30000|
|Počet replik|1|1|1|1|1|1|1|1|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Přečtěte si horizontální navýšení kapacity|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Zahrnuté úložiště zálohování|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|
|||

### <a name="business-critical-service-tier"></a>Obchodní vrstvy kritické služby

#### <a name="generation-4-compute-platform"></a>Výpočetní platforma běžící generace 4
|Úroveň výkonu|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generování H/W|4|4|4|4|4|4|
|Virtuální jádra|1|2|4|8|16|24|
|Paměť (GB)|7|14|28|56|112|168|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|1|2|4|8|20|36|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Maximální velikost dat (GB)|1024|1024|1024|1024|1024|1024|
|Maximální velikost protokolu (GB)|307|307|307|307|307|307|
|Velikost databáze TempDB (GB)|32|64|128|256|384|384|
|Cíl vstupně-výstupních operací (64 KB)|5000|10000|20000|40000|80000|120000|
|Vstupně-výstupní latence (přibližné)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|
|Maximální počet souběžných pracovních procesů (požadavků)|200|400|800|1600|3200|4800|
|Maximální povolené relace|30000|30000|30000|30000|30000|30000|
|Počet replik|3|3|3|3|3|3|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Přečtěte si horizontální navýšení kapacity|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|
|||

#### <a name="generation-5-compute-platform"></a>Výpočetní platforma běžící generace 5
|Úroveň výkonu|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |--: |--: |--: |--: |--: |
|Generování H/W|5|5|5|5|5|5|5|5|
|Virtuální jádra|2|4|8|16|24|32|40|80|
|Paměť (GB)|11|22|44|88|132|176|220|440|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|1.571|3,142|6.284|15.768|25.252|37.936|52.22|131.64|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Vstupně-výstupní latence (přibližné)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|1 až 2 ms (zápis)<br>1 až 2 ms (čtení)|
|Maximální velikost dat (GB)|1024|1024|1024|1024|2 048|4 096|4 096|4 096|
|Maximální velikost protokolu (GB)|307|307|307|307|614|1229|1229|1229|
|Velikost databáze TempDB (GB)|64|128|256|384|384|384|384|384|
|Cíl vstupně-výstupních operací (64 KB)|5000|10000|20000|40000|60000|80000|100000|200000
|Maximální počet souběžných pracovních procesů (požadavků)|200|400|800|1600|2400|3200|4000|8000|
|Maximální povolené relace|30000|30000|30000|30000|30000|30000|30000|30000|
|Počet replik|3|3|3|3|3|3|3|3|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Přečtěte si horizontální navýšení kapacity|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Zahrnuté úložiště zálohování|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|Velikost databáze X 1|
|||

## <a name="next-steps"></a>Další postup

- Zobrazit [nejčastější dotazy k SQL Database](sql-database-faq.md) odpovědi na nejčastější dotazy.
- Informace o obecných omezeních Azure najdete v tématu [předplatného Azure a limity, kvóty a omezení](../azure-subscription-service-limits.md).
