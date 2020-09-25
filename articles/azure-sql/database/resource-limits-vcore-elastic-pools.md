---
title: Limity prostředků virtuálních jader elastického fondu
description: Tato stránka popisuje některé běžné limity vCore prostředků pro elastické fondy v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 07/21/2020
ms.openlocfilehash: 345fd0df5b56bac164e78a540f8b26221294eabe
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325291"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-purchasing-model"></a>Omezení prostředků pro elastické fondy pomocí modelu nákupu vCore
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Tento článek poskytuje podrobné omezení prostředků pro Azure SQL Database elastické fondy a databáze ve fondu pomocí modelu nákupu vCore.

Limity nákupních modelů pro DTU najdete v tématu [SQL Database omezení prostředků DTU – elastické fondy](resource-limits-dtu-elastic-pools.md).

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi a uvolnit nevyužité místo. Další informace najdete v tématu [Správa prostoru souborů v Azure SQL Database](file-space-manage.md).

Můžete nastavit úroveň služby, výpočetní velikost (cíl služby) a množství úložiště pomocí [Azure Portal](elastic-pool-manage.md#azure-portal), [PowerShellu](elastic-pool-manage.md#powershell), rozhraní příkazového [řádku Azure](elastic-pool-manage.md#azure-cli)nebo [REST API](elastic-pool-manage.md#rest-api).

> [!IMPORTANT]
> Pokyny a požadavky pro škálování najdete v tématu [škálování elastického fondu](elastic-pool-scale.md).

## <a name="general-purpose---provisioned-compute---gen4"></a>Pro obecné účely zřízené COMPUTE – COMPUTE GEN4 –

> [!IMPORTANT]
> Nové databáze COMPUTE GEN4 – již nejsou podporovány v oblastech Austrálie – východ a Brazílie – jih.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Úroveň služby pro obecné účely: výpočetní platforma generace 4 (část 1)

|Velikost výpočetních prostředků (cíl služby)|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Generování výpočtů|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|
|Virtuální jádra|1|2|3|4|5|6|
|Paměť (GB)|7|14|21|28|35|42|
|Maximální počet databáze na fond <sup>1</sup>|100|200|500|500|500|500|
|Podpora columnstore|Yes|Yes|Yes|Yes|Yes|Yes|
|Úložiště OLTP v paměti (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximální velikost dat (GB)|512|756|1536|1536|1536|2 048|
|Maximální velikost protokolu|154|227|461|461|461|614|
|Maximální velikost dat databáze TempDB (GB)|32|64|96|128|160|192|
|Typ úložiště|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|
|Latence v/v (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet datových IOPS na fond <sup>2</sup> |400|800|1200|1600|2000|2400|
|Maximální počet protokolů na fond (MB/s)|4.7|9,4|14,1|18,8|23,4|28,1|
|Maximální počet souběžných pracovních procesů na fond (požadavky) <sup>3</sup> |210|420|630|840|1050|1260|
|Maximální počet souběžných přihlášení na fond <sup>3</sup> |210|420|630|840|1050|1260|
|Maximální počet souběžných relací|30 000|30 000|30 000|30 000|30 000|30 000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 3|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 5|0, 0,25, 0,5, 1... 6|
|Počet replik|1|1|1|1|1|1|
|Více AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Škálování čtení|N/A|N/A|N/A|N/A|N/A|N/A|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

<sup>1</sup> Další informace najdete [v tématu Správa prostředků v hustých elastických fondech](elastic-pool-resource-management.md) .

<sup>2</sup> maximální hodnota pro velikosti v/v v rozsahu od 8 kb do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> v případě maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi si prohlédněte [omezení prostředků databáze s jedinou databází](resource-limits-vcore-single-databases.md). Pokud například elastický fond používá Gen5 a maximální počet vCore na databázi je nastaven na hodnotu 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi nastavený na 0,5, pak je hodnota maximální počet souběžných pracovních procesů 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na vCore. Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Úroveň služby pro obecné účely: výpočetní platforma generace 4 (část 2)

|Velikost výpočetních prostředků (cíl služby)|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generování výpočtů|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|
|Virtuální jádra|7|8|9|10|16|24|
|Paměť (GB)|49|56|63|70|112|159,5|
|Maximální počet databáze na fond <sup>1</sup>|500|500|500|500|500|500|
|Podpora columnstore|Yes|Yes|Yes|Yes|Yes|Yes|
|Úložiště OLTP v paměti (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximální velikost dat (GB)|2 048|2 048|2 048|2 048|3584|4 096|
|Maximální velikost protokolu (GB)|614|614|614|614|1075|1229|
|Maximální velikost dat databáze TempDB (GB)|224|256|288|320|512|768|
|Typ úložiště|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|
|Latence v/v (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet datových IOPS na fond <sup>2</sup>|2800|3200|3600|4000|6400|9600|
|Maximální počet protokolů na fond (MB/s)|32,8|37,5|37,5|37,5|37,5|37,5|
|Maximální počet souběžných pracovních procesů na fond (požadavky) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Maximální počet souběžných přihlášení ve fondu (požadavky) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Maximální počet souběžných relací|30 000|30 000|30 000|30 000|30 000|30 000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0, 0,25, 0,5, 1... 7|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 9|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 10, 16|0, 0,25, 0,5, 1... 10, 16, 24|
|Počet replik|1|1|1|1|1|1|
|Více AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Škálování čtení|N/A|N/A|N/A|N/A|N/A|N/A|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

<sup>1</sup> Další informace najdete [v tématu Správa prostředků v hustých elastických fondech](elastic-pool-resource-management.md) .

<sup>2</sup> maximální hodnota pro velikosti v/v v rozsahu od 8 kb do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](resource-limits-logical-server.md#resource-governance).    

<sup>3</sup> v případě maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi si prohlédněte [omezení prostředků databáze s jedinou databází](resource-limits-vcore-single-databases.md). Pokud například elastický fond používá Gen5 a maximální počet vCore na databázi je nastaven na hodnotu 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi nastavený na 0,5, pak je hodnota maximální počet souběžných pracovních procesů 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na vCore. Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

## <a name="general-purpose---provisioned-compute---gen5"></a>Pro obecné účely zřízené COMPUTE – Gen5

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Úroveň služby pro obecné účely: generace 5 COMPUTE Platform (část 1)

|Velikost výpočetních prostředků (cíl služby)|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuální jádra|2|4|6|8|10|12|14|
|Paměť (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Maximální počet databáze na fond <sup>1</sup>|100|200|500|500|500|500|500|
|Podpora columnstore|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|Úložiště OLTP v paměti (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximální velikost dat (GB)|512|756|1536|1536|1536|2 048|2 048|
|Maximální velikost protokolu (GB)|154|227|461|461|461|614|614|
|Maximální velikost dat databáze TempDB (GB)|64|128|192|256|320|384|448|
|Typ úložiště|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|
|Latence v/v (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet datových IOPS na fond <sup>2</sup>|800|1600|2400|3200|4000|4800|5600|
|Maximální počet protokolů na fond (MB/s)|9,4|18,8|28,1|37,5|37,5|37,5|37,5|
|Maximální počet souběžných pracovních procesů na fond (požadavky) <sup>3</sup>|210|420|630|840|1050|1260|1470|
|Maximální počet souběžných přihlášení na fond (požadavky) <sup>3</sup>|210|420|630|840|1050|1260|1470|
|Maximální počet souběžných relací|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 6|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 12|0, 0,25, 0,5, 1... 14|
|Počet replik|1|1|1|1|1|1|1|
|Více AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Škálování čtení|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

<sup>1</sup> Další informace najdete [v tématu Správa prostředků v hustých elastických fondech](elastic-pool-resource-management.md) .

<sup>2</sup> maximální hodnota pro velikosti v/v v rozsahu od 8 kb do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> v případě maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi si prohlédněte [omezení prostředků databáze s jedinou databází](resource-limits-vcore-single-databases.md). Pokud například elastický fond používá Gen5 a maximální počet vCore na databázi je nastaven na hodnotu 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi nastavený na 0,5, pak je hodnota maximální počet souběžných pracovních procesů 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na vCore. Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Úroveň služby pro obecné účely: generace 5 COMPUTE Platform (část 2)

|Velikost výpočetních prostředků (cíl služby)|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuální jádra|16|18|20|24|32|40|80|
|Paměť (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Maximální počet databáze na fond <sup>1</sup>|500|500|500|500|500|500|500|
|Podpora columnstore|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|Úložiště OLTP v paměti (GB)|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximální velikost dat (GB)|2 048|3072|3072|3072|4 096|4 096|4 096|
|Maximální velikost protokolu (GB)|614|922|922|922|1229|1229|1229|
|Maximální velikost dat databáze TempDB (GB)|512|576|640|768|1024|1280|2560|
|Typ úložiště|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|
|Latence v/v (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet datových IOPS na fond <sup>2</sup> |6 400|7 200|8 000|9 600|12 800|16 000|16 000|
|Maximální počet protokolů na fond (MB/s)|37,5|37,5|37,5|37,5|37,5|37,5|37,5|
|Maximální počet souběžných pracovních procesů na fond (požadavky) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Maximální počet souběžných přihlášení na fond (požadavky) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Maximální počet souběžných relací|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0, 0,25, 0,5, 1... 16|0, 0,25, 0,5, 1... 18|0, 0,25, 0,5, 1... 20|0, 0,25, 0,5, 1... 20, 24|0, 0,25, 0,5, 1... 20, 24, 32|0, 0,25, 0,5, 1... 16, 24, 32, 40|0, 0,25, 0,5, 1... 16, 24, 32, 40, 80|
|Počet replik|1|1|1|1|1|1|1|
|Více AZ|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Škálování čtení|N/A|N/A|N/A|N/A|N/A|N/A|N/A|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

<sup>1</sup> Další informace najdete [v tématu Správa prostředků v hustých elastických fondech](elastic-pool-resource-management.md) .

<sup>2</sup> maximální hodnota pro velikosti v/v v rozsahu od 8 kb do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> v případě maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi si prohlédněte [omezení prostředků databáze s jedinou databází](resource-limits-vcore-single-databases.md). Pokud například elastický fond používá Gen5 a maximální počet vCore na databázi je nastaven na hodnotu 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi nastavený na 0,5, pak je hodnota maximální počet souběžných pracovních procesů 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na vCore. Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Pro obecné účely zřízené COMPUTE-Fsv2-Series

### <a name="fsv2-series-compute-generation-part-1"></a>Výpočet výpočetní úrovně Fsv2-Series (část 1)

|Velikost výpočetních prostředků (cíl služby)|GP_Fsv2_8|GP_Fsv2_10|GP_Fsv2_12|GP_Fsv2_14| GP_Fsv2_16|
|:---| ---:|---:|---:|---:|---:|
|Generování výpočtů|Řada Fsv2|Řada Fsv2|Řada Fsv2|Řada Fsv2|Řada Fsv2|
|Virtuální jádra|8|10|12|14|16|
|Paměť (GB)|15,1|18,9|22,7|26,5|30,2|
|Maximální počet databáze na fond <sup>1</sup>|500|500|500|500|500|
|Podpora columnstore|Yes|Yes|Yes|Yes|Yes|
|Úložiště OLTP v paměti (GB)|N/A|N/A|N/A|N/A|N/A|
|Maximální velikost dat (GB)|1024|1024|1024|1024|1536|
|Maximální velikost protokolu (GB)|336|336|336|336|512|
|Maximální velikost dat databáze TempDB (GB)|333|333|333|333|333|
|Typ úložiště|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|
|Latence v/v (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet datových IOPS na fond <sup>2</sup>|2560|3200|3840|4480|5120|
|Maximální počet protokolů na fond (MB/s)|30|30|30|30|30|
|Maximální počet souběžných pracovních procesů na fond (požadavky) <sup>3</sup>|400|500|600|700|800|
|Maximální počet souběžných přihlášení na fond (požadavky) <sup>3</sup>|800|1000|1200|1400|1600|
|Maximální počet souběžných relací|30 000|30 000|30 000|30 000|30 000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0-8|0-10|0-12|0-14|0-16|
|Počet replik|1|1|1|1|1|
|Více AZ|N/A|N/A|N/A|N/A|N/A|
|Škálování čtení|N/A|N/A|N/A|N/A|N/A|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|


<sup>1</sup> Další informace najdete [v tématu Správa prostředků v hustých elastických fondech](elastic-pool-resource-management.md) .

<sup>2</sup> maximální hodnota pro velikosti v/v v rozsahu od 8 kb do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> v případě maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi si prohlédněte [omezení prostředků databáze s jedinou databází](resource-limits-vcore-single-databases.md). Pokud například elastický fond používá Gen5 a maximální počet vCore na databázi je nastaven na hodnotu 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi nastavený na 0,5, pak je hodnota maximální počet souběžných pracovních procesů 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na vCore. Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

### <a name="fsv2-series-compute-generation-part-2"></a>Generace COMPUTE Fsv2-Series (část 2)

|Velikost výpočetních prostředků (cíl služby)|GP_Fsv2_18|GP_Fsv2_20|GP_Fsv2_24|GP_Fsv2_32| GP_Fsv2_36|GP_Fsv2_72|
|:---| ---:|---:|---:|---:|---:|---:|
|Generování výpočtů|Řada Fsv2|Řada Fsv2|Řada Fsv2|Řada Fsv2|Řada Fsv2|Řada Fsv2|
|Virtuální jádra|18|20|24|32|36|72|
|Paměť (GB)|34,0|37,8|45,4|60,5|68,0|136,0|
|Maximální počet databáze na fond <sup>1</sup>|500|500|500|500|500|
|Podpora columnstore|Yes|Yes|Yes|Yes|Yes|Yes|
|Úložiště OLTP v paměti (GB)|N/A|N/A|N/A|N/A|N/A|N/A|
|Maximální velikost dat (GB)|1536|1536|1536|3072|3072|4 096|
|Maximální velikost protokolu (GB)|512|512|512|1024|1024|1024|
|Maximální velikost dat databáze TempDB (GB)|83,25|92,5|111|148|166,5|333|
|Typ úložiště|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|Vzdálený disk SSD|
|Latence v/v (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet datových IOPS na fond <sup>2</sup>|5760|6400|7680|10240|11520|23040|
|Maximální počet protokolů na fond (MB/s)|30|30|30|30|30|30|
|Maximální počet souběžných pracovních procesů na fond (požadavky) <sup>3</sup>|900|1000|1200|1600|1800|3600|
|Maximální počet souběžných přihlášení na fond (požadavky) <sup>3</sup>|1800|2000|2400|3200|3600|7200|
|Maximální počet souběžných relací|30 000|30 000|30 000|30 000|30 000|30 000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0-18|0-20|0-24|0-32|0-36|0-72|
|Počet replik|1|1|1|1|1|1|
|Více AZ|N/A|N/A|N/A|N/A|N/A|N/A|
|Škálování čtení|N/A|N/A|N/A|N/A|N/A|N/A|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

<sup>1</sup> Další informace najdete [v tématu Správa prostředků v hustých elastických fondech](elastic-pool-resource-management.md) .

<sup>2</sup> maximální hodnota pro velikosti v/v v rozsahu od 8 kb do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> v případě maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi si prohlédněte [omezení prostředků databáze s jedinou databází](resource-limits-vcore-single-databases.md). Pokud například elastický fond používá Gen5 a maximální počet vCore na databázi je nastaven na hodnotu 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi nastavený na 0,5, pak je hodnota maximální počet souběžných pracovních procesů 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na vCore. Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

## <a name="business-critical---provisioned-compute---gen4"></a>Uživatelsky důležité – zřízené COMPUTE – COMPUTE GEN4 –

> [!IMPORTANT]
> Nové databáze COMPUTE GEN4 – již nejsou podporovány v oblastech Austrálie – východ a Brazílie – jih.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Podniková úroveň služeb: výpočetní platforma generace 4 (část 1)

|Velikost výpočetních prostředků (cíl služby)|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Generování výpočtů|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|
|Virtuální jádra|2|3|4|5|6|
|Paměť (GB)|14|21|28|35|42|
|Maximální počet databáze na fond <sup>1</sup>|50|100|100|100|100|
|Podpora columnstore|Yes|Yes|Yes|Yes|Yes|
|Úložiště OLTP v paměti (GB)|2|3|4|5|6|
|Typ úložiště|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|
|Maximální velikost dat (GB)|1024|1024|1024|1024|1024|
|Maximální velikost protokolu (GB)|307|307|307|307|307|
|Maximální velikost dat databáze TempDB (GB)|64|96|128|160|192|
|Latence v/v (přibližná)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální počet datových IOPS na fond <sup>2</sup>|9 000|13 500|18 000|22 500|27 000|
|Maximální počet protokolů na fond (MB/s)|20|30|40|50|60|
|Maximální počet souběžných pracovních procesů na fond (požadavky) <sup>3</sup>|420|630|840|1050|1260|
|Maximální počet souběžných přihlášení na fond (požadavky) <sup>3</sup>|420|630|840|1050|1260|
|Maximální počet souběžných relací|30 000|30 000|30 000|30 000|30 000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 3|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 5|0, 0,25, 0,5, 1... 6|
|Počet replik|4|4|4|4|4|
|Více AZ|Yes|Yes|Yes|Yes|Yes|
|Škálování čtení|Yes|Yes|Yes|Yes|Yes|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

<sup>1</sup> Další informace najdete [v tématu Správa prostředků v hustých elastických fondech](elastic-pool-resource-management.md) .

<sup>2</sup> maximální hodnota pro velikosti v/v v rozsahu od 8 kb do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> v případě maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi si prohlédněte [omezení prostředků databáze s jedinou databází](resource-limits-vcore-single-databases.md). Pokud například elastický fond používá Gen5 a maximální počet vCore na databázi je nastaven na hodnotu 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi nastavený na 0,5, pak je hodnota maximální počet souběžných pracovních procesů 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na vCore. Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Podniková úroveň služeb: výpočetní platforma generace 4 (část 2)

|Velikost výpočetních prostředků (cíl služby)|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generování výpočtů|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|
|Virtuální jádra|7|8|9|10|16|24|
|Paměť (GB)|49|56|63|70|112|159,5|
|Maximální počet databáze na fond <sup>1</sup>|100|100|100|100|100|100|
|Podpora columnstore|N/A|N/A|N/A|N/A|N/A|N/A|
|Úložiště OLTP v paměti (GB)|7|8|9,5|11|20|36|
|Typ úložiště|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|
|Maximální velikost dat (GB)|1024|1024|1024|1024|1024|1024|
|Maximální velikost protokolu (GB)|307|307|307|307|307|307|
|Maximální velikost dat databáze TempDB (GB)|224|256|288|320|512|768|
|Latence v/v (přibližná)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální počet datových IOPS na fond <sup>2</sup>|31 500|36 000|40 500|45 000|72 000|96 000|
|Maximální počet protokolů na fond (MB/s)|70|80|80|80|80|80|
|Maximální počet souběžných pracovních procesů na fond (požadavky) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Maximální počet souběžných přihlášení na fond (požadavky) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Maximální počet souběžných relací|30 000|30 000|30 000|30 000|30 000|30 000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0, 0,25, 0,5, 1... 7|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 9|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 10, 16|0, 0,25, 0,5, 1... 10, 16, 24|
|Počet replik|4|4|4|4|4|4|
|Více AZ|Yes|Yes|Yes|Yes|Yes|Yes|
|Škálování čtení|Yes|Yes|Yes|Yes|Yes|Yes|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

<sup>1</sup> Další informace najdete [v tématu Správa prostředků v hustých elastických fondech](elastic-pool-resource-management.md) .

<sup>2</sup> maximální hodnota pro velikosti v/v v rozsahu od 8 kb do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> v případě maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi si prohlédněte [omezení prostředků databáze s jedinou databází](resource-limits-vcore-single-databases.md). Pokud například elastický fond používá Gen5 a maximální počet vCore na databázi je nastaven na hodnotu 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi nastavený na 0,5, pak je hodnota maximální počet souběžných pracovních procesů 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na vCore. Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

## <a name="business-critical---provisioned-compute---gen5"></a>Uživatelsky důležité – zřízené COMPUTE – Gen5

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Podniková úroveň služeb: výpočetní platforma generace 5 (část 1)

|Velikost výpočetních prostředků (cíl služby)|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuální jádra|4|6|8|10|12|14|
|Paměť (GB)|20,8|31,1|41,5|51,9|62,3|72,7|
|Maximální počet databáze na fond <sup>1</sup>|50|100|100|100|100|100|
|Podpora columnstore|Yes|Yes|Yes|Yes|Yes|Yes|
|Úložiště OLTP v paměti (GB)|3,14|4,71|6,28|8,65|11,02|13,39|
|Maximální velikost dat (GB)|1024|1536|1536|1536|3072|3072|
|Maximální velikost protokolu (GB)|307|307|461|461|922|922|
|Maximální velikost dat databáze TempDB (GB)|128|192|256|320|384|448|
|Typ úložiště|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|
|Latence v/v (přibližná)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální počet datových IOPS na fond <sup>2</sup>|18 000|27 000|36 000|45 000|54 000|63 000|
|Maximální počet protokolů na fond (MB/s)|60|90|120|120|120|120|
|Maximální počet souběžných pracovních procesů na fond (požadavky) <sup>3</sup>|420|630|840|1050|1260|1470|
|Maximální počet souběžných přihlášení na fond (požadavky) <sup>3</sup>|420|630|840|1050|1260|1470|
|Maximální počet souběžných relací|30 000|30 000|30 000|30 000|30 000|30 000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 6|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 12|0, 0,25, 0,5, 1... 14|
|Počet replik|4|4|4|4|4|4|
|Více AZ|Yes|Yes|Yes|Yes|Yes|Yes|
|Škálování čtení|Yes|Yes|Yes|Yes|Yes|Yes|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

<sup>1</sup> Další informace najdete [v tématu Správa prostředků v hustých elastických fondech](elastic-pool-resource-management.md) .

<sup>2</sup> maximální hodnota pro velikosti v/v v rozsahu od 8 kb do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> v případě maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi si prohlédněte [omezení prostředků databáze s jedinou databází](resource-limits-vcore-single-databases.md). Pokud například elastický fond používá Gen5 a maximální počet vCore na databázi je nastaven na hodnotu 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi nastavený na 0,5, pak je hodnota maximální počet souběžných pracovních procesů 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na vCore. Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Podniková úroveň služeb: výpočetní platforma generace 5 (část 2)

|Velikost výpočetních prostředků (cíl služby)|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuální jádra|16|18|20|24|32|40|80|
|Paměť (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Maximální počet databáze na fond <sup>1</sup>|100|100|100|100|100|100|100|
|Podpora columnstore|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|Úložiště OLTP v paměti (GB)|15.77|18,14|20,51|25,25|37,94|52,23|131,68|
|Maximální velikost dat (GB)|3072|3072|3072|4 096|4 096|4 096|4 096|
|Maximální velikost protokolu (GB)|922|922|922|1229|1229|1229|1229|
|Maximální velikost dat databáze TempDB (GB)|512|576|640|768|1024|1280|2560|
|Typ úložiště|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|
|Latence v/v (přibližná)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální počet datových IOPS na fond <sup>2</sup>|72 000|81 000|90,000|108 000|144 000|180 000|256 000|
|Maximální počet protokolů na fond (MB/s)|120|120|120|120|120|120|120|
|Maximální počet souběžných pracovních procesů na fond (požadavky) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Maximální počet souběžných přihlášení na fond (požadavky) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Maximální počet souběžných relací|30 000|30 000|30 000|30 000|30 000|30 000|30 000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0, 0,25, 0,5, 1... 16|0, 0,25, 0,5, 1... 18|0, 0,25, 0,5, 1... 20|0, 0,25, 0,5, 1... 20, 24|0, 0,25, 0,5, 1... 20, 24, 32|0, 0,25, 0,5, 1... 20, 24, 32, 40|0, 0,25, 0,5, 1... 20, 24, 32, 40, 80|
|Počet replik|4|4|4|4|4|4|4|
|Více AZ|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|Škálování čtení|Yes|Yes|Yes|Yes|Yes|Yes|Yes|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

<sup>1</sup> Další informace najdete [v tématu Správa prostředků v hustých elastických fondech](elastic-pool-resource-management.md) .

<sup>2</sup> maximální hodnota pro velikosti v/v v rozsahu od 8 kb do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> v případě maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi si prohlédněte [omezení prostředků databáze s jedinou databází](resource-limits-vcore-single-databases.md). Pokud například elastický fond používá Gen5 a maximální počet vCore na databázi je nastaven na hodnotu 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi nastavený na 0,5, pak je hodnota maximální počet souběžných pracovních procesů 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na vCore. Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

## <a name="business-critical---provisioned-compute---m-series"></a>Podnikové kritické pro výpočetní prostředí-M-Series

### <a name="m-series-compute-generation-part-1"></a>Generace výpočetních prostředků řady M-Series (část 1)

|Velikost výpočetních prostředků (cíl služby)|BC_M_8|BC_M_10|BC_M_12|BC_M_14|BC_M_16|BC_M_18|
|:---| ---:|---:|---:|---:|---:|---:|
|Generování výpočtů|Řada M|Řada M|Řada M|Řada M|Řada M|Řada M|
|Virtuální jádra|8|10|12|14|16|18|
|Paměť (GB)|235,4|294,3|353,2|412,0|470,9|529,7|
|Maximální počet databáze na fond <sup>1</sup>|100|100|100|100|100|100|
|Podpora columnstore|Yes|Yes|Yes|Yes|Yes|Yes|
|Úložiště OLTP v paměti (GB)|64|80|96|112|128|150|
|Maximální velikost dat (GB)|512|640|768|896|1024|1152|
|Maximální velikost protokolu (GB)|171|213|256|299|341|384|
|Maximální velikost dat databáze TempDB (GB)|256|320|384|448|512|576|
|Typ úložiště|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|
|Latence v/v (přibližná)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální počet datových IOPS na fond <sup>2</sup>|12 499|15 624|18 748|21 873|24 998|28 123|
|Maximální počet protokolů na fond (MB/s)|48|60|72|84|96|108|
|Maximální počet souběžných pracovních procesů na fond (požadavky) <sup>3</sup>|800|1 000|1 200|1 400|1 600|1 800|
|Maximální počet souběžných přihlášení na fond (požadavky) <sup>3</sup>|800|1 000|1 200|1 400|1 600|1 800|
|Maximální počet souběžných relací|30000|30000|30000|30000|30000|30000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0-8|0-10|0-12|0-14|0-16|0-18|
|Počet replik|4|4|4|4|4|4|
|Více AZ|No|No|No|No|No|No|
|Škálování čtení|Yes|Yes|Yes|Yes|Yes|Yes|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

<sup>1</sup> Další informace najdete [v tématu Správa prostředků v hustých elastických fondech](elastic-pool-resource-management.md) .

<sup>2</sup> maximální hodnota pro velikosti v/v v rozsahu od 8 kb do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> v případě maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi si prohlédněte [omezení prostředků databáze s jedinou databází](resource-limits-vcore-single-databases.md). Pokud například elastický fond používá Gen5 a maximální počet vCore na databázi je nastaven na hodnotu 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi nastavený na 0,5, pak je hodnota maximální počet souběžných pracovních procesů 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na vCore. Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

Pokud jsou všechny virtuální jádra elastického fondu zaneprázdněné, pak každá databáze ve fondu obdrží stejné množství výpočetních prostředků pro zpracování dotazů. Azure SQL Database poskytuje spravedlivé sdílení prostředků mezi databázemi tím, že zajišťuje rovné řezy výpočetního času. V případě, že je vCore min na databázi nastavená na nenulovou hodnotu, je pro každou databázi navíc zaručená velikost sdílení prostředků elastického fondu.



### <a name="m-series-compute-generation-part-2"></a>Generace COMPUTE řady M-Series (část 2)

|Velikost výpočetních prostředků (cíl služby)|BC_M_20|BC_M_24|BC_M_32|BC_M_64|BC_M_128|
|:---| ---:|---:|---:|---:|---:|
|Generování výpočtů|Řada M|Řada M|Řada M|Řada M|Řada M|
|Virtuální jádra|20|24|32|64|128|
|Paměť (GB)|588,6|706,3|941,8|1883,5|3767,0|
|Maximální počet databáze na fond <sup>1</sup>|100|100|100|100|100|100|
|Podpora columnstore|Yes|Yes|Yes|Yes|Yes|
|Úložiště OLTP v paměti (GB)|172|216|304|704|1768|
|Maximální velikost dat (GB)|1280|1536|2 048|4 096|4 096|
|Maximální velikost protokolu (GB)|427|512|683|1024|1024|
|Maximální velikost dat databáze TempDB (GB)|4 096|2 048|1024|768|640|
|Typ úložiště|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|
|Latence v/v (přibližná)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální počet datových IOPS na fond <sup>2</sup>|31 248|37 497|49 996|99 993|160 000|
|Maximální počet protokolů na fond (MB/s)|120|144|192|264|264|
|Maximální počet souběžných pracovních procesů na fond (požadavky) <sup>3</sup>|2 000|2 400|3 200|6 400|12 800|
|Maximální počet souběžných přihlášení na fond (požadavky) <sup>3</sup>|2 000|2 400|3 200|6 400|12 800|
|Maximální počet souběžných relací|30000|30000|30000|30000|30000|
|Počet replik|4|4|4|4|4|
|Více AZ|No|No|No|No|No|
|Škálování čtení|Yes|Yes|Yes|Yes|Yes|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

<sup>1</sup> Další informace najdete [v tématu Správa prostředků v hustých elastických fondech](elastic-pool-resource-management.md) .

<sup>2</sup> maximální hodnota pro velikosti v/v v rozsahu od 8 kb do 64 KB. Skutečný IOPS je závislý na úlohách. Podrobnosti najdete v tématu zásady [správy v/v data](resource-limits-logical-server.md#resource-governance).

<sup>3</sup> v případě maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi si prohlédněte [omezení prostředků databáze s jedinou databází](resource-limits-vcore-single-databases.md). Pokud například elastický fond používá Gen5 a maximální počet vCore na databázi je nastaven na hodnotu 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi nastavený na 0,5, pak je hodnota maximální počet souběžných pracovních procesů 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na vCore. Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

Pokud jsou všechny virtuální jádra elastického fondu zaneprázdněné, pak každá databáze ve fondu obdrží stejné množství výpočetních prostředků pro zpracování dotazů. Azure SQL Database poskytuje spravedlivé sdílení prostředků mezi databázemi tím, že zajišťuje rovné řezy výpočetního času. V případě, že je vCore min na databázi nastavená na nenulovou hodnotu, je pro každou databázi navíc zaručená velikost sdílení prostředků elastického fondu.


## <a name="database-properties-for-pooled-databases"></a>Vlastnosti databáze pro sdružené databáze

Následující tabulka popisuje vlastnosti pro databáze ve fondu.

> [!NOTE]
> Omezení prostředků jednotlivých databází v elastických fondech jsou obecně stejná jako u samostatných databází mimo fondy, které mají stejnou výpočetní velikost (cíl služby). Například maximální počet souběžných pracovních procesů pro databázi GP_Gen4_1 je 200 pracovních procesů. Maximální počet souběžných pracovních procesů pro databázi ve fondu GP_Gen4_1 je také 200 pracovních procesů. Všimněte si, že celkový počet souběžných pracovních procesů ve fondu GP_Gen4_1 je 210.

| Vlastnost | Popis |
|:--- |:--- |
| Maximální počet virtuální jádra na databázi |Maximální počet virtuální jádra, které mohou využívat všechny databáze ve fondu, pokud jsou k dispozici na základě využití jinými databázemi ve fondu. Maximální počet virtuální jádra na databázi není záruka prostředků pro databázi. Toto nastavení je globální a platí pro všechny databáze ve fondu. Nastavte maximální počet virtuální jádra na databázi dostatečně vysoký, aby bylo možné zpracovávat špičky ve využití databáze. Očekává se, že určitý stupeň překročení je očekávaný, protože fond obecně předpokládá použití vzorců za horkou a studenou databází, ve kterých se všechny databáze současně nešpičkují.|
| Minimální počet virtuální jádra na databázi |Minimální počet virtuální jádra, které jsou zaručené u všech databází ve fondu. Toto nastavení je globální a platí pro všechny databáze ve fondu. Minimální počet virtuální jádra na databázi může být nastaven na hodnotu 0 a také na výchozí hodnotu. Tato vlastnost je nastavena na hodnotu odkudkoli mezi 0 a průměrným využitím virtuální jádra na databázi. Součin počtu databází ve fondu a minimální virtuální jádra na databázi nemůže překročit virtuální jádra na fond.|
| Maximální velikost úložiště na databázi |Maximální velikost databáze nastavená uživatelem pro databázi ve fondu. Databáze ve fondu sdílejí přidělené úložiště fondu, takže velikost databáze může dosáhnout je omezená na menší zbývající velikost úložiště fondu a velikosti databáze.  Maximální velikost databáze odkazuje na maximální velikost datových souborů a nezahrnuje místo, které využívají soubory protokolu. |
|||

## <a name="next-steps"></a>Další kroky

- Omezení prostředků vCore pro izolovanou databázi najdete v tématu [omezení prostředků pro izolované databáze pomocí modelu nákupu Vcore](resource-limits-vcore-single-databases.md) .
- Omezení prostředků DTU pro jednu databázi najdete v tématu [omezení prostředků pro izolované databáze pomocí modelu nákupu DTU](resource-limits-dtu-single-databases.md) .
- Omezení prostředků DTU pro elastické fondy najdete v tématu [omezení prostředků pro elastické fondy pomocí modelu nákupu DTU](resource-limits-dtu-elastic-pools.md) .
- Omezení prostředků pro spravované instance najdete v tématu [omezení prostředků spravované instance](../managed-instance/resource-limits.md).
- Informace o obecných omezeních Azure najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- Informace o omezeních prostředků na logickém SQL serveru najdete v tématu [Přehled omezení prostředků na logickém SQL serveru](resource-limits-logical-server.md) , kde najdete informace o omezeních na úrovni serveru a předplatného.
