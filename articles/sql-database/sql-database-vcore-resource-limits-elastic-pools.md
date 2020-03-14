---
title: omezení prostředků vCore – elastické fondy
description: Tato stránka popisuje některé běžné limity vCore prostředků pro elastické fondy v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab, sstein
ms.date: 03/03/2020
ms.openlocfilehash: a6186753c845070ff2a5b3a3f8c6ff0de51e52f0
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255701"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-purchasing-model"></a>Omezení prostředků pro elastické fondy pomocí modelu nákupu vCore

Tento článek poskytuje podrobné omezení prostředků pro Azure SQL Database elastické fondy a databáze ve fondu pomocí modelu nákupu vCore.

Limity nákupních modelů pro DTU najdete v tématu [SQL Database omezení prostředků DTU – elastické fondy](sql-database-dtu-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [Správa prostoru souborů v Azure SQL Database](sql-database-file-space-management.md).

Úroveň služby, výpočetní velikost a množství úložiště můžete nastavit pomocí [Azure Portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShellu](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), rozhraní příkazového [řádku Azure](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases)nebo [REST API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!IMPORTANT]
> Pokyny a požadavky pro škálování najdete v tématu [škálování elastického fondu](sql-database-elastic-pool-scale.md) .

## <a name="general-purpose---provisioned-compute---gen4"></a>Pro obecné účely zřízené COMPUTE – COMPUTE GEN4 –

> [!IMPORTANT]
> Nové databáze COMPUTE GEN4 – již nejsou podporovány v oblastech Austrálie – východ a Brazílie – jih.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Úroveň služby pro obecné účely: výpočetní platforma generace 4 (část 1)

|Velikost výpočetního prostředí|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Generování výpočtů|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|
|virtuální jádra|1|2|3|4|5|6|
|Paměť (GB)|7|14|21|28|35|42|
|Maximální počet databází pro každý fond|100|200|500|500|500|500|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Maximální velikost dat (GB)|512|756|1536|1536|1536|2048|
|Maximální velikost protokolu|154|227|461|461|461|614|
|Maximální velikost dat databáze TempDB (GB)|32|64|96|128|160|192|
|Typ úložiště|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|
|Vstupně-výstupní latence (přibližné)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet datových IOPS na fond *|400|800|1200|1600|2000|2400|
|Maximální počet protokolů na fond (MB/s)|4.7|9,4|14,1|18,8|23,4|28,1|
|Maximální počet souběžných pracovních procesů na fond (požadavky) * * |210|420|630|840|1050|1260|
|Maximální počet souběžných přihlášení na fond * * |210|420|630|840|1050|1260|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|Počet replik|1|1|1|1|1|1|
|Více AZ|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Škálování čtení|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* maximální hodnotu velikosti v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](sql-database-resource-limits-database-server.md#resource-governance).

\*\* maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi najdete v části [omezení prostředků databáze na jednu databázi](sql-database-vcore-resource-limits-single-databases.md). Pokud například elastický fond používá Gen5 a maximální počet vCore na databázi je nastaven na hodnotu 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi nastavený na 0,5, pak je hodnota maximální počet souběžných pracovních procesů 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na vCore. Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Úroveň služby pro obecné účely: výpočetní platforma generace 4 (část 2)

|Velikost výpočetního prostředí|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generování výpočtů|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|
|virtuální jádra|7|8|9|10|16|24|
|Paměť (GB)|49|56|63|70|112|159,5|
|Maximální počet databází pro každý fond|500|500|500|500|500|500|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Maximální velikost dat (GB)|2048|2048|2048|2048|3584|4 096|
|Maximální velikost protokolu (GB)|614|614|614|614|1075|1229|
|Maximální velikost dat databáze TempDB (GB)|224|256|288|320|512|768|
|Typ úložiště|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|
|Vstupně-výstupní latence (přibližné)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet datových IOPS na fond *|2800|3 200|3600|4 000|6 400|9600|
|Maximální počet protokolů na fond (MB/s)|32,8|37,5|37,5|37,5|37,5|37,5|
|Maximální počet souběžných pracovních procesů na fond (požadavky) *|1470|1680|1890|2100|3360|5040|
|Maximální počet souběžných přihlášení ve fondu (požadavky) *|1470|1680|1890|2100|3360|5040|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|Počet replik|1|1|1|1|1|1|
|Více AZ|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Škálování čtení|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi najdete v části [omezení prostředků databáze na jednu databázi](sql-database-vcore-resource-limits-single-databases.md). Pokud například elastický fond používá Gen5 a maximální počet vCore na databázi je nastaven na hodnotu 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi nastavený na 0,5, pak je hodnota maximální počet souběžných pracovních procesů 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na vCore. Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

## <a name="general-purpose---provisioned-compute---gen5"></a>Pro obecné účely zřízené COMPUTE – Gen5

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Úroveň služby pro obecné účely: generace 5 COMPUTE Platform (část 1)

|Velikost výpočetního prostředí|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|virtuální jádra|2|4|6|8|10|12|14|
|Paměť (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Maximální počet databází pro každý fond|100|200|500|500|500|500|500|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Maximální velikost dat (GB)|512|756|1536|1536|1536|2048|2048|
|Maximální velikost protokolu (GB)|154|227|461|461|461|614|614|
|Maximální velikost dat databáze TempDB (GB)|64|128|192|256|320|384|448|
|Typ úložiště|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|
|Vstupně-výstupní latence (přibližné)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet datových IOPS na fond *|800|1600|2400|3 200|4 000|4800|5600|
|Maximální počet protokolů na fond (MB/s)|9,4|18,8|28,1|37,5|37,5|37,5|37,5|
|Maximální počet souběžných pracovních procesů na fond (požadavky) * *|210|420|630|840|1050|1260|1470|
|Maximální počet souběžných přihlášení na fond (požadavky) * *|210|420|630|840|1050|1260|1470|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0, 0,25, 0,5, 1, 2|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|Počet replik|1|1|1|1|1|1|1|
|Více AZ|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Škálování čtení|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* maximální hodnotu velikosti v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](sql-database-resource-limits-database-server.md#resource-governance).

\*\* maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi najdete v části [omezení prostředků databáze na jednu databázi](sql-database-vcore-resource-limits-single-databases.md). Pokud například elastický fond používá Gen5 a maximální počet vCore na databázi je nastaven na hodnotu 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi nastavený na 0,5, pak je hodnota maximální počet souběžných pracovních procesů 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na vCore. Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Úroveň služby pro obecné účely: generace 5 COMPUTE Platform (část 2)

|Velikost výpočetního prostředí|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|virtuální jádra|16|18|20|24|32|40|80|
|Paměť (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Maximální počet databází pro každý fond|500|500|500|500|500|500|500|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Maximální velikost dat (GB)|2048|3072|3072|3072|4 096|4 096|4 096|
|Maximální velikost protokolu (GB)|614|922|922|922|1229|1229|1229|
|Maximální velikost dat databáze TempDB (GB)|512|576|640|768|1024|1280|2560|
|Typ úložiště|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|
|Vstupně-výstupní latence (přibližné)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet datových IOPS na fond * |6,400|7 200|8 000|9 600|12,800|16,000|32,000|
|Maximální počet protokolů na fond (MB/s)|37,5|37,5|37,5|37,5|37,5|37,5|37,5|
|Maximální počet souběžných pracovních procesů na fond (požadavky) * *|1680|1890|2100|2520|3360|4200|8400|
|Maximální počet souběžných přihlášení na fond (požadavky) * *|1680|1890|2100|2520|3360|4200|8400|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...16, 24, 32, 40|0, 0.25, 0.5, 1...16, 24, 32, 40, 80|
|Počet replik|1|1|1|1|1|1|1|
|Více AZ|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Škálování čtení|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* maximální hodnotu velikosti v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](sql-database-resource-limits-database-server.md#resource-governance).

\*\* maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi najdete v části [omezení prostředků databáze na jednu databázi](sql-database-vcore-resource-limits-single-databases.md). Pokud například elastický fond používá Gen5 a maximální počet vCore na databázi je nastaven na hodnotu 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi nastavený na 0,5, pak je hodnota maximální počet souběžných pracovních procesů 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na vCore. Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Pro obecné účely zřízené COMPUTE-Fsv2-Series

### <a name="fsv2-series-compute-generation-preview"></a>Generování výpočetních prostředků Fsv2-Series (Preview)

|Velikost výpočetního prostředí|GP_Fsv2_72|
|:--- | --: |
|Generování výpočtů|Řada Fsv2|
|virtuální jádra|72|
|Paměť (GB)|136,2|
|Maximální počet databází pro každý fond|500|
|Podpora columnstore|Ano|
|Úložiště OLTP v paměti (GB)|NEUŽÍVÁ SE.|
|Maximální velikost dat (GB)|4 096|
|Maximální velikost protokolu (GB)|1024|
|Maximální velikost dat databáze TempDB (GB)|333|
|Typ úložiště|Úložiště úrovně Premium (vzdálené)|
|Vstupně-výstupní latence (přibližné)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet datových IOPS na fond *|16,000|
|Maximální počet protokolů na fond (MB/s)|37,5|
|Maximální počet souběžných pracovních procesů na fond (požadavky) * *|3780|
|Maximální počet souběžných přihlášení na fond (požadavky) * *|3780|
|Maximální počet souběžných relací|30,000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0-72|
|Počet replik|1|
|Více AZ|NEUŽÍVÁ SE.|
|Škálování čtení|NEUŽÍVÁ SE.|
|Zahrnuté úložiště zálohování|velikost databáze 1X|

\* maximální hodnotu velikosti v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](sql-database-resource-limits-database-server.md#resource-governance).

\*\* maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi najdete v části [omezení prostředků databáze na jednu databázi](sql-database-vcore-resource-limits-single-databases.md). Pokud například elastický fond používá Gen5 a maximální počet vCore na databázi je nastaven na hodnotu 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi nastavený na 0,5, pak je hodnota maximální počet souběžných pracovních procesů 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na vCore. Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

## <a name="business-critical---provisioned-compute---gen4"></a>Uživatelsky důležité – zřízené COMPUTE – COMPUTE GEN4 –

> [!IMPORTANT]
> Nové databáze COMPUTE GEN4 – již nejsou podporovány v oblastech Austrálie – východ a Brazílie – jih.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Podniková úroveň služeb: výpočetní platforma generace 4 (část 1)

|Velikost výpočetního prostředí|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Generování výpočtů|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|
|virtuální jádra|2|3|4|5|6|
|Paměť (GB)|14|21|28|35|42|
|Maximální počet databází pro každý fond|50|100|100|100|100|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|2|3|4|5|6|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Maximální velikost dat (GB)|1024|1024|1024|1024|1024|
|Maximální velikost protokolu (GB)|307|307|307|307|307|
|Maximální velikost dat databáze TempDB (GB)|64|96|128|160|192|
|Vstupně-výstupní latence (přibližné)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální počet datových IOPS na fond *|9 000|13 500|18 000|22 500|27 000|
|Maximální počet protokolů na fond (MB/s)|20|30|40|50|60|
|Maximální počet souběžných pracovních procesů na fond (požadavky) * *|420|630|840|1050|1260|
|Maximální počet souběžných přihlášení na fond (požadavky) * *|420|630|840|1050|1260|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0, 0,25, 0,5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|Počet replik|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* maximální hodnotu velikosti v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](sql-database-resource-limits-database-server.md#resource-governance).

\*\* maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi najdete v části [omezení prostředků databáze na jednu databázi](sql-database-vcore-resource-limits-single-databases.md). Pokud například elastický fond používá Gen5 a maximální počet vCore na databázi je nastaven na hodnotu 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi nastavený na 0,5, pak je hodnota maximální počet souběžných pracovních procesů 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na vCore. Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Podniková úroveň služeb: výpočetní platforma generace 4 (část 2)

|Velikost výpočetního prostředí|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generování výpočtů|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|
|virtuální jádra|7|8|9|10|16|24|
|Paměť (GB)|49|56|63|70|112|159,5|
|Maximální počet databází pro každý fond|100|100|100|100|100|100|
|Podpora columnstore|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|NEUŽÍVÁ SE.|
|Úložiště OLTP v paměti (GB)|7|8|9.5|11|20|36|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Maximální velikost dat (GB)|1024|1024|1024|1024|1024|1024|
|Maximální velikost protokolu (GB)|307|307|307|307|307|307|
|Maximální velikost dat databáze TempDB (GB)|224|256|288|320|512|768|
|Vstupně-výstupní latence (přibližné)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální počet datových IOPS na fond *|31 500|36 000|40 500|45,000|72 000|96 000|
|Maximální počet protokolů na fond (MB/s)|70|80|80|80|80|80|
|Maximální počet souběžných pracovních procesů na fond (požadavky) * *|1470|1680|1890|2100|3360|5040|
|Maximální počet souběžných přihlášení na fond (požadavky) * *|1470|1680|1890|2100|3360|5040|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|Počet replik|4|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* maximální hodnotu velikosti v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](sql-database-resource-limits-database-server.md#resource-governance).

\*\* maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi najdete v části [omezení prostředků databáze na jednu databázi](sql-database-vcore-resource-limits-single-databases.md). Pokud například elastický fond používá Gen5 a maximální počet vCore na databázi je nastaven na hodnotu 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi nastavený na 0,5, pak je hodnota maximální počet souběžných pracovních procesů 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na vCore. Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

## <a name="business-critical---provisioned-compute---gen5"></a>Uživatelsky důležité – zřízené COMPUTE – Gen5

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Podniková úroveň služeb: výpočetní platforma generace 5 (část 1)

|Velikost výpočetního prostředí|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|virtuální jádra|4|6|8|10|12|14|
|Paměť (GB)|20,8|31,1|41,5|51,9|62,3|72,7|
|Maximální počet databází pro každý fond|50|100|100|100|100|100|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|3,14|4,71|6,28|8,65|11,02|13,39|
|Maximální velikost dat (GB)|1024|1536|1536|1536|3072|3072|
|Maximální velikost protokolu (GB)|307|307|461|461|922|922|
|Maximální velikost dat databáze TempDB (GB)|128|192|256|320|384|448|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Vstupně-výstupní latence (přibližné)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální počet datových IOPS na fond *|18 000|27 000|36 000|45,000|54,000|63 000|
|Maximální počet protokolů na fond (MB/s)|60|90|120|120|120|120|
|Maximální počet souběžných pracovních procesů na fond (požadavky) * *|420|630|840|1050|1260|1470|
|Maximální počet souběžných přihlášení na fond (požadavky) * *|420|630|840|1050|1260|1470|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|Počet replik|4|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* maximální hodnotu velikosti v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](sql-database-resource-limits-database-server.md#resource-governance).

\*\* maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi najdete v části [omezení prostředků databáze na jednu databázi](sql-database-vcore-resource-limits-single-databases.md). Pokud například elastický fond používá Gen5 a maximální počet vCore na databázi je nastaven na hodnotu 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi nastavený na 0,5, pak je hodnota maximální počet souběžných pracovních procesů 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na vCore. Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Podniková úroveň služeb: výpočetní platforma generace 5 (část 2)

|Velikost výpočetního prostředí|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|virtuální jádra|16|18|20|24|32|40|80|
|Paměť (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Maximální počet databází pro každý fond|100|100|100|100|100|100|100|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|15,77|18,14|20,51|25,25|37,94|52,23|131,68|
|Maximální velikost dat (GB)|3072|3072|3072|4 096|4 096|4 096|4 096|
|Maximální velikost protokolu (GB)|922|922|922|1229|1229|1229|1229|
|Maximální velikost dat databáze TempDB (GB)|512|576|640|768|1024|1280|2560|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Vstupně-výstupní latence (přibližné)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální počet datových IOPS na fond *|72 000|81 000|90,000|108 000|144 000|180,000|256 000|
|Maximální počet protokolů na fond (MB/s)|120|120|120|120|120|120|120|
|Maximální počet souběžných pracovních procesů na fond (požadavky) * *|1680|1890|2100|2520|3360|4200|8400|
|Maximální počet souběžných přihlášení na fond (požadavky) * *|1680|1890|2100|2520|3360|4200|8400|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...20, 24, 32, 40|0, 0.25, 0.5, 1...20, 24, 32, 40, 80|
|Počet replik|4|4|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* maximální hodnotu velikosti v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](sql-database-resource-limits-database-server.md#resource-governance).

\*\* maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi najdete v části [omezení prostředků databáze na jednu databázi](sql-database-vcore-resource-limits-single-databases.md). Pokud například elastický fond používá Gen5 a maximální počet vCore na databázi je nastaven na hodnotu 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi nastavený na 0,5, pak je hodnota maximální počet souběžných pracovních procesů 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na vCore. Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

## <a name="business-critical---provisioned-compute---m-series"></a>Podnikové kritické pro výpočetní prostředí-M-Series

### <a name="m-series-compute-generation-preview"></a>Generování výpočetních prostředků řady M-Series (Preview)

|Velikost výpočetního prostředí|BC_M_128|
|:--- | --: |
|Generování výpočtů|Řada M|
|virtuální jádra|128|
|Paměť (GB)|3767,1|
|Maximální počet databází pro každý fond|100|
|Podpora columnstore|Ano|
|Úložiště OLTP v paměti (GB)|1768|
|Maximální velikost dat (GB)|4 096|
|Maximální velikost protokolu (GB)|2048|
|Maximální velikost dat databáze TempDB (GB)|4 096|
|Typ úložiště|Místní disk SSD|
|Vstupně-výstupní latence (přibližné)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální počet datových IOPS na fond *|200 000|
|Maximální počet protokolů na fond (MB/s)|333|
|Maximální počet souběžných pracovních procesů na fond (požadavky) *|13 440|
|Maximální počet souběžných přihlášení na fond (požadavky) *|13 440|
|Maximální počet souběžných relací|30,000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0-128|
|Počet replik|4|
|Více AZ|Ano|
|Škálování čtení|Ano|
|Zahrnuté úložiště zálohování|velikost databáze 1X|

\* maximální hodnotu velikosti v/v v rozsahu od 8 KB do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](sql-database-resource-limits-database-server.md#resource-governance).

\*\* maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi najdete v části [omezení prostředků databáze na jednu databázi](sql-database-vcore-resource-limits-single-databases.md). Pokud například elastický fond používá Gen5 a maximální počet vCore na databázi je nastaven na hodnotu 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi nastavený na 0,5, pak je hodnota maximální počet souběžných pracovních procesů 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na vCore. Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

Pokud jsou všechny virtuální jádra elastického fondu zaneprázdněné, pak každá databáze ve fondu obdrží stejné množství výpočetních prostředků pro zpracování dotazů. Služba SQL Database poskytuje spravedlivé sdílení prostředků mezi databázemi tím, že zajišťuje rovnoměrné rozdělení výpočetního času. V případě, že je vCore min na databázi nastavená na nenulovou hodnotu, je pro každou databázi navíc zaručená velikost sdílení prostředků elastického fondu.

## <a name="database-properties-for-pooled-databases"></a>Vlastnosti databáze pro sdružené databáze

Následující tabulka popisuje vlastnosti pro databáze ve fondu.

> [!NOTE]
> Omezení prostředků jednotlivých databází v elastických fondech jsou obecně stejná jako u samostatných databází mimo fondy, které mají stejnou výpočetní velikost. Například maximální počet souběžných pracovních procesů pro databázi GP_Gen4_1 je 200 pracovních procesů. Maximální počet souběžných pracovních procesů pro databázi ve fondu GP_Gen4_1 je také 200 pracovních procesů. Všimněte si, že celkový počet souběžných pracovních procesů ve fondu GP_Gen4_1 je 210.

| Vlastnost | Popis |
|:--- |:--- |
| Maximální počet virtuální jádra na databázi |Maximální počet virtuální jádra, které mohou využívat všechny databáze ve fondu, pokud jsou k dispozici na základě využití jinými databázemi ve fondu. Maximální počet virtuální jádra na databázi není záruka prostředků pro databázi. Toto nastavení je globální a platí pro všechny databáze ve fondu. Nastavte maximální počet virtuální jádra na databázi dostatečně vysoký, aby bylo možné zpracovávat špičky ve využití databáze. Očekává se, že určitý stupeň překročení je očekávaný, protože fond obecně předpokládá použití vzorců za horkou a studenou databází, ve kterých se všechny databáze současně nešpičkují.|
| Minimální počet virtuální jádra na databázi |Minimální počet virtuální jádra, které jsou zaručené u všech databází ve fondu. Toto nastavení je globální a platí pro všechny databáze ve fondu. Minimální počet virtuální jádra na databázi může být nastaven na hodnotu 0 a také na výchozí hodnotu. Tato vlastnost je nastavena na hodnotu odkudkoli mezi 0 a průměrným využitím virtuální jádra na databázi. Součin počtu databází ve fondu a minimální virtuální jádra na databázi nemůže překročit virtuální jádra na fond.|
| Maximální velikost úložiště na databázi |Maximální velikost databáze nastavená uživatelem pro databázi ve fondu. Databáze ve fondu sdílejí přidělené úložiště fondu, takže velikost databáze může dosáhnout je omezená na menší zbývající velikost úložiště fondu a velikosti databáze. Maximální velikost databáze odkazuje na maximální velikost datových souborů a nezahrnuje místo využívané soubory protokolu. |
|||

## <a name="next-steps"></a>Další kroky

- Omezení prostředků vCore pro izolovanou databázi najdete v tématu [omezení prostředků pro izolované databáze pomocí modelu nákupu Vcore](sql-database-vcore-resource-limits-single-databases.md) .
- Omezení prostředků DTU pro jednu databázi najdete v tématu [omezení prostředků pro izolované databáze pomocí modelu nákupu DTU](sql-database-dtu-resource-limits-single-databases.md) .
- Omezení prostředků DTU pro elastické fondy najdete v tématu [omezení prostředků pro elastické fondy pomocí modelu nákupu DTU](sql-database-dtu-resource-limits-elastic-pools.md) .
- Omezení prostředků pro spravované instance najdete v tématu [omezení prostředků spravované instance](sql-database-managed-instance-resource-limits.md).
- Informace o obecných omezeních Azure najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Informace o omezeních prostředků na databázovém serveru najdete v tématu [Přehled omezení prostředků na serveru SQL Database](sql-database-resource-limits-database-server.md) , kde najdete informace o omezeních na úrovni serveru a předplatného.
