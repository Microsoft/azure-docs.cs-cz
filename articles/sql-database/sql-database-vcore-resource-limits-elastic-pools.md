---
title: omezení prostředků virtuálních jader – elastické fondy
description: Tato stránka popisuje některé běžné omezení prostředků virtuálních jader pro elastické fondy v Azure SQL Database.
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
ms.openlocfilehash: b3c5594b8eef76dcb57903408dd1e77c96890eab
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346271"
---
# <a name="resource-limits-for-elastic-pools-using-the-vcore-purchasing-model"></a>Omezení prostředků pro elastické fondy pomocí nákupního modelu virtuálních jader

Tento článek obsahuje podrobné limity prostředků pro elastické fondy Azure SQL Database a sdružené databáze pomocí nákupního modelu virtuálních jader.

Omezení nákupního modelu DTU naleznete v tématu [limity prostředků SQL Database DTU – elastické fondy](sql-database-dtu-resource-limits-elastic-pools.md).

> [!IMPORTANT]
> Za určitých okolností může být nutné zmenšit databázi, aby bylo možné uvolnit nevyužité místo. Další informace najdete [v tématu Správa místa v souborech v Azure SQL Database](sql-database-file-space-management.md).

Vrstvu služby, velikost výpočetních prostředků a velikost úložiště můžete nastavit pomocí [portálu Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShellu](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), [rozhraní API Azure](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases)nebo rozhraní REST [API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!IMPORTANT]
> Pokyny a důležité informace o změně měřítka najdete v [tématu Škálování elastického fondu](sql-database-elastic-pool-scale.md)

## <a name="general-purpose---provisioned-compute---gen4"></a>Obecný účel – zřízené výpočetní prostředky – Gen4

> [!IMPORTANT]
> Nové databáze Gen4 již nejsou podporovány v oblastech Austrálie – východ nebo Brazílie – jih.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-1"></a>Úroveň služby pro obecné účely: Výpočetní platforma generace 4 (část 1)

