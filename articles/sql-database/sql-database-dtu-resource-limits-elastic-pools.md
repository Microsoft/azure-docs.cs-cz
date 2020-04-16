---
title: Omezení prostředků DTU elastické fondy
description: Tato stránka popisuje některé běžné limity prostředků DTU pro elastické fondy v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
ms.date: 03/14/2019
ms.openlocfilehash: 76085dc29d40944cf704dbc5efc578b3314f499a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419234"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-purchasing-model"></a>Omezení prostředků pro elastické fondy pomocí nákupního modelu DTU

Tento článek obsahuje podrobné limity prostředků pro elastické fondy Azure SQL Database a sdružené databáze pomocí nákupního modelu DTU.

Omezení prostředků nákupního modelu DTU pro jednotlivé databáze naleznete v tématu [Limity prostředků DTU – jednotlivé databáze](sql-database-vcore-resource-limits-elastic-pools.md). Omezení prostředků virtuálních jader najdete [v tématu omezení prostředků virtuálních jader – jednotlivé databáze](sql-database-vcore-resource-limits-single-databases.md) a omezení prostředků [virtuálních jader – elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md).

## <a name="elastic-pool-storage-sizes-and-compute-sizes"></a>Elastický fond: Velikosti a výpočetní velikosti úložiště

Pro elastické fondy databáze SQL zobrazí následující tabulky prostředky, které jsou k dispozici na každé úrovni služby a velikost výpočetních prostředků. Vrstvu služby, velikost výpočetních prostředků a velikost úložiště můžete nastavit pomocí [portálu Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShellu](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), [rozhraní API Azure](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases)nebo rozhraní REST [API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!IMPORTANT]
> Pokyny a důležité informace o změně měřítka najdete v [tématu Škálování elastického fondu](sql-database-elastic-pool-scale.md)
> [!NOTE]
> Omezení prostředků jednotlivých databází v elastických fondech jsou obecně stejné jako pro jednotlivé databáze mimo fondy založené na DTU a vrstvě služby. Například maximální počet souběžných pracovníků pro databázi S2 je 120 pracovníků. Takže maximální souběžné pracovníky pro databázi ve fondu Standard je také 120 pracovníků, pokud je maximální DTU na databázi ve fondu 50 DTU (což je ekvivalentní S2).

> [!NOTE]
> Limit prostředků úložiště na fond v každé z následujících tabulek nezahrnuje úložiště tempdb a protokolu.

### <a name="basic-elastic-pool-limits"></a>Základní limity elastického fondu

