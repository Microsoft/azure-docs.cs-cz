---
title: Azure SQL Database omezení prostředků založené na vCore – jedna databáze | Microsoft Docs
description: Tato stránka popisuje několik běžných omezení prostředků založených na vCore pro jednu databázi v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 04/22/2019
ms.openlocfilehash: 661ac9ea3fd87268c43bf0a0eba66e30f636fc77
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566203"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-based-purchasing-model"></a>Omezení prostředků pro izolované databáze pomocí nákupního modelu založeného na vCore

Tento článek poskytuje podrobné omezení prostředků pro Azure SQL Database izolované databáze pomocí nákupního modelu založeného na vCore.

Omezení pro nákupní model založený na DTU pro jednotlivé databáze na serveru SQL Database najdete v tématu [Přehled omezení prostředků na serveru SQL Database](sql-database-resource-limits-database-server.md).

> [!IMPORTANT]
> Za určitých okolností budete muset zmenšit databázi uvolnění nevyužívaného místa. Další informace najdete v tématu [spravovat místo souborů ve službě Azure SQL Database](sql-database-file-space-management.md).

Můžete nastavit úroveň služby, výpočetní velikost a množství úložiště pro jednu databázi pomocí [Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [jazyka Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShellu](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), rozhraní příkazového [řádku Azure](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)nebo [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Pokyny a požadavky pro škálování najdete v tématu [škálování izolované databáze](sql-database-single-database-scale.md).

## <a name="general-purpose-service-tier-storage-sizes-and-compute-sizes"></a>Úroveň služby Pro obecné účely: Velikosti úložiště a velikosti výpočtů

> [!IMPORTANT]
> Nové databáze COMPUTE GEN4 – již nejsou podporovány v oblasti AustraliaEast.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Úroveň služby Pro obecné účely: Výpočetní platforma generace 4 (část 1)

|Velikost výpočetního prostředí|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|H/W generace|4|4|4|4|4|4|
|Virtuální jádra|1|2|3|4|5|6|
|Paměť (GB)|7|14|21|28|35|42|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|
|Maximální velikost dat (GB)|1024|1024|1024|1536|1536|1536|
|Maximální velikost protokolu (GB)|307|307|307|461|461|461|
|Velikost databáze TempDB (GB)|32|64|96|128|160|192|
|Typ úložiště|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|
|Vstupně-výstupní latence (přibližné)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Cílové IOPS (64 KB)|500|1000|1500|2000|2500|3000|
|Omezení rychlosti protokolu (MB/s)|3.75|7.5|11.25|15|18,75|22.5|
|Maximální počet souběžných pracovních procesů (požadavků)|200|400|600|800|1000|1200|
|Maximální počet povolených relací|30000|30000|30000|30000|30000|30000|
|Počet replik|1|1|1|1|1|1|
|Více AZ|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|
|Škálování čtení na více instancí|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Úroveň služby Pro obecné účely: Výpočetní platforma generace 4 (část 2)

|Velikost výpočetního prostředí|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|H/W generace|4|4|4|4|4|4|
|Virtuální jádra|7|8|9|10|16|24|
|Paměť (GB)|49|56|63|70|112|168|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|
|Maximální velikost dat (GB)|1536|3072|3072|3072|4096|4096|
|Maximální velikost protokolu (GB)|461|922|922|922|1229|1229|
|Velikost databáze TempDB (GB)|224|256|288|320|384|384|
|Typ úložiště|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|
|Vstupně-výstupní latence (přibližné)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)
|Cílové IOPS (64 KB)|3500|4000|4500|5000|7000|7000|
|Omezení rychlosti protokolu (MB/s)|26,25|30|30|30|30|30|
|Maximální počet souběžných pracovních procesů (požadavků)|1400|1600|1800|2000|3200|4800|
|Maximální počet povolených relací|30000|30000|30000|30000|30000|30000|
|Počet replik|1|1|1|1|1|1|
|Více AZ|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|
|Škálování čtení na více instancí|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Úroveň služby Pro obecné účely: Výpočetní platforma 5. generace (část 1)

|Velikost výpočetního prostředí|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|H/W generace|5|5|5|5|5|5|5|
|Virtuální jádra|2|4|6|8|10|12|14|
|Paměť (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|
|Maximální velikost dat (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maximální velikost protokolu (GB)|307|307|307|461|461|461|461|
|Velikost databáze TempDB (GB)|64|128|192|256|320|384|384|
|Typ úložiště|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|
|Vstupně-výstupní latence (přibližné)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Cílové IOPS (64 KB)|1000|2000|3000|4000|5000|6000|7000|
|Omezení rychlosti protokolu (MB/s)|3.75|7.5|11.25|15|18,75|22.5|26,25|
|Maximální počet souběžných pracovních procesů (požadavků)|200|400|600|800|1000|1200|1400|
|Maximální počet povolených relací|30000|30000|30000|30000|30000|30000|30000|
|Počet replik|1|1|1|1|1|1|1|
|Více AZ|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|
|Škálování čtení na více instancí|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Úroveň služby Pro obecné účely: Výpočetní platforma 5. generace (část 2)

|Velikost výpočetního prostředí|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|H/W generace|5|5|5|5|5|5|5|
|Virtuální jádra|16|18|20|24|32|40|80|
|Paměť (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|
|Maximální velikost dat (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maximální velikost protokolu (GB)|922|922|922|1229|1229|1229|1229|
|Velikost databáze TempDB (GB)|384|384|384|384|384|384|384|
|Typ úložiště|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|
|Vstupně-výstupní latence (přibližné)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Cílové IOPS (64 KB)|7000|7000|7000|7000|7000|7000|7000|
|Omezení rychlosti protokolu (MB/s)|30|30|30|30|30|30|30|
|Maximální počet souběžných pracovních procesů (požadavků)|1600|1800|2000|2400|3200|4000|8000|
|Maximální počet povolených relací|30000|30000|30000|30000|30000|30000|30000|
|Počet replik|1|1|1|1|1|1|1|
|Více AZ|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|
|Škálování čtení na více instancí|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

### <a name="serverless-compute-tier"></a>Bezserverová výpočetní úroveň

[Výpočetní vrstva bez serveru](sql-database-serverless.md) je ve verzi Preview a je určena jenom pro jednotlivé databáze pomocí modelu nákupu Vcore.

#### <a name="generation-5-compute-platform"></a>Generace 5 – výpočetní platforma

|Velikost výpočetního prostředí|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|
|:--- | --: |--: |--: |
|H/W generace|5|5|5|
|Min – max – virtuální jádra|0,5 – 1|0,5 – 2|0,5 – 4|
|Minimální – maximální velikost paměti (GB)|2.02-3|2.05 – 6|2.10 – 12|
|Minimální prodleva automatického pozastavení (hodiny)|6|6|6|
|Podpora columnstore|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|Není k dispozici|Není k dispozici|Není k dispozici|
|Maximální velikost dat (GB)|512|1024|1024|
|Maximální velikost protokolu (GB)|12|24|48|
|Velikost databáze TempDB (GB)|32|64|128|
|Typ úložiště|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|Úložiště úrovně Premium (vzdálené)|
|Vstupně-výstupní latence (přibližné)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Cílové IOPS (64 KB)|500|1000|2000|
|Omezení rychlosti protokolu (MB/s)|2.5|5.6|10|
|Maximální počet souběžných pracovních procesů (požadavků)|75|150|300|
|Maximální počet povolených relací|30000|30000|30000|
|Počet replik|1|1|1|
|Více AZ|Není k dispozici|Není k dispozici|Není k dispozici|
|Škálování čtení na více instancí|Není k dispozici|Není k dispozici|Není k dispozici|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

## <a name="business-critical-service-tier-for-provisioned-compute-tier"></a>Úroveň služby Pro důležité obchodní informace pro zřízenou výpočetní úroveň

> [!IMPORTANT]
> Nové databáze COMPUTE GEN4 – již nejsou podporovány v oblasti AustraliaEast.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Úroveň služby Pro důležité obchodní informace: Výpočetní platforma generace 4 (část 1)

|Velikost výpočetního prostředí|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|H/W generace|4|4|4|4|4|4|
|Virtuální jádra|1|2|3|4|5|6|
|Paměť (GB)|7|14|21|28|35|42|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|1|2|3|4|5|6|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Maximální velikost dat (GB)|650|650|650|650|650|650|
|Maximální velikost protokolu (GB)|195|195|195|195|195|195|
|Velikost databáze TempDB (GB)|32|64|96|128|160|192|
|Vstupně-výstupní latence (přibližné)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Cílové IOPS (64 KB)|5000|10000|15 000|20000|25000|30000|
|Omezení rychlosti protokolu (MB/s)|8|16|24|32|40|48|
|Maximální počet souběžných pracovních procesů (požadavků)|200|400|600|800|1000|1200|
|Maximální počet souběžných přihlášení|200|400|600|800|1000|1200|
|Maximální počet povolených relací|30000|30000|30000|30000|30000|30000|
|Počet replik|4|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení na více instancí|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Úroveň služby Pro důležité obchodní informace: Výpočetní platforma generace 4 (část 2)

|Velikost výpočetního prostředí|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|H/W generace|4|4|4|4|4|4|
|Virtuální jádra|7|8|9|10|16|24|
|Paměť (GB)|49|56|63|70|112|168|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|7|8|9,5|11|20|36|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Maximální velikost dat (GB)|650|650|650|650|1024|1024|
|Maximální velikost protokolu (GB)|195|195|195|195|307|307|
|Velikost databáze TempDB (GB)|224|256|288|320|384|384|
|Vstupně-výstupní latence (přibližné)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Cílové IOPS (64 KB)|35000|40000|45000|50000|80000|120000|
|Omezení rychlosti protokolu (MB/s)|56|64|64|64|64|64|
|Maximální počet souběžných pracovních procesů (požadavků)|1400|1600|1800|2000|3200|4800|
|Maximální počet souběžných přihlášení (požadavků)|1400|1600|1800|2000|3200|4800|
|Maximální počet povolených relací|30000|30000|30000|30000|30000|30000|
|Počet replik|4|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení na více instancí|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Úroveň služby Pro důležité obchodní informace: Výpočetní platforma 5. generace (část 1)

|Velikost výpočetního prostředí|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|H/W generace|5|5|5|5|5|5|5|
|Virtuální jádra|2|4|6|8|10|12|14|
|Paměť (GB)|10.2|20,4|30,6|40,8|51|61,2|71,4|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|1,571|3,142|4,713|6,284|8,655|11,026|13,397|
|Maximální velikost dat (GB)|1024|1024|1536|1536|1536|3072|3072|
|Maximální velikost protokolu (GB)|307|307|307|461|461|922|922|
|Velikost databáze TempDB (GB)|64|128|192|256|320|384|384|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Vstupně-výstupní latence (přibližné)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Cílové IOPS (64 KB)|8000|16000|24000|32000|40000|48000|56000|
|Omezení rychlosti protokolu (MB/s)|12|24|36|48|60|72|84|
|Maximální počet souběžných pracovních procesů (požadavků)|200|400|600|800|1000|1200|1400|
|Maximální počet souběžných přihlášení|200|400|600|800|1000|1200|1400|
|Maximální počet povolených relací|30000|30000|30000|30000|30000|30000|30000|
|Počet replik|4|4|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení na více instancí|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Úroveň služby Pro důležité obchodní informace: Výpočetní platforma 5. generace (část 2)

|Velikost výpočetního prostředí|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|H/W generace|5|5|5|5|5|5|5|
|Virtuální jádra|16|18|20|24|32|40|80|
|Paměť (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|15,768|18,139|20,51|25,252|37,936|52,22|131,64|
|Maximální velikost dat (GB)|3072|3072|3072|4096|4096|4096|4096|
|Maximální velikost protokolu (GB)|922|922|922|1229|1229|1229|1229|
|Velikost databáze TempDB (GB)|384|384|384|384|384|384|384|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Vstupně-výstupní latence (přibližné)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Cílové IOPS (64 KB)|64000|72000|80000|96000|128000|160000|320000|
|Omezení rychlosti protokolu (MB/s)|96|96|96|96|96|96|96|
|Maximální počet souběžných pracovních procesů (požadavků)|1600|1800|2000|2400|3200|4000|8000|
|Maximální počet souběžných přihlášení|1600|1800|2000|2400|3200|4000|8000|
|Maximální počet povolených relací|30000|30000|30000|30000|30000|30000|30000|
|Počet replik|4|4|4|4|4|4|4|
|Více AZ|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení na více instancí|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|velikost databáze 1X|

## <a name="hyperscale-service-tier"></a>Hyperškálování úrovně služby

### <a name="generation-5-compute-platform"></a>Generace 5 – výpočetní platforma

|Úroveň výkonu|HS_Gen5_2|HS_Gen5_4|HS_Gen5_8|HS_Gen5_16|HS_Gen5_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|H/W generace|5|5|5|5|5|5|5|5|
|Virtuální jádra|2|4|8|16|24|32|40|80|
|Paměť (GB)|10.2|20,4|40,8|81,6|122,4|163,2|204|408|
|Podpora columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|
|Maximální velikost dat (TB)|100 |100 |100 |100 |100 |100 |100 |100 |
|Maximální velikost protokolu (TB)|1 |1 |1 |1 |1 |1 |1 |1 |
|Velikost databáze TempDB (GB)|64|128|256|384|384|384|384|384|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Cílové IOPS (64 KB)| [Poznámka 1](#note-1) |[Poznámka 1](#note-1)|[Poznámka 1](#note-1) |[Poznámka 1](#note-1) |[Poznámka 1](#note-1) |[Poznámka 1](#note-1) |[Poznámka 1](#note-1) | [Poznámka 1](#note-1) |
|Vstupně-výstupní latence (přibližné)|Má být určeno|Má být určeno|Má být určeno|Má být určeno|Má být určeno|Má být určeno|Má být určeno|Má být určeno|
|Maximální počet souběžných pracovních procesů (požadavků)|200|400|800|1600|2400|3200|4000|8000|
|Maximální počet povolených relací|30000|30000|30000|30000|30000|30000|30000|30000|
|Počet replik|2|2|2|2|2|2|2|2|
|Více AZ|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|Není k dispozici|
|Škálování čtení na více instancí|Ano|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště zálohování |7|7|7|7|7|7|7|7|
|||

### <a name="note-1"></a>Poznámka 1

Škálovatelná architektura je Vícevrstvá architektura s ukládáním do mezipaměti na více úrovních. Platnost IOPS bude záviset na zatížení.

### <a name="next-steps"></a>Další postup

- Omezení prostředků DTU pro jednu databázi najdete v tématu [omezení prostředků pro izolované databáze pomocí nákupního modelu založeného na DTU](sql-database-dtu-resource-limits-single-databases.md) .
- Omezení prostředků vCore pro elastické fondy najdete v tématu [omezení prostředků pro elastické fondy pomocí nákupního modelu založeného na Vcore](sql-database-vcore-resource-limits-elastic-pools.md) .
- Omezení prostředků DTU pro elastické fondy najdete v tématu [omezení prostředků pro elastické fondy pomocí nákupního modelu založeného na DTU](sql-database-dtu-resource-limits-elastic-pools.md) .
- Omezení prostředků pro spravované instance najdete v tématu [omezení prostředků spravované instance](sql-database-managed-instance-resource-limits.md).
- Informace o obecných omezeních Azure najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../azure-subscription-service-limits.md).
- Informace o omezeních prostředků na databázovém serveru najdete v tématu [Přehled omezení prostředků na serveru SQL Database](sql-database-resource-limits-database-server.md) , kde najdete informace o omezeních na úrovni serveru a předplatného.