|Velikost výpočetních prostředků|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Výpočetní generace|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|
|virtuální jádra|1|2|3|4|5|6|
|Paměť (GB)|7|14|21|28|35|42|
|Maximální počet DB s počtem na fond <sup>1</sup>|100|200|500|500|500|500|
|Podpora pro Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (CZ)|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Maximální velikost dat (GB)|512|756|1536|1536|1536|2 048|
|Maximální velikost protokolu|154|227|461|461|461|614|
|Maximální velikost dat TempDB (GB)|32|64|96|128|160|192|
|Typ úložiště|Prémiové (vzdálené) úložiště|Prémiové (vzdálené) úložiště|Prémiové (vzdálené) úložiště|Prémiové (vzdálené) úložiště|Prémiové (vzdálené) úložiště|Prémiové (vzdálené) úložiště|
|Latence vi (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet vibes dat na fond <sup>2</sup> |400|800|1200|1600|2000|2400|
|Maximální míra protokolů na fond (Mb/s)|4.7|9.4|14.1|18.8|23.4|28.1|
|Maximální počet souběžných pracovníků na fond (požadavky) <sup>3</sup> |210|420|630|840|1050|1260|
|Maximální počet souběžných přihlášení na fond <sup>3</sup> |210|420|630|840|1050|1260|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|
|Volby virtuálních jader min/max elastického fondu na databázi|0, 0.25, 0.5, 1|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|Počet replik|1|1|1|1|1|1|
|Multi-AZ|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Škálování čtení|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Zahrnuté úložiště záloh|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|

<sup>1</sup> Další informace naleznete v tématu [Správa prostředků v hustých elastických fondech.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> Maximální hodnota pro velikosti vi v rozsahu od 8 KB do 64 KB. Skutečné viposlužby jsou závislé na pracovní zátěži. Podrobnosti naleznete v [tématu Zásadsprávné správy vi.](sql-database-resource-limits-database-server.md#resource-governance)

<sup>3</sup> Maximální počet souběžných pracovníků (požadavků) pro všechny jednotlivé databáze naleznete v [tématu Limity prostředků jedné databáze](sql-database-vcore-resource-limits-single-databases.md). Například pokud elastický fond používá Gen5 a maximální virtuální jádro na databázi je nastavena na 2, pak maximální hodnota souběžných pracovníků je 200.  Pokud je maximální virtuální jádro na databázi nastaveno na 0,5, pak je hodnota maximálního počtu souběžných pracovníků 50, protože v Gen5 je maximálně 100 souběžných pracovníků na virtuální jádro. Pro ostatní nastavení max virtuálních jader na databázi, které jsou menší než 1 virtuální jádro nebo méně, je podobný počet maximální hodování na velikosti.

### <a name="general-purpose-service-tier-generation-4-compute-platform-part-2"></a>Úroveň služby pro obecné účely: Výpočetní platforma generace 4 (část 2)

|Velikost výpočetních prostředků|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Výpočetní generace|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|
|virtuální jádra|7|8|9|10|16|24|
|Paměť (GB)|49|56|63|70|112|159.5|
|Maximální počet DB s počtem na fond <sup>1</sup>|500|500|500|500|500|500|
|Podpora pro Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (CZ)|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Maximální velikost dat (GB)|2 048|2 048|2 048|2 048|3584|4 096|
|Maximální velikost protokolu (GB)|614|614|614|614|1075|1229|
|Maximální velikost dat TempDB (GB)|224|256|288|320|512|768|
|Typ úložiště|Prémiové (vzdálené) úložiště|Prémiové (vzdálené) úložiště|Prémiové (vzdálené) úložiště|Prémiové (vzdálené) úložiště|Prémiové (vzdálené) úložiště|Prémiové (vzdálené) úložiště|
|Latence vi (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet vibes dat na fond <sup>2</sup>|2800|3200|3600|4000|6400|9600|
|Maximální míra protokolů na fond (Mb/s)|32.8|37.5|37.5|37.5|37.5|37.5|
|Maximální počet souběžných pracovníků na fond (požadavky) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Maximální počet souběžných přihlášení fondu (požadavky) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|
|Volby virtuálních jader min/max elastického fondu na databázi|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|Počet replik|1|1|1|1|1|1|
|Multi-AZ|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Škálování čtení|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Zahrnuté úložiště záloh|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|

<sup>1</sup> Další informace naleznete v tématu [Správa prostředků v hustých elastických fondech.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> Maximální hodnota pro velikosti vi v rozsahu od 8 KB do 64 KB. Skutečné viposlužby jsou závislé na pracovní zátěži. Podrobnosti naleznete v [tématu Zásadsprávné správy vi.](sql-database-resource-limits-database-server.md#resource-governance)    

<sup>3</sup> Maximální počet souběžných pracovníků (požadavků) pro všechny jednotlivé databáze naleznete v [tématu Limity prostředků jedné databáze](sql-database-vcore-resource-limits-single-databases.md). Například pokud elastický fond používá Gen5 a maximální virtuální jádro na databázi je nastavena na 2, pak maximální hodnota souběžných pracovníků je 200.  Pokud je maximální virtuální jádro na databázi nastaveno na 0,5, pak je hodnota maximálního počtu souběžných pracovníků 50, protože v Gen5 je maximálně 100 souběžných pracovníků na virtuální jádro. Pro ostatní nastavení max virtuálních jader na databázi, které jsou menší než 1 virtuální jádro nebo méně, je podobný počet maximální hodování na velikosti.

## <a name="general-purpose---provisioned-compute---gen5"></a>Obecný účel – zřízené výpočetní prostředky – Gen5

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-1"></a>Úroveň služby pro obecné účely: Výpočetní platforma generace 5 (část 1)

|Velikost výpočetních prostředků|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Výpočetní generace|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|
|virtuální jádra|2|4|6|8|10|12|14|
|Paměť (GB)|10.4|20.8|31.1|41.5|51.9|62.3|72.7|
|Maximální počet DB s počtem na fond <sup>1</sup>|100|200|500|500|500|500|500|
|Podpora pro Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (CZ)|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Maximální velikost dat (GB)|512|756|1536|1536|1536|2 048|2 048|
|Maximální velikost protokolu (GB)|154|227|461|461|461|614|614|
|Maximální velikost dat TempDB (GB)|64|128|192|256|320|384|448|
|Typ úložiště|Prémiové (vzdálené) úložiště|Prémiové (vzdálené) úložiště|Prémiové (vzdálené) úložiště|Prémiové (vzdálené) úložiště|Prémiové (vzdálené) úložiště|Prémiové (vzdálené) úložiště|Prémiové (vzdálené) úložiště|
|Latence vi (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet vibes dat na fond <sup>2</sup>|800|1600|2400|3200|4000|4800|5600|
|Maximální míra protokolů na fond (Mb/s)|9.4|18.8|28.1|37.5|37.5|37.5|37.5|
|Maximální počet souběžných pracovníků na fond (požadavky) <sup>3</sup>|210|420|630|840|1050|1260|1470|
|Maximální počet souběžných přihlášení na fond (požadavky) <sup>3</sup>|210|420|630|840|1050|1260|1470|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Volby virtuálních jader min/max elastického fondu na databázi|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|Počet replik|1|1|1|1|1|1|1|
|Multi-AZ|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Škálování čtení|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Zahrnuté úložiště záloh|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|

<sup>1</sup> Další informace naleznete v tématu [Správa prostředků v hustých elastických fondech.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> Maximální hodnota pro velikosti vi v rozsahu od 8 KB do 64 KB. Skutečné viposlužby jsou závislé na pracovní zátěži. Podrobnosti naleznete v [tématu Zásadsprávné správy vi.](sql-database-resource-limits-database-server.md#resource-governance)

<sup>3</sup> Maximální počet souběžných pracovníků (požadavků) pro všechny jednotlivé databáze naleznete v [tématu Limity prostředků jedné databáze](sql-database-vcore-resource-limits-single-databases.md). Například pokud elastický fond používá Gen5 a maximální virtuální jádro na databázi je nastavena na 2, pak maximální hodnota souběžných pracovníků je 200.  Pokud je maximální virtuální jádro na databázi nastaveno na 0,5, pak je hodnota maximálního počtu souběžných pracovníků 50, protože v Gen5 je maximálně 100 souběžných pracovníků na virtuální jádro. Pro ostatní nastavení max virtuálních jader na databázi, které jsou menší než 1 virtuální jádro nebo méně, je podobný počet maximální hodování na velikosti.

### <a name="general-purpose-service-tier-generation-5-compute-platform-part-2"></a>Úroveň služby pro obecné účely: Výpočetní platforma generace 5 (část 2)

|Velikost výpočetních prostředků|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Výpočetní generace|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|
|virtuální jádra|16|18|20|24|32|40|80|
|Paměť (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Maximální počet DB s počtem na fond <sup>1</sup>|500|500|500|500|500|500|500|
|Podpora pro Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (CZ)|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Maximální velikost dat (GB)|2 048|3072|3072|3072|4 096|4 096|4 096|
|Maximální velikost protokolu (GB)|614|922|922|922|1229|1229|1229|
|Maximální velikost dat TempDB (GB)|512|576|640|768|1024|1280|2560|
|Typ úložiště|Prémiové (vzdálené) úložiště|Prémiové (vzdálené) úložiště|Prémiové (vzdálené) úložiště|Prémiové (vzdálené) úložiště|Prémiové (vzdálené) úložiště|Prémiové (vzdálené) úložiště|Prémiové (vzdálené) úložiště|
|Latence vi (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet vibes dat na fond <sup>2</sup> |6,400|7,200|8 000|9 600|12,800|16 000|32,000|
|Maximální míra protokolů na fond (Mb/s)|37.5|37.5|37.5|37.5|37.5|37.5|37.5|
|Maximální počet souběžných pracovníků na fond (požadavky) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Maximální počet souběžných přihlášení na fond (požadavky) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Volby virtuálních jader min/max elastického fondu na databázi|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...16, 24, 32, 40|0, 0.25, 0.5, 1...16, 24, 32, 40, 80|
|Počet replik|1|1|1|1|1|1|1|
|Multi-AZ|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Škálování čtení|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Zahrnuté úložiště záloh|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|

<sup>1</sup> Další informace naleznete v tématu [Správa prostředků v hustých elastických fondech.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> Maximální hodnota pro velikosti vi v rozsahu od 8 KB do 64 KB. Skutečné viposlužby jsou závislé na pracovní zátěži. Podrobnosti naleznete v [tématu Zásadsprávné správy vi.](sql-database-resource-limits-database-server.md#resource-governance)

<sup>3</sup> Maximální počet souběžných pracovníků (požadavků) pro všechny jednotlivé databáze naleznete v [tématu Limity prostředků jedné databáze](sql-database-vcore-resource-limits-single-databases.md). Například pokud elastický fond používá Gen5 a maximální virtuální jádro na databázi je nastavena na 2, pak maximální hodnota souběžných pracovníků je 200.  Pokud je maximální virtuální jádro na databázi nastaveno na 0,5, pak je hodnota maximálního počtu souběžných pracovníků 50, protože v Gen5 je maximálně 100 souběžných pracovníků na virtuální jádro. Pro ostatní nastavení max virtuálních jader na databázi, které jsou menší než 1 virtuální jádro nebo méně, je podobný počet maximální hodování na velikosti.

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Obecný účel – zřízené výpočetní prostředky – řada Fsv2

### <a name="fsv2-series-compute-generation-preview"></a>Výpočetní generace řady Fsv2 (náhled)

|Velikost výpočetních prostředků|GP_Fsv2_72|
|:--- | --: |
|Výpočetní generace|Řada Fsv2|
|virtuální jádra|72|
|Paměť (GB)|136.2|
|Maximální počet DB s počtem na fond <sup>1</sup>|500|
|Podpora pro Columnstore|Ano|
|Úložiště OLTP v paměti (CZ)|Není dostupné.|
|Maximální velikost dat (GB)|4 096|
|Maximální velikost protokolu (GB)|1024|
|Maximální velikost dat TempDB (GB)|333|
|Typ úložiště|Prémiové (vzdálené) úložiště|
|Latence vi (přibližná)|5-7 ms (zápis)<br>5-10 ms (čtení)|
|Maximální počet vibes dat na fond <sup>2</sup>|16 000|
|Maximální míra protokolů na fond (Mb/s)|37.5|
|Maximální počet souběžných pracovníků na fond (požadavky) <sup>3</sup>|3780|
|Maximální počet souběžných přihlášení na fond (požadavky) <sup>3</sup>|3780|
|Maximální počet souběžných relací|30,000|
|Volby virtuálních jader min/max elastického fondu na databázi|0-72|
|Počet replik|1|
|Multi-AZ|Není dostupné.|
|Škálování čtení|Není dostupné.|
|Zahrnuté úložiště záloh|1X db velikost|

<sup>1</sup> Další informace naleznete v tématu [Správa prostředků v hustých elastických fondech.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> Maximální hodnota pro velikosti vi v rozsahu od 8 KB do 64 KB. Skutečné viposlužby jsou závislé na pracovní zátěži. Podrobnosti naleznete v [tématu Zásadsprávné správy vi.](sql-database-resource-limits-database-server.md#resource-governance)

<sup>3</sup> Maximální počet souběžných pracovníků (požadavků) pro všechny jednotlivé databáze naleznete v [tématu Limity prostředků jedné databáze](sql-database-vcore-resource-limits-single-databases.md). Například pokud elastický fond používá Gen5 a maximální virtuální jádro na databázi je nastavena na 2, pak maximální hodnota souběžných pracovníků je 200.  Pokud je maximální virtuální jádro na databázi nastaveno na 0,5, pak je hodnota maximálního počtu souběžných pracovníků 50, protože v Gen5 je maximálně 100 souběžných pracovníků na virtuální jádro. Pro ostatní nastavení max virtuálních jader na databázi, které jsou menší než 1 virtuální jádro nebo méně, je podobný počet maximální hodování na velikosti.

## <a name="business-critical---provisioned-compute---gen4"></a>Kritické pro podnikání – zřízené výpočetní prostředky – Gen4

> [!IMPORTANT]
> Nové databáze Gen4 již nejsou podporovány v oblastech Austrálie – východ nebo Brazílie – jih.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-1"></a>Úroveň důležitých služeb podniku: Výpočetní platforma generace 4 (část 1)

|Velikost výpočetních prostředků|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Výpočetní generace|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|
|virtuální jádra|2|3|4|5|6|
|Paměť (GB)|14|21|28|35|42|
|Maximální počet DB s počtem na fond <sup>1</sup>|50|100|100|100|100|
|Podpora pro Columnstore|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (CZ)|2|3|4|5|6|
|Typ úložiště|Místní SSD|Místní SSD|Místní SSD|Místní SSD|Místní SSD|
|Maximální velikost dat (GB)|1024|1024|1024|1024|1024|
|Maximální velikost protokolu (GB)|307|307|307|307|307|
|Maximální velikost dat TempDB (GB)|64|96|128|160|192|
|Latence vi (přibližná)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální počet vibes dat na fond <sup>2</sup>|9 000|13,500|18 000|22,500|27,000|
|Maximální míra protokolů na fond (Mb/s)|20|30|40|50|60|
|Maximální počet souběžných pracovníků na fond (požadavky) <sup>3</sup>|420|630|840|1050|1260|
|Maximální počet souběžných přihlášení na fond (požadavky) <sup>3</sup>|420|630|840|1050|1260|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|
|Volby virtuálních jader min/max elastického fondu na databázi|0, 0.25, 0.5, 1, 2|0, 0.25, 0.5, 1...3|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...5|0, 0.25, 0.5, 1...6|
|Počet replik|4|4|4|4|4|
|Multi-AZ|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště záloh|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|

<sup>1</sup> Další informace naleznete v tématu [Správa prostředků v hustých elastických fondech.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> Maximální hodnota pro velikosti vi v rozsahu od 8 KB do 64 KB. Skutečné viposlužby jsou závislé na pracovní zátěži. Podrobnosti naleznete v [tématu Zásadsprávné správy vi.](sql-database-resource-limits-database-server.md#resource-governance)

<sup>3</sup> Maximální počet souběžných pracovníků (požadavků) pro všechny jednotlivé databáze naleznete v [tématu Limity prostředků jedné databáze](sql-database-vcore-resource-limits-single-databases.md). Například pokud elastický fond používá Gen5 a maximální virtuální jádro na databázi je nastavena na 2, pak maximální hodnota souběžných pracovníků je 200.  Pokud je maximální virtuální jádro na databázi nastaveno na 0,5, pak je hodnota maximálního počtu souběžných pracovníků 50, protože v Gen5 je maximálně 100 souběžných pracovníků na virtuální jádro. Pro ostatní nastavení max virtuálních jader na databázi, které jsou menší než 1 virtuální jádro nebo méně, je podobný počet maximální hodování na velikosti.

### <a name="business-critical-service-tier-generation-4-compute-platform-part-2"></a>Úroveň důležitých služeb podniku: Výpočetní platforma generace 4 (část 2)

|Velikost výpočetních prostředků|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Výpočetní generace|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|Gen4 (Gen4)|
|virtuální jádra|7|8|9|10|16|24|
|Paměť (GB)|49|56|63|70|112|159.5|
|Maximální počet DB s počtem na fond <sup>1</sup>|100|100|100|100|100|100|
|Podpora pro Columnstore|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|Není dostupné.|
|Úložiště OLTP v paměti (CZ)|7|8|9.5|11|20|36|
|Typ úložiště|Místní SSD|Místní SSD|Místní SSD|Místní SSD|Místní SSD|Místní SSD|
|Maximální velikost dat (GB)|1024|1024|1024|1024|1024|1024|
|Maximální velikost protokolu (GB)|307|307|307|307|307|307|
|Maximální velikost dat TempDB (GB)|224|256|288|320|512|768|
|Latence vi (přibližná)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální počet vibes dat na fond <sup>2</sup>|31,500|36,000|40,500|45 000|72,000|96,000|
|Maximální míra protokolů na fond (Mb/s)|70|80|80|80|80|80|
|Maximální počet souběžných pracovníků na fond (požadavky) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Maximální počet souběžných přihlášení na fond (požadavky) <sup>3</sup>|1470|1680|1890|2100|3360|5040|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|
|Volby virtuálních jader min/max elastického fondu na databázi|0, 0.25, 0.5, 1...7|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...9|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...10, 16|0, 0.25, 0.5, 1...10, 16, 24|
|Počet replik|4|4|4|4|4|4|
|Multi-AZ|Ano|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště záloh|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|

<sup>1</sup> Další informace naleznete v tématu [Správa prostředků v hustých elastických fondech.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> Maximální hodnota pro velikosti vi v rozsahu od 8 KB do 64 KB. Skutečné viposlužby jsou závislé na pracovní zátěži. Podrobnosti naleznete v [tématu Zásadsprávné správy vi.](sql-database-resource-limits-database-server.md#resource-governance)

<sup>3</sup> Maximální počet souběžných pracovníků (požadavků) pro všechny jednotlivé databáze naleznete v [tématu Limity prostředků jedné databáze](sql-database-vcore-resource-limits-single-databases.md). Například pokud elastický fond používá Gen5 a maximální virtuální jádro na databázi je nastavena na 2, pak maximální hodnota souběžných pracovníků je 200.  Pokud je maximální virtuální jádro na databázi nastaveno na 0,5, pak je hodnota maximálního počtu souběžných pracovníků 50, protože v Gen5 je maximálně 100 souběžných pracovníků na virtuální jádro. Pro ostatní nastavení max virtuálních jader na databázi, které jsou menší než 1 virtuální jádro nebo méně, je podobný počet maximální hodování na velikosti.

## <a name="business-critical---provisioned-compute---gen5"></a>Kritické pro podnikání – zřízené výpočetní prostředky – Gen5

### <a name="business-critical-service-tier-generation-5-compute-platform-part-1"></a>Úroveň důležitých služeb pro podniky: Výpočetní platforma generace 5 (část 1)

|Velikost výpočetních prostředků|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Výpočetní generace|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|
|virtuální jádra|4|6|8|10|12|14|
|Paměť (GB)|20.8|31.1|41.5|51.9|62.3|72.7|
|Maximální počet DB s počtem na fond <sup>1</sup>|50|100|100|100|100|100|
|Podpora pro Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (CZ)|3.14|4.71|6.28|8.65|11.02|13.39|
|Maximální velikost dat (GB)|1024|1536|1536|1536|3072|3072|
|Maximální velikost protokolu (GB)|307|307|461|461|922|922|
|Maximální velikost dat TempDB (GB)|128|192|256|320|384|448|
|Typ úložiště|Místní SSD|Místní SSD|Místní SSD|Místní SSD|Místní SSD|Místní SSD|
|Latence vi (přibližná)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální počet vibes dat na fond <sup>2</sup>|18 000|27,000|36,000|45 000|54,000|63,000|
|Maximální míra protokolů na fond (Mb/s)|60|90|120|120|120|120|
|Maximální počet souběžných pracovníků na fond (požadavky) <sup>3</sup>|420|630|840|1050|1260|1470|
|Maximální počet souběžných přihlášení na fond (požadavky) <sup>3</sup>|420|630|840|1050|1260|1470|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|
|Volby virtuálních jader min/max elastického fondu na databázi|0, 0.25, 0.5, 1...4|0, 0.25, 0.5, 1...6|0, 0.25, 0.5, 1...8|0, 0.25, 0.5, 1...10|0, 0.25, 0.5, 1...12|0, 0.25, 0.5, 1...14|
|Počet replik|4|4|4|4|4|4|
|Multi-AZ|Ano|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště záloh|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|

<sup>1</sup> Další informace naleznete v tématu [Správa prostředků v hustých elastických fondech.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> Maximální hodnota pro velikosti vi v rozsahu od 8 KB do 64 KB. Skutečné viposlužby jsou závislé na pracovní zátěži. Podrobnosti naleznete v [tématu Zásadsprávné správy vi.](sql-database-resource-limits-database-server.md#resource-governance)

<sup>3</sup> Maximální počet souběžných pracovníků (požadavků) pro všechny jednotlivé databáze naleznete v [tématu Limity prostředků jedné databáze](sql-database-vcore-resource-limits-single-databases.md). Například pokud elastický fond používá Gen5 a maximální virtuální jádro na databázi je nastavena na 2, pak maximální hodnota souběžných pracovníků je 200.  Pokud je maximální virtuální jádro na databázi nastaveno na 0,5, pak je hodnota maximálního počtu souběžných pracovníků 50, protože v Gen5 je maximálně 100 souběžných pracovníků na virtuální jádro. Pro ostatní nastavení max virtuálních jader na databázi, které jsou menší než 1 virtuální jádro nebo méně, je podobný počet maximální hodování na velikosti.

### <a name="business-critical-service-tier-generation-5-compute-platform-part-2"></a>Úroveň důležitých služeb pro podniky: Výpočetní platforma generace 5 (část 2)

|Velikost výpočetních prostředků|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Výpočetní generace|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|Gen5 (Gen5)|
|virtuální jádra|16|18|20|24|32|40|80|
|Paměť (GB)|83|93.4|103.8|124.6|166.1|207.6|415.2|
|Maximální počet DB s počtem na fond <sup>1</sup>|100|100|100|100|100|100|100|
|Podpora pro Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (CZ)|15.77|18.14|20.51|25.25|37.94|52.23|131.68|
|Maximální velikost dat (GB)|3072|3072|3072|4 096|4 096|4 096|4 096|
|Maximální velikost protokolu (GB)|922|922|922|1229|1229|1229|1229|
|Maximální velikost dat TempDB (GB)|512|576|640|768|1024|1280|2560|
|Typ úložiště|Místní SSD|Místní SSD|Místní SSD|Místní SSD|Místní SSD|Místní SSD|Místní SSD|
|Latence vi (přibližná)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální počet vibes dat na fond <sup>2</sup>|72,000|81,000|90,000|108,000|144,000|180,000|256,000|
|Maximální míra protokolů na fond (Mb/s)|120|120|120|120|120|120|120|
|Maximální počet souběžných pracovníků na fond (požadavky) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Maximální počet souběžných přihlášení na fond (požadavky) <sup>3</sup>|1680|1890|2100|2520|3360|4200|8400|
|Maximální počet souběžných relací|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Volby virtuálních jader min/max elastického fondu na databázi|0, 0.25, 0.5, 1...16|0, 0.25, 0.5, 1...18|0, 0.25, 0.5, 1...20|0, 0.25, 0.5, 1...20, 24|0, 0.25, 0.5, 1...20, 24, 32|0, 0.25, 0.5, 1...20, 24, 32, 40|0, 0.25, 0.5, 1...20, 24, 32, 40, 80|
|Počet replik|4|4|4|4|4|4|4|
|Multi-AZ|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Škálování čtení|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště záloh|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|1X db velikost|

<sup>1</sup> Další informace naleznete v tématu [Správa prostředků v hustých elastických fondech.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> Maximální hodnota pro velikosti vi v rozsahu od 8 KB do 64 KB. Skutečné viposlužby jsou závislé na pracovní zátěži. Podrobnosti naleznete v [tématu Zásadsprávné správy vi.](sql-database-resource-limits-database-server.md#resource-governance)

<sup>3</sup> Maximální počet souběžných pracovníků (požadavků) pro všechny jednotlivé databáze naleznete v [tématu Limity prostředků jedné databáze](sql-database-vcore-resource-limits-single-databases.md). Například pokud elastický fond používá Gen5 a maximální virtuální jádro na databázi je nastavena na 2, pak maximální hodnota souběžných pracovníků je 200.  Pokud je maximální virtuální jádro na databázi nastaveno na 0,5, pak je hodnota maximálního počtu souběžných pracovníků 50, protože v Gen5 je maximálně 100 souběžných pracovníků na virtuální jádro. Pro ostatní nastavení max virtuálních jader na databázi, které jsou menší než 1 virtuální jádro nebo méně, je podobný počet maximální hodování na velikosti.

## <a name="business-critical---provisioned-compute---m-series"></a>Kritické pro podnikání – zřízené výpočetní prostředky – řada M

### <a name="m-series-compute-generation-preview"></a>Výpočetní generace řady M (náhled)

|Velikost výpočetních prostředků|BC_M_128|
|:--- | --: |
|Výpočetní generace|Řada M|
|virtuální jádra|128|
|Paměť (GB)|3767.1|
|Maximální počet DB s počtem na fond <sup>1</sup>|100|
|Podpora pro Columnstore|Ano|
|Úložiště OLTP v paměti (CZ)|1768|
|Maximální velikost dat (GB)|4 096|
|Maximální velikost protokolu (GB)|2 048|
|Maximální velikost dat TempDB (GB)|4 096|
|Typ úložiště|Místní SSD|
|Latence vi (přibližná)|1-2 ms (zápis)<br>1-2 ms (čtení)|
|Maximální počet vibes dat na fond <sup>2</sup>|200 000|
|Maximální míra protokolů na fond (Mb/s)|333|
|Maximální počet souběžných pracovníků na fond (požadavky) <sup>3</sup>|13,440|
|Maximální počet souběžných přihlášení na fond (požadavky) <sup>3</sup>|13,440|
|Maximální počet souběžných relací|30,000|
|Volby virtuálních jader min/max elastického fondu na databázi|0-128|
|Počet replik|4|
|Multi-AZ|Ano|
|Škálování čtení|Ano|
|Zahrnuté úložiště záloh|1X db velikost|

<sup>1</sup> Další informace naleznete v tématu [Správa prostředků v hustých elastických fondech.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> Maximální hodnota pro velikosti vi v rozsahu od 8 KB do 64 KB. Skutečné viposlužby jsou závislé na pracovní zátěži. Podrobnosti naleznete v [tématu Zásadsprávné správy vi.](sql-database-resource-limits-database-server.md#resource-governance)

<sup>3</sup> Maximální počet souběžných pracovníků (požadavků) pro všechny jednotlivé databáze naleznete v [tématu Limity prostředků jedné databáze](sql-database-vcore-resource-limits-single-databases.md). Například pokud elastický fond používá Gen5 a maximální virtuální jádro na databázi je nastavena na 2, pak maximální hodnota souběžných pracovníků je 200.  Pokud je maximální virtuální jádro na databázi nastaveno na 0,5, pak je hodnota maximálního počtu souběžných pracovníků 50, protože v Gen5 je maximálně 100 souběžných pracovníků na virtuální jádro. Pro ostatní nastavení max virtuálních jader na databázi, které jsou menší než 1 virtuální jádro nebo méně, je podobný počet maximální hodování na velikosti.

Pokud jsou všechna virtuální jádra elastického fondu zaneprázdněna, každá databáze ve fondu obdrží stejné množství výpočetních prostředků pro zpracování dotazů. Služba SQL Database poskytuje spravedlivé sdílení prostředků mezi databázemi tím, že zajišťuje rovnoměrné rozdělení výpočetního času. Reálnost sdílení prostředků elastického fondu je navíc k libovolnému množství prostředku, které je jinak zaručeno pro každou databázi, když je minimální virtuální jádro na databázi nastaveno na nenulovou hodnotu.

## <a name="database-properties-for-pooled-databases"></a>Vlastnosti databáze pro sdružené databáze

Následující tabulka popisuje vlastnosti pro sdružené databáze.

> [!NOTE]
> Omezení prostředků jednotlivých databází v elastických fondech jsou obecně stejné jako pro jednotlivé databáze mimo fondy, které mají stejnou velikost výpočetních prostředků. Například maximální počet souběžných pracovníků pro GP_Gen4_1 databáze je 200 pracovníků. Maximální počet souběžných pracovníků pro databázi ve fondu GP_Gen4_1 je tedy také 200 pracovníků. Celkový počet souběžných pracovníků ve fondu GP_Gen4_1 je 210.

| Vlastnost | Popis |
|:--- |:--- |
| Maximální virtuální jádra na databázi |Maximální počet virtuálních jader, které může použít libovolná databáze ve fondu, pokud je k dispozici na základě využití jinými databázemi ve fondu. Maximální virtuální jádra na databázi není zárukou prostředků pro databázi. Toto nastavení je globální a platí pro všechny databáze ve fondu. Nastavte maximální virtuální jádra na databázi dostatečně vysoko, aby zvládla špičky v využití databáze. Určitý stupeň nadměrného potvrzení se očekává, protože fond obecně předpokládá, že vzory používání za tepla a za studena pro databáze, kde všechny databáze nejsou současně vrcholit.|
| Min virtuální jádra na databázi |Minimální počet virtuálních jader, které je zaručena databáze ve fondu. Toto nastavení je globální a platí pro všechny databáze ve fondu. Min vCores na databázi může být nastavena na 0 a je také výchozí hodnotu. Tato vlastnost je nastavena na kdekoli mezi 0 a průměrné využití virtuálních jader na databázi. Součin počtu databází ve fondu a min virtuálních jader na databázi nesmí překročit virtuální jádra na fond.|
| Maximální úložiště na databázi |Maximální velikost databáze nastavená uživatelem pro databázi ve fondu. Sdružené databáze sdílejí přidělené úložiště fondu, takže velikost, kterou může databáze dosáhnout, je omezena na menší zbývající úložiště fondu a velikost databáze.  Maximální velikost databáze odkazuje na maximální velikost datových souborů a nezahrnuje místo, které využívají soubory protokolu. |
|||

## <a name="next-steps"></a>Další kroky

- Omezení prostředků virtuálních jader pro jednu databázi najdete v [tématu omezení prostředků pro jednotlivé databáze pomocí nákupního modelu virtuálních jader.](sql-database-vcore-resource-limits-single-databases.md)
- Omezení prostředků DTU pro jednu databázi naleznete v [tématu omezení prostředků pro jednotlivé databáze pomocí nákupního modelu DTU](sql-database-dtu-resource-limits-single-databases.md)
- Omezení prostředků DTU pro elastické fondy najdete v [tématu omezení prostředků pro elastické fondy pomocí nákupního modelu DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Omezení prostředků pro spravované instance naleznete v tématu [limity prostředků spravované instance](sql-database-managed-instance-resource-limits.md).
- Informace o obecných omezeních Azure najdete v tématu [Omezení předplatného a služeb Azure, kvóty a omezení](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Informace o omezení prostředků na databázovém serveru naleznete v [tématu přehled omezení prostředků na serveru DATABÁZE SQL](sql-database-resource-limits-database-server.md) informace o omezení na serveru a odběr úrovně.