| Počet eDTU na fond | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Zahrnuté úložiště na fond (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maximální počet možností úložiště na fond (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maximální úložiště OLTP v paměti na fond (GB) | – | – | – | – | – | – | – | – |
| Maximální počet DB s počtem na fond <sup>1</sup> | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Maximální počet souběžných pracovníků (požadavků) na fond <sup>2</sup> | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Maximální počet souběžných relací na fond <sup>2</sup> | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Volby min eDTU na databázi | 0,5 | 0,5 | 0,5 | 0,5 | 0,5 | 0,5 | 0,5 | 0,5 |
| Maximální počet možností eDTU na databázi | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Maximální velikost úložiště na databázi (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 |
||||||||

<sup>1</sup> Další informace naleznete v tématu [Správa prostředků v hustých elastických fondech.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> Maximální počet souběžných pracovníků (požadavků) pro všechny jednotlivé databáze naleznete v [tématu Limity prostředků jedné databáze](sql-database-vcore-resource-limits-single-databases.md). Například pokud elastický fond používá Gen5 a maximální virtuální jádro na databázi je nastavena na 2, pak maximální hodnota souběžných pracovníků je 200.  Pokud je maximální virtuální jádro na databázi nastaveno na 0,5, pak je hodnota maximálního počtu souběžných pracovníků 50, protože v Gen5 je maximálně 100 souběžných pracovníků na virtuální jádro. Pro ostatní nastavení max virtuálních jader na databázi, které jsou menší než 1 virtuální jádro nebo méně, je podobný počet maximální hodování na velikosti.

### <a name="standard-elastic-pool-limits"></a>Standardní limity elastického fondu

| Počet eDTU na fond | **50** | **100** | **200** | **300** | **400** | **800**|
|:---|---:|---:|---:| ---: | ---: | ---: |
| Zahrnuté úložiště na fond (GB) | 50 | 100 | 200 | 300 | 400 | 800 |
| Maximální počet možností úložiště na fond (GB) | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1024 | 300, 500, 750, 1024, 1280 | 400, 500, 750, 1024, 1280, 1536 | 800, 1024, 1280, 1536, 1792, 2048 |
| Maximální úložiště OLTP v paměti na fond (GB) | – | – | – | – | – | – |
| Maximální počet DB s počtem na fond <sup>1</sup> | 100 | 200 | 500 | 500 | 500 | 500 |
| Maximální počet souběžných pracovníků (požadavků) na fond <sup>2</sup> | 100 | 200 | 400 | 600 | 800 | 1600 |
| Maximální počet souběžných relací na fond <sup>2</sup> | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Volby min eDTU na databázi | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Maximální počet možností eDTU na databázi | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 |
| Maximální velikost úložiště na databázi (GB) | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

<sup>1</sup> Další informace naleznete v tématu [Správa prostředků v hustých elastických fondech.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> Maximální počet souběžných pracovníků (požadavků) pro všechny jednotlivé databáze naleznete v [tématu Limity prostředků jedné databáze](sql-database-vcore-resource-limits-single-databases.md). Například pokud elastický fond používá Gen5 a maximální virtuální jádro na databázi je nastavena na 2, pak maximální hodnota souběžných pracovníků je 200.  Pokud je maximální virtuální jádro na databázi nastaveno na 0,5, pak je hodnota maximálního počtu souběžných pracovníků 50, protože v Gen5 je maximálně 100 souběžných pracovníků na virtuální jádro. Pro ostatní nastavení max virtuálních jader na databázi, které jsou menší než 1 virtuální jádro nebo méně, je podobný počet maximální hodování na velikosti.

### <a name="standard-elastic-pool-limits-continued"></a>Limity elastického fondu úrovně Standard (pokračování)

| Počet eDTU na fond | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Zahrnuté úložiště na fond (GB) | 1200 | 1600 | 2000 | 2500 | 3000 |
| Maximální počet možností úložiště na fond (GB) | 1200, 1280, 1536, 1792, 2048, 2304, 2560 | 1600, 1792, 2048, 2304, 2560, 2816, 3072 | 2000, 2048, 2304, 2560, 2816, 3072, 3328, 3584 | 2500, 2560, 2816, 3072, 3328, 3584, 3840, 4096 | 3000, 3072, 3328, 3584, 3840, 4096 |
| Maximální úložiště OLTP v paměti na fond (GB) | – | – | – | – | – |
| Maximální počet DB s počtem na fond <sup>1</sup> | 500 | 500 | 500 | 500 | 500 |
| Maximální počet souběžných pracovníků (požadavků) na fond <sup>2</sup> | 2400 | 3200 | 4000 | 5000 | 6000 |
| Maximální počet souběžných relací na fond <sup>2</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Volby min eDTU na databázi | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1 200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600, 2 000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600, 2 000, 2 500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600, 2 000, 2 500, 3 000 |
| Maximální počet možností eDTU na databázi | 10, 20, 50, 100, 200, 300, 400, 800, 1 200 | 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600 | 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600, 2 000 | 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600, 2 000, 2 500 | 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600, 2 000, 2 500, 3 000 |
| Maximální počet možností úložiště na databázi (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> Další informace naleznete v tématu [Správa prostředků v hustých elastických fondech.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> Maximální počet souběžných pracovníků (požadavků) pro všechny jednotlivé databáze naleznete v [tématu Limity prostředků jedné databáze](sql-database-vcore-resource-limits-single-databases.md). Například pokud elastický fond používá Gen5 a maximální virtuální jádro na databázi je nastavena na 2, pak maximální hodnota souběžných pracovníků je 200.  Pokud je maximální virtuální jádro na databázi nastaveno na 0,5, pak je hodnota maximálního počtu souběžných pracovníků 50, protože v Gen5 je maximálně 100 souběžných pracovníků na virtuální jádro. Pro ostatní nastavení max virtuálních jader na databázi, které jsou menší než 1 virtuální jádro nebo méně, je podobný počet maximální hodování na velikosti.

### <a name="premium-elastic-pool-limits"></a>Prémiové limity elastického fondu

| Počet eDTU na fond | **125** | **250** | **500** | **1000** | **1500**|
|:---|---:|---:|---:| ---: | ---: |
| Zahrnuté úložiště na fond (GB) | 250 | 500 | 750 | 1024 | 1536 |
| Maximální počet možností úložiště na fond (GB) | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 1536 |
| Maximální úložiště OLTP v paměti na fond (GB) | 1 | 2 | 4 | 10 | 12 |
| Maximální počet DB s počtem na fond <sup>1</sup> | 50 | 100 | 100 | 100 | 100 |
| Maximální počet souběžných pracovníků na fond (požadavky) <sup>2</sup> | 200 | 400 | 800 | 1600 | 2400 |
| Maximální počet souběžných relací na fond <sup>2</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Minimální počet eDTU na databázi | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1 000 | 0, 25, 50, 75, 125, 250, 500, 1 000|
| Maximální počet eDTU na databázi | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1 000 | 25, 50, 75, 125, 250, 500, 1 000|
| Maximální velikost úložiště na databázi (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> Další informace naleznete v tématu [Správa prostředků v hustých elastických fondech.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> Maximální počet souběžných pracovníků (požadavků) pro všechny jednotlivé databáze naleznete v [tématu Limity prostředků jedné databáze](sql-database-vcore-resource-limits-single-databases.md). Například pokud elastický fond používá Gen5 a maximální virtuální jádro na databázi je nastavena na 2, pak maximální hodnota souběžných pracovníků je 200.  Pokud je maximální virtuální jádro na databázi nastaveno na 0,5, pak je hodnota maximálního počtu souběžných pracovníků 50, protože v Gen5 je maximálně 100 souběžných pracovníků na virtuální jádro. Pro ostatní nastavení max virtuálních jader na databázi, které jsou menší než 1 virtuální jádro nebo méně, je podobný počet maximální hodování na velikosti.

### <a name="premium-elastic-pool-limits-continued"></a>Limity elastického fondu úrovně Premium (pokračování)

| Počet eDTU na fond | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: |
| Zahrnuté úložiště na fond (GB) | 2 048 | 2560 | 3072 | 3548 | 4 096 |
| Maximální počet možností úložiště na fond (GB) | 2 048 | 2560 | 3072 | 3548 | 4 096|
| Maximální úložiště OLTP v paměti na fond (GB) | 16 | 20 | 24 | 28 | 32 |
| Maximální počet DB s počtem na fond <sup>1</sup> | 100 | 100 | 100 | 100 | 100 |
| Maximální počet souběžných pracovníků (požadavků) na fond <sup>2</sup> | 3200 | 4000 | 4800 | 5600 | 6400 |
| Maximální počet souběžných relací na fond <sup>2</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Volby min eDTU na databázi | 0, 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 0, 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 0, 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 0, 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 0, 25, 50, 75, 125, 250, 500, 1 000, 1 750, 4 000 |
| Maximální počet možností eDTU na databázi | 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 25, 50, 75, 125, 250, 500, 1 000, 1 750, 4 000 |
| Maximální velikost úložiště na databázi (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> Další informace naleznete v tématu [Správa prostředků v hustých elastických fondech.](sql-database-elastic-pool-resource-management.md)

<sup>2</sup> Maximální počet souběžných pracovníků (požadavků) pro všechny jednotlivé databáze naleznete v [tématu Limity prostředků jedné databáze](sql-database-vcore-resource-limits-single-databases.md). Například pokud elastický fond používá Gen5 a maximální virtuální jádro na databázi je nastavena na 2, pak maximální hodnota souběžných pracovníků je 200.  Pokud je maximální virtuální jádro na databázi nastaveno na 0,5, pak je hodnota maximálního počtu souběžných pracovníků 50, protože v Gen5 je maximálně 100 souběžných pracovníků na virtuální jádro. Pro ostatní nastavení max virtuálních jader na databázi, které jsou menší než 1 virtuální jádro nebo méně, je podobný počet maximální hodování na velikosti.

> [!IMPORTANT]
> Více než 1 TB úložiště na úrovni Premium je v současné době k dispozici ve všech oblastech kromě: Čína – východ, Čína – sever, Německo – střed, Německo – severovýchod, Západní střed USA, USA DoD a Us Government Central. V těchto oblastech je maximální úložiště na úrovni Premium omezeno na 1 TB.  Další informace naleznete v [aktuálních omezeních P11-P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).

Pokud se využívají všechny DTU elastického fondu, pak každá databáze ve fondu obdrží stejné množství prostředků ke zpracování dotazů. Služba SQL Database poskytuje spravedlivé sdílení prostředků mezi databázemi tím, že zajišťuje rovnoměrné rozdělení výpočetního času. Pokud je minimální počet DTU na databázi nastaven na nenulovou hodnotu, pro každou databázi bude garantováno spravedlivé sdílení prostředků elastického fondu je společně s libovolným množstvím prostředků.

> [!NOTE]
> Omezení `tempdb` naleznete v tématu [omezení tempdb](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

### <a name="database-properties-for-pooled-databases"></a>Vlastnosti databáze pro sdružené databáze

Následující tabulka popisuje vlastnosti pro sdružené databáze.

| Vlastnost | Popis |
|:--- |:--- |
| Maximální počet eDTU na databázi |Maximální počet eDTU, které může využívat libovolná databáze ve fondu za předpokladu, že jsou dostupné v závislosti na využití ostatními databázemi ve fondu. Maximální počet eDTU na databázi není garancí prostředků pro databázi. Toto nastavení je globální a platí pro všechny databáze ve fondu. Nastavte maximální počet eDTU na databázi dostatečně vysoký, aby databáze zvládly využití ve špičkách. Očekává se určitý stupeň předimenzování, protože fond obecně předpokládá studené a horké vzory používání, při nichž nenastávají špičky u všech databází ve stejnou chvíli. Předpokládejme například, že využití ve špičce na databázi je 20 eDTU a špička v jednu chvíli nastává pouze u 20 % ze 100 databází ve fondu. Pokud je maximální počet eDTU na databázi nastaven na 20 eDTU, je rozumné fond 5krát předimenzovat a nastavit počet eDTU na fond na 400. |
| Minimální počet eDTU na databázi |Minimální počet eDTU garantovaných pro každou databázi ve fondu. Toto nastavení je globální a platí pro všechny databáze ve fondu. Minimální počet eDTU na databázi může být nastaven na 0, což je i výchozí hodnota. Tato vlastnost je nastavena na libovolnou hodnotu mezi 0 a průměrným využitím eDTU na databázi. Násobek počtu databází ve fondu a minimálním počtem eDTU na databázi nemůže překročit počet eDTU na fond. Pokud je například ve fondu 20 databází a minimální počet eDTU na databázi je nastaven na 10 eDTU, pak musí být počet eDTU na fond alespoň 200 eDTU. |
| Maximální úložiště na databázi |Maximální velikost databáze nastavená uživatelem pro databázi ve fondu. Sdružené databáze však sdílejí přidělené úložiště fondu. I v případě, že celkové maximální úložiště *na databázi* je nastavena na větší než celkový dostupný *úložný prostor fondu*, celkové místo skutečně využité všechny databáze nebude moci překročit limit fondu k dispozici.  Maximální velikost databáze odkazuje na maximální velikost datových souborů a nezahrnuje místo, které využívají soubory protokolu. |
|||

## <a name="next-steps"></a>Další kroky

- Omezení prostředků virtuálních jader pro jednu databázi najdete v [tématu omezení prostředků pro jednotlivé databáze pomocí nákupního modelu virtuálních jader.](sql-database-vcore-resource-limits-single-databases.md)
- Omezení prostředků DTU pro jednu databázi naleznete v [tématu omezení prostředků pro jednotlivé databáze pomocí nákupního modelu DTU](sql-database-dtu-resource-limits-single-databases.md)
- Omezení prostředků virtuálních jader pro elastické fondy najdete v [tématu omezení prostředků pro elastické fondy pomocí nákupního modelu virtuálních jader.](sql-database-vcore-resource-limits-elastic-pools.md)
- Omezení prostředků pro spravované instance naleznete v tématu [limity prostředků spravované instance](sql-database-managed-instance-resource-limits.md).
- Informace o obecných omezeních Azure najdete v tématu [Omezení předplatného a služeb Azure, kvóty a omezení](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Informace o omezení prostředků na databázovém serveru naleznete v [tématu přehled omezení prostředků na serveru DATABÁZE SQL](sql-database-resource-limits-database-server.md) informace o omezení na serveru a odběr úrovně.
