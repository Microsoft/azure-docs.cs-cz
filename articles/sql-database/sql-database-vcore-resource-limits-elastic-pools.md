---
title: Omezení prostředků Azure SQL Database vCore – elastické fondy | Microsoft Docs
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
ms.date: 11/04/2019
ms.openlocfilehash: 65c2bfe4d79f5b7d468999143524b96b60f0efaf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495993"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-purchasing-model"></a>Omezení prostředků pro elastické fondy pomocí modelu nákupu vCore

Tento článek poskytuje podrobné omezení prostředků pro Azure SQL Database elastické fondy a databáze ve fondu pomocí modelu nákupu vCore.

Limity nákupních modelů pro DTU najdete v tématu [SQL Database omezení prostředků DTU – elastické fondy](sql-database-dtu-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi a uvolnit nevyužité místo. Další informace najdete v tématu [Správa prostoru souborů v Azure SQL Database](sql-database-file-space-management.md).

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
|Virtuální jádra|1\. místo|2|3|4|5|6|
|Paměť (GB)|7|14|21|28|35|42|
|Maximální počet databází pro každý fond|100|200|500|500|500|500|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|
|Maximální velikost dat (GB)|512|756|756|1536|1536|1536|
|Maximální velikost protokolu|154|227|227|461|461|461|
|Velikost databáze TempDB (GB)|32|64|96|128|160|192|
|Typ úložiště|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|
|Latence v/v (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Cílové IOPS (64 KB)|500|1 000|1 500|2000|2500|3000|
|Omezení rychlosti protokolu (MB/s)|4,6875|9,375|14,0625|18,75|23,4375|28,125|
|Maximální počet souběžných pracovních procesů na fond (požadavky) * |210|420|630|840|1050|1260|
|Maximální počet souběžných přihlášení na fond * |210|420|630|840|1050|1260|
|Maximální počet povolených relací|30000|30000|30000|30000|30000|30000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 3|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 5|0, 0,25, 0,5, 1... 6|
|Počet replik|1\. místo|1\. místo|1\. místo|1\. místo|1\. místo|1\. místo|
|Více AZ|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|
|Škálování čtení na více instancí|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi najdete v části [omezení prostředků databáze na jednu databázi](sql-database-vcore-resource-limits-single-databases.md). Pokud například elastický fond používá Gen5 a jeho maximální vCore na databázi je 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi 0,5, pak maximální počet souběžných pracovních procesů je 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na Vcore.  Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.


### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Úroveň služby pro obecné účely: výpočetní platforma generace 4 (část 2)

|Velikost výpočetního prostředí|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generování výpočtů|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|
|Virtuální jádra|7|8|9|10|16|24|
|Paměť (GB)|49|56|63|70|112|168|
|Maximální počet databází pro každý fond|500|500|500|500|500|500|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|
|Maximální velikost dat (GB)|1536|2 048|2 048|2 048|3584|4 096|
|Maximální velikost protokolu (GB)|461|614|614|614|1075|1229|
|Velikost databáze TempDB (GB)|224|256|288|320|384|384|
|Typ úložiště|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|
|Latence v/v (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Cílové IOPS (64 KB)|3500|4000|4500|5000|7000|7000|
|Omezení rychlosti protokolu (MB/s)|32,8125|37,5|37,5|37,5|37,5|37,5|
|Maximální počet souběžných pracovních procesů na fond (požadavky) *|1470|1680|1890|2 100|3360|5040|
|Maximální počet souběžných přihlášení ve fondu (požadavky) *|1470|1680|1890|2 100|3360|5040|
|Maximální počet povolených relací|30000|30000|30000|30000|30000|30000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0, 0,25, 0,5, 1... 7|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 9|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 10, 16|0, 0,25, 0,5, 1... 10, 16, 24|
|Počet replik|1\. místo|1\. místo|1\. místo|1\. místo|1\. místo|1\. místo|
|Více AZ|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|
|Škálování čtení na více instancí|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi najdete v části [omezení prostředků databáze na jednu databázi](sql-database-vcore-resource-limits-single-databases.md). Pokud například elastický fond používá Gen5 a jeho maximální vCore na databázi je 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi 0,5, pak maximální počet souběžných pracovních procesů je 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na Vcore.  Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.


## <a name="general-purpose---provisioned-compute---gen5"></a>Pro obecné účely zřízené COMPUTE – Gen5

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Úroveň služby pro obecné účely: generace 5 COMPUTE Platform (část 1)

|Velikost výpočetního prostředí|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuální jádra|2|4|6|8|10|12|14|
|Paměť (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Maximální počet databází pro každý fond|100|200|500|500|500|500|500|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|
|Maximální velikost dat (GB)|512|756|756|1536|1536|1536|
|Maximální velikost protokolu (GB)|154|227|227|461|461|461|461|
|Velikost databáze TempDB (GB)|64|128|192|256|320|384|384|
|Typ úložiště|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|
|Latence v/v (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Cílové IOPS (64 KB)|1 000|2000|3000|4000|5000|6000|7000|
|Omezení rychlosti protokolu (MB/s)|4,6875|9,375|14,0625|18,75|23,4375|28,125|32,8125|
|Maximální počet souběžných pracovních procesů na fond (požadavky) *|210|420|630|840|1050|1260|1470|
|Maximální počet souběžných přihlášení na fond (požadavky) *|210|420|630|840|1050|1260|1470|
|Maximální počet povolených relací|30000|30000|30000|30000|30000|30000|30000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 6|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 12|0, 0,25, 0,5, 1... 14|
|Počet replik|1\. místo|1\. místo|1\. místo|1\. místo|1\. místo|1\. místo|1\. místo|
|Více AZ|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|
|Škálování čtení na více instancí|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi najdete v části [omezení prostředků databáze na jednu databázi](sql-database-vcore-resource-limits-single-databases.md). Pokud například elastický fond používá Gen5 a jeho maximální vCore na databázi je 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi 0,5, pak maximální počet souběžných pracovních procesů je 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na Vcore.  Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Úroveň služby pro obecné účely: generace 5 COMPUTE Platform (část 2)

|Velikost výpočetního prostředí|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuální jádra|16|18|20|24|32|40|80|
|Paměť (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Maximální počet databází pro každý fond|500|500|500|500|500|500|500|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|
|Maximální velikost dat (GB)|2 048|2 048|3072|3072|4 096|4 096|4 096|
|Maximální velikost protokolu (GB)|614|614|922|922|1229|1229|1229|
|Velikost databáze TempDB (GB)|384|384|384|384|384|384|384|
|Typ úložiště|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|
|Latence v/v (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Cílové IOPS (64 KB)|7000|7000|7000|7000|7000|7000|7000|
|Omezení rychlosti protokolu (MB/s)|37,5|37,5|37,5|37,5|37,5|37,5|37,5|
|Maximální počet souběžných pracovních procesů na fond (požadavky) *|1680|1890|2 100|2520|3360|4200|8400|
|Maximální počet souběžných přihlášení na fond (požadavky) *|1680|1890|2 100|2520|3360|4200|8400|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0, 0,25, 0,5, 1... 16|0, 0,25, 0,5, 1... 18|0, 0,25, 0,5, 1... 20|0, 0,25, 0,5, 1... 20, 24|0, 0,25, 0,5, 1... 20, 24, 32|0, 0,25, 0,5, 1... 16, 24, 32, 40|0, 0,25, 0,5, 1... 16, 24, 32, 40, 80|
|Počet replik|1\. místo|1\. místo|1\. místo|1\. místo|1\. místo|1\. místo|1\. místo|
|Více AZ|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|
|Škálování čtení na více instancí|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Pro obecné účely zřízené COMPUTE-Fsv2-Series

### <a name="fsv2-series-compute-generation-preview"></a>Generování výpočetních prostředků Fsv2-Series (Preview)

|Velikost výpočetního prostředí|GP_Fsv2_72|
|:--- | --: |
|Generování výpočtů|Fsv2-series|
|Virtuální jádra|72|
|Paměť (GB)|136|
|Maximální počet databází pro každý fond|500|
|Podpora columnstore|Ano|
|Úložiště OLTP v paměti (GB)|Nevztahuje se|
|Maximální velikost dat (GB)|4 096|
|Maximální velikost protokolu (GB)|1024|
|Maximální velikost dat databáze TempDB (GB)|333|
|Typ úložiště|Úložiště úrovně Premium (vzdálené)|
|Latence v/v (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Cílové IOPS (64 KB)|36000|
|Omezení rychlosti protokolu (MB/s)|37,5|
|Maximální počet souběžných pracovních procesů na fond (požadavky) *|1680|
|Maximální počet souběžných přihlášení na fond (požadavky) *|1680|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0-72|
|Počet replik|1\. místo|
|Více AZ|Nevztahuje se|
|Škálování čtení na více instancí|Nevztahuje se|
|Zahrnuté úložiště zálohování|velikost databáze 1X|




\* maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi najdete v části [omezení prostředků databáze na jednu databázi](sql-database-vcore-resource-limits-single-databases.md). Pokud například elastický fond používá Gen5 a jeho maximální vCore na databázi je 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi 0,5, pak maximální počet souběžných pracovních procesů je 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na Vcore.  Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.


## <a name="business-critical---provisioned-compute---gen4"></a>Uživatelsky důležité – zřízené COMPUTE – COMPUTE GEN4 –

> [!IMPORTANT]
> Nové databáze COMPUTE GEN4 – již nejsou podporovány v oblastech Austrálie – východ a Brazílie – jih.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Podniková úroveň služeb: výpočetní platforma generace 4 (část 1)

|Velikost výpočetního prostředí|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Generování výpočtů|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|
|Virtuální jádra|1\. místo|2|3|4|5|6|
|Paměť (GB)|7|14|21|28|35|42|
|Maximální počet databází pro každý fond|Pro tuto výpočetní velikost se podporují jenom jeden databáze.|50|100|100|100|100|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|1\. místo|2|3|4|5|6|
|Typ úložiště|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|
|Maximální velikost dat (GB)|650|650|650|650|650|650|
|Maximální velikost protokolu (GB)|195|195|195|195|195|195|
|Velikost databáze TempDB (GB)|32|64|96|128|160|192|
|Latence v/v (přibližná)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Cílové IOPS (64 KB)|5000|10000|15 000|20000|25000|30000|
|Omezení rychlosti protokolu (MB/s)|10|20|30|40|50|60|
|Maximální počet souběžných pracovních procesů na fond (požadavky) *|210|420|630|840|1050|1260|
|Maximální počet souběžných přihlášení na fond (požadavky) *|210|420|630|840|1050|1260|
|Maximální počet povolených relací|30000|30000|30000|30000|30000|30000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|Nevztahuje se|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1... 3|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 5|0, 0,25, 0,5, 1... 6|
|Počet replik|4|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení na více instancí|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi najdete v části [omezení prostředků databáze na jednu databázi](sql-database-vcore-resource-limits-single-databases.md). Pokud například elastický fond používá Gen5 a jeho maximální vCore na databázi je 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi 0,5, pak maximální počet souběžných pracovních procesů je 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na Vcore.  Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Podniková úroveň služeb: výpočetní platforma generace 4 (část 2)

|Velikost výpočetního prostředí|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generování výpočtů|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|COMPUTE GEN4 –|
|Virtuální jádra|7|8|9|10|16|24|
|Paměť (GB)|81,6|91,8|102|122,4|163,2|204|
|Maximální počet databází pro každý fond|100|100|100|100|100|100|
|Podpora columnstore|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|Nevztahuje se|
|Úložiště OLTP v paměti (GB)|7|8|9,5|11|20|36|
|Typ úložiště|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|
|Maximální velikost dat (GB)|650|650|650|650|1024|1024|
|Maximální velikost protokolu (GB)|195|195|195|195|307|307|
|Velikost databáze TempDB (GB)|224|256|288|320|384|384|
|Latence v/v (přibližná)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Cílové IOPS (64 KB)|35000|40000|45000|50000|80000|120000|
|Omezení rychlosti protokolu (MB/s)|70|80|80|80|80|80|
|Maximální počet souběžných pracovních procesů na fond (požadavky) *|1470|1680|1890|2 100|3360|5040|
|Maximální počet souběžných přihlášení na fond (požadavky) *|1470|1680|1890|2 100|3360|5040|
|Maximální počet povolených relací|30000|30000|30000|30000|30000|30000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0, 0,25, 0,5, 1... 7|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 9|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 10, 16|0, 0,25, 0,5, 1... 10, 16, 24|
|Počet replik|4|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení na více instancí|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi najdete v části [omezení prostředků databáze na jednu databázi](sql-database-vcore-resource-limits-single-databases.md). Pokud například elastický fond používá Gen5 a jeho maximální vCore na databázi je 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi 0,5, pak maximální počet souběžných pracovních procesů je 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na Vcore.  Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

## <a name="business-critical---provisioned-compute---gen5"></a>Uživatelsky důležité – zřízené COMPUTE – Gen5

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Podniková úroveň služeb: výpočetní platforma generace 5 (část 1)

|Velikost výpočetního prostředí|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuální jádra|2|4|6|8|10|12|14|
|Paměť (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Maximální počet databází pro každý fond|Pro tuto výpočetní velikost se podporují jenom jeden databáze.|50|100|100|100|100|100|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|1,571|3,142|4,713|6,284|8,655|11,026|13,397|
|Maximální velikost dat (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maximální velikost protokolu (GB)|307|307|307|461|461|922|922|
|Velikost databáze TempDB (GB)|64|128|192|256|320|384|384|
|Typ úložiště|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|
|Latence v/v (přibližná)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Cílové IOPS (64 KB)|5000|10000|15 000|20000|25000|30000|35000|
|Omezení rychlosti protokolu (MB/s)|15|30|45|60|75|90|105|
|Maximální počet souběžných pracovních procesů na fond (požadavky) *|210|420|630|840|1050|1260|1470|
|Maximální počet souběžných přihlášení na fond (požadavky) *|210|420|630|840|1050|1260|1470|
|Maximální počet povolených relací|30000|30000|30000|30000|30000|30000|30000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|Nevztahuje se|0, 0,25, 0,5, 1... 4|0, 0,25, 0,5, 1... 6|0, 0,25, 0,5, 1... 8|0, 0,25, 0,5, 1... 10|0, 0,25, 0,5, 1... 12|0, 0,25, 0,5, 1... 14|
|Počet replik|4|4|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení na více instancí|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

\* maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi najdete v části [omezení prostředků databáze na jednu databázi](sql-database-vcore-resource-limits-single-databases.md). Pokud například elastický fond používá Gen5 a jeho maximální vCore na databázi je 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi 0,5, pak maximální počet souběžných pracovních procesů je 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na Vcore.  Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Podniková úroveň služeb: výpočetní platforma generace 5 (část 2)

|Velikost výpočetního prostředí|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Generování výpočtů|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|Virtuální jádra|16|18|20|24|32|40|80|
|Paměť (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Maximální počet databází pro každý fond|100|100|100|100|100|100|100|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|15,768|18,139|20,51|25,252|37,936|52,22|131,64|
|Maximální velikost dat (GB)|3072|3072|3072|4 096|4 096|4 096|4 096|
|Maximální velikost protokolu (GB)|922|922|922|1229|1229|1229|1229|
|Velikost databáze TempDB (GB)|384|384|384|384|384|384|384|
|Typ úložiště|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|Místní jednotka SSD|
|Latence v/v (přibližná)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Cílové IOPS (64 KB)|40000|45000|50000|60000|80000|100000|200000|
|Omezení rychlosti protokolu (MB/s)|120|120|120|120|120|120|120|
|Maximální počet souběžných pracovních procesů na fond (požadavky) *|1680|1890|2 100|2520|3360|4200|8400|
|Maximální počet souběžných přihlášení na fond (požadavky) *|1680|1890|2 100|2520|3360|4200|8400|
|Maximální počet povolených relací|30000|30000|30000|30000|30000|30000|30000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0, 0,25, 0,5, 1... 16|0, 0,25, 0,5, 1... 18|0, 0,25, 0,5, 1... 20|0, 0,25, 0,5, 1... 20, 24|0, 0,25, 0,5, 1... 20, 24, 32|0, 0,25, 0,5, 1... 20, 24, 32, 40|0, 0,25, 0,5, 1... 20, 24, 32, 40, 80|
|Počet replik|4|4|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení na více instancí|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

## <a name="business-critical---provisioned-compute---m-series"></a>Podnikové kritické pro výpočetní prostředí-M-Series

### <a name="m-series-compute-generation-preview"></a>Generování výpočetních prostředků řady M-Series (Preview)

|Velikost výpočetního prostředí|GP_M_128|
|:--- | --: |
|Generování výpočtů|M-Series|
|Virtuální jádra|128|
|Paměť (GB)|3767|
|Maximální počet databází pro každý fond|100|
|Podpora columnstore|Ano|
|Úložiště OLTP v paměti (GB)|481|
|Maximální velikost dat (GB)|4 096|
|Maximální velikost protokolu (GB)|2 048|
|Maximální velikost dat databáze TempDB (GB)|4 096|
|Typ úložiště|Místní jednotka SSD|
|Latence v/v (přibližná)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Cílové IOPS (64 KB)|40000|
|Omezení rychlosti protokolu (MB/s)|120|
|Maximální počet souběžných pracovních procesů na fond (požadavky) *|1680|
|Maximální počet souběžných přihlášení na fond (požadavky) *|1680|
|Maximální počet povolených relací|30000|
|Minimální/maximální vCore elastického fondu – volby pro každou databázi|0-128|
|Počet replik|4|
|Více AZ|Ano|
|Škálování čtení na více instancí|Ano|
|Zahrnuté úložiště zálohování|velikost databáze 1X|



\* maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi najdete v části [omezení prostředků databáze na jednu databázi](sql-database-vcore-resource-limits-single-databases.md). Pokud například elastický fond používá Gen5 a jeho maximální vCore na databázi je 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi 0,5, pak maximální počet souběžných pracovních procesů je 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na Vcore.  Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

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
- Informace o obecných omezeních Azure najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../azure-subscription-service-limits.md).
- Informace o omezeních prostředků na databázovém serveru najdete v tématu [Přehled omezení prostředků na serveru SQL Database](sql-database-resource-limits-database-server.md) , kde najdete informace o omezeních na úrovni serveru a předplatného.
