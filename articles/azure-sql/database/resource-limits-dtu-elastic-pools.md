---
title: Prostředky DTU omezení elastických fondů
description: Tato stránka popisuje některé běžné limity prostředků DTU pro elastické fondy v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seo-lt-2019 sqldbrb=1 references_regions
ms.devlang: ''
ms.topic: reference
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 07/28/2020
ms.openlocfilehash: d87c5d162b96209c0ce3d3276dc518f42373590f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92780808"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-purchasing-model"></a>Omezení prostředků pro elastické fondy pomocí modelu nákupu DTU
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Tento článek poskytuje podrobné omezení prostředků pro databáze v Azure SQL Database, které jsou v elastickém fondu, pomocí modelu nákupu DTU.

* Omezení prostředků modelu nákupu DTU pro Azure SQL Database najdete v tématu [omezení prostředků DTU – Azure SQL Database](resource-limits-dtu-single-databases.md).
* Omezení prostředků vCore najdete v tématu omezení [prostředků Vcore –](resource-limits-vcore-single-databases.md) [omezení prostředků Azure SQL Database a Vcore – elastické fondy](resource-limits-vcore-elastic-pools.md).

## <a name="elastic-pool-storage-sizes-and-compute-sizes"></a>Elastický fond: velikosti úložiště a velikosti výpočtů

V případě Azure SQL Database elastických fondů se v následujících tabulkách zobrazují prostředky dostupné v každé úrovni služby a výpočetní velikost. Úroveň služby, výpočetní velikost a množství úložiště můžete nastavit pomocí:

* [Azure Portal](elastic-pool-manage.md#azure-portal)
* [PowerShell](elastic-pool-manage.md#powershell)
* [Azure CLI](elastic-pool-manage.md#azure-cli)
* [REST API](elastic-pool-manage.md#rest-api).

> [!IMPORTANT]
> Pokyny a požadavky pro škálování najdete v tématu [škálování elastického fondu](elastic-pool-scale.md) .

Limity prostředků jednotlivých databází v elastických fondech jsou obecně stejné jako pro izolované databáze mimo fondy založené na DTU a vrstvě služeb. Například maximální počet souběžných pracovních procesů pro databázi S2 je 120 pracovních procesů. Maximální počet souběžných pracovních procesů pro databázi ve fondu Standard je tedy 120 pracovních procesů, pokud je maximální počet DTU na databázi ve fondu 50 DTU (což je ekvivalentem S2).
 
Pro stejný počet DTU můžou prostředky poskytované elastickému fondu překročit prostředky poskytované do izolované databáze mimo elastický fond. To znamená, že využití eDTU elastického fondu může být menší než součet využití DTU napříč databázemi v rámci fondu v závislosti na vzorech úloh. Například v extrémním případě s pouze jednou databází v elastickém fondu, kde využití DTU databáze je 100%, je možné, že využití eDTU fondu činí 50% pro určité vzory úloh. K tomu může dojít i v případě, že maximální počet jednotek DTU na databázi zůstává maximální podporovanou hodnotou velikosti daného fondu.

> [!NOTE]
> Omezení počtu prostředků úložiště na jeden fond v každé z následujících tabulek nezahrnuje úložiště tempdb a log.

### <a name="basic-elastic-pool-limits"></a>Základní limity elastického fondu

| Počet eDTU na fond | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Zahrnuté úložiště na fond (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maximální velikost úložiště na fond (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maximální In-Memory OLTP úložiště na fond (GB) | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A |
| Maximální počet databáze na fond <sup>1</sup> | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Maximální počet souběžných pracovních procesů (požadavků) na fond <sup>2</sup> | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Maximální počet souběžných relací na fond <sup>2</sup> | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Minimální počet jednotek DTU na jednu databázi | 0,5 | 0,5 | 0,5 | 0,5 | 0,5 | 0,5 | 0,5 | 0,5 |
| Maximální počet DTU na výběr databáze | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Maximální velikost úložiště na databázi (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 |
||||||||

<sup>1</sup> Další informace najdete [v tématu Správa prostředků v hustých elastických fondech](elastic-pool-resource-management.md) .

<sup>2</sup> pro maximální počet souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi si prohlédněte [omezení prostředků databáze s jedinou databází](resource-limits-vcore-single-databases.md). Pokud například elastický fond používá Gen5 a maximální počet vCore na databázi je nastaven na hodnotu 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi nastavený na 0,5, pak je hodnota maximální počet souběžných pracovních procesů 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na vCore. Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

### <a name="standard-elastic-pool-limits"></a>Standardní limity elastického fondu

| Počet eDTU na fond | **50** | **100** | **200** | **300** | **400** | **800**|
|:---|---:|---:|---:| ---: | ---: | ---: |
| Zahrnuté úložiště na fond (GB) <sup>1</sup> | 50 | 100 | 200 | 300 | 400 | 800 |
| Maximální velikost úložiště na fond (GB) | 500 | 750 | 1024 | 1280 | 1536 | 2 048 |
| Maximální In-Memory OLTP úložiště na fond (GB) | N/A | N/A | N/A | N/A | N/A | N/A |
| Maximální počet databáze na fond <sup>2</sup> | 100 | 200 | 500 | 500 | 500 | 500 |
| Maximální počet souběžných pracovních procesů (požadavků) na fond <sup>3</sup> | 100 | 200 | 400 | 600 | 800 | 1600 |
| Maximální počet souběžných relací na fond <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Minimální počet jednotek DTU na jednu databázi | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Maximální počet DTU na výběr databáze | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 |
| Maximální velikost úložiště na databázi (GB) | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

<sup>1</sup> Další informace o cenách, které vznikly v důsledku dodatečného zřízeného úložiště, najdete v tématu [SQL Database cenové možnosti](https://azure.microsoft.com/pricing/details/sql-database/elastic/) .

<sup>2</sup> Další informace najdete [v tématu Správa prostředků v hustých elastických fondech](elastic-pool-resource-management.md) .

<sup>3</sup> v případě maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi si prohlédněte [omezení prostředků databáze s jedinou databází](resource-limits-vcore-single-databases.md). Pokud například elastický fond používá Gen5 a maximální počet vCore na databázi je nastaven na hodnotu 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi nastavený na 0,5, pak je hodnota maximální počet souběžných pracovních procesů 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na vCore. Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

### <a name="standard-elastic-pool-limits-continued"></a>Limity elastického fondu úrovně Standard (pokračování)

| Počet eDTU na fond | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Zahrnuté úložiště na fond (GB) <sup>1</sup> | 1200 | 1600 | 2000 | 2500 | 3000 |
| Maximální velikost úložiště na fond (GB) | 2560 | 3072 | 3584 | 4 096 | 4 096 |
| Maximální In-Memory OLTP úložiště na fond (GB) | N/A | N/A | N/A | N/A | N/A |
| Maximální počet databáze na fond <sup>2</sup> | 500 | 500 | 500 | 500 | 500 |
| Maximální počet souběžných pracovních procesů (požadavků) na fond <sup>3</sup> | 2400 | 3200 | 4000 | 5000 | 6000 |
| Maximální počet souběžných relací na fond <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Minimální počet jednotek DTU na jednu databázi | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1 200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600, 2 000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600, 2 000, 2 500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600, 2 000, 2 500, 3 000 |
| Maximální počet DTU na výběr databáze | 10, 20, 50, 100, 200, 300, 400, 800, 1 200 | 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600 | 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600, 2 000 | 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600, 2 000, 2 500 | 10, 20, 50, 100, 200, 300, 400, 800, 1 200, 1 600, 2 000, 2 500, 3 000 |
| Maximální velikost úložiště na databázi (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> Další informace o cenách, které vznikly v důsledku dodatečného zřízeného úložiště, najdete v tématu [SQL Database cenové možnosti](https://azure.microsoft.com/pricing/details/sql-database/elastic/) .

<sup>2</sup> Další informace najdete [v tématu Správa prostředků v hustých elastických fondech](elastic-pool-resource-management.md) .

<sup>3</sup> v případě maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi si prohlédněte [omezení prostředků databáze s jedinou databází](resource-limits-vcore-single-databases.md). Pokud například elastický fond používá Gen5 a maximální počet vCore na databázi je nastaven na hodnotu 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi nastavený na 0,5, pak je hodnota maximální počet souběžných pracovních procesů 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na vCore. Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

### <a name="premium-elastic-pool-limits"></a>Prémiové limity elastického fondu

| Počet eDTU na fond | **125** | **250** | **500** | **1000** | **1500**|
|:---|---:|---:|---:| ---: | ---: |
| Zahrnuté úložiště na fond (GB) <sup>1</sup> | 250 | 500 | 750 | 1024 | 1536 |
| Maximální velikost úložiště na fond (GB) | 1024 | 1024 | 1024 | 1024 | 1536 |
| Maximální In-Memory OLTP úložiště na fond (GB) | 1 | 2 | 4 | 10 | 12 |
| Maximální počet databáze na fond <sup>2</sup> | 50 | 100 | 100 | 100 | 100 |
| Maximální počet souběžných pracovních procesů na fond (požadavky) <sup>3</sup> | 200 | 400 | 800 | 1600 | 2400 |
| Maximální počet souběžných relací na fond <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Minimální počet eDTU na databázi | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1 000 | 0, 25, 50, 75, 125, 250, 500, 1 000|
| Maximální počet eDTU na databázi | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1 000 | 25, 50, 75, 125, 250, 500, 1 000|
| Maximální velikost úložiště na databázi (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> Další informace o cenách, které vznikly v důsledku dodatečného zřízeného úložiště, najdete v tématu [SQL Database cenové možnosti](https://azure.microsoft.com/pricing/details/sql-database/elastic/) .

<sup>2</sup> Další informace najdete [v tématu Správa prostředků v hustých elastických fondech](elastic-pool-resource-management.md) .

<sup>3</sup> v případě maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi si prohlédněte [omezení prostředků databáze s jedinou databází](resource-limits-vcore-single-databases.md). Pokud například elastický fond používá Gen5 a maximální počet vCore na databázi je nastaven na hodnotu 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi nastavený na 0,5, pak je hodnota maximální počet souběžných pracovních procesů 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na vCore. Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

### <a name="premium-elastic-pool-limits-continued"></a>Limity elastického fondu úrovně Premium (pokračování)

| Počet eDTU na fond | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: |
| Zahrnuté úložiště na fond (GB) <sup>1</sup> | 2 048 | 2560 | 3072 | 3548 | 4 096 |
| Maximální velikost úložiště na fond (GB) | 2 048 | 2560 | 3072 | 3548 | 4 096|
| Maximální In-Memory OLTP úložiště na fond (GB) | 16 | 20 | 24 | 28 | 32 |
| Maximální počet databáze na fond <sup>2</sup> | 100 | 100 | 100 | 100 | 100 |
| Maximální počet souběžných pracovních procesů (požadavků) na fond <sup>3</sup> | 3200 | 4000 | 4800 | 5600 | 6400 |
| Maximální počet souběžných relací na fond <sup>3</sup> | 30000 | 30000 | 30000 | 30000 | 30000 |
| Minimální počet jednotek DTU na jednu databázi | 0, 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 0, 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 0, 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 0, 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 0, 25, 50, 75, 125, 250, 500, 1 000, 1 750, 4 000 |
| Maximální počet DTU na výběr databáze | 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 25, 50, 75, 125, 250, 500, 1 000, 1 750 | 25, 50, 75, 125, 250, 500, 1 000, 1 750, 4 000 |
| Maximální velikost úložiště na databázi (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> Další informace o cenách, které vznikly v důsledku dodatečného zřízeného úložiště, najdete v tématu [SQL Database cenové možnosti](https://azure.microsoft.com/pricing/details/sql-database/elastic/) .

<sup>2</sup> Další informace najdete [v tématu Správa prostředků v hustých elastických fondech](elastic-pool-resource-management.md) .

<sup>3</sup> v případě maximálního počtu souběžných pracovních procesů (požadavků) pro každou jednotlivou databázi si prohlédněte [omezení prostředků databáze s jedinou databází](resource-limits-vcore-single-databases.md). Pokud například elastický fond používá Gen5 a maximální počet vCore na databázi je nastaven na hodnotu 2, maximální počet souběžných pracovních procesů je 200.  Pokud je maximální počet vCore na databázi nastavený na 0,5, pak je hodnota maximální počet souběžných pracovních procesů 50, protože na Gen5 existuje maximální 100 počet souběžných pracovních procesů na vCore. Pro jiné maximum vCore nastavení na databázi, které mají méně než 1 vCore nebo méně, je počet souběžných pracovních procesů podobně škálovat.

> [!IMPORTANT]
> Ve všech oblastech je aktuálně k dispozici více než 1 TB úložiště na úrovni Premium s výjimkou: Čína – východ, Čína – sever, Německo – střed a Německo – severovýchod. V těchto oblastech je maximální velikost úložiště na úrovni Premium omezená na 1 TB.  Další informace najdete v tématu [aktuální omezení P11-P15](single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).

Pokud se využívají všechny DTU elastického fondu, pak každá databáze ve fondu obdrží stejné množství prostředků ke zpracování dotazů. Služba SQL Database poskytuje spravedlivé sdílení prostředků mezi databázemi tím, že zajišťuje rovnoměrné rozdělení výpočetního času. Pokud je minimální počet DTU na databázi nastaven na nenulovou hodnotu, pro každou databázi bude garantováno spravedlivé sdílení prostředků elastického fondu je společně s libovolným množstvím prostředků.

> [!NOTE]
> `tempdb`Omezení najdete v tématu [omezení tempdb](/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

### <a name="database-properties-for-pooled-databases"></a>Vlastnosti databáze pro sdružené databáze

Následující tabulka popisuje vlastnosti pro databáze ve fondu.

| Vlastnost | Popis |
|:--- |:--- |
| Maximální počet eDTU na databázi |Maximální počet eDTU, které může využívat libovolná databáze ve fondu za předpokladu, že jsou dostupné v závislosti na využití ostatními databázemi ve fondu. Maximální počet eDTU na databázi není garancí prostředků pro databázi. Toto nastavení je globální a platí pro všechny databáze ve fondu. Nastavte maximální počet eDTU na databázi dostatečně vysoký, aby databáze zvládly využití ve špičkách. Očekává se určitý stupeň předimenzování, protože fond obecně předpokládá studené a horké vzory používání, při nichž nenastávají špičky u všech databází ve stejnou chvíli. Předpokládejme například, že využití ve špičce na databázi je 20 eDTU a špička v jednu chvíli nastává pouze u 20 % ze 100 databází ve fondu. Pokud je maximální počet eDTU na databázi nastaven na 20 eDTU, je rozumné fond 5krát předimenzovat a nastavit počet eDTU na fond na 400. |
| Minimální počet eDTU na databázi |Minimální počet eDTU garantovaných pro každou databázi ve fondu. Toto nastavení je globální a platí pro všechny databáze ve fondu. Minimální počet eDTU na databázi může být nastaven na 0, což je i výchozí hodnota. Tato vlastnost je nastavena na libovolnou hodnotu mezi 0 a průměrným využitím eDTU na databázi. Násobek počtu databází ve fondu a minimálním počtem eDTU na databázi nemůže překročit počet eDTU na fond. Pokud je například ve fondu 20 databází a minimální počet eDTU na databázi je nastaven na 10 eDTU, pak musí být počet eDTU na fond alespoň 200 eDTU. |
| Maximální velikost úložiště na databázi |Maximální velikost databáze nastavená uživatelem pro databázi ve fondu. Databáze ve fondu ale sdílejí přidělené úložiště fondu. I v případě, že celkové maximum úložiště *na databázi* je větší než celkový dostupný *prostor úložiště fondu* , nebude mít celkové místo v podstatě využitý limit fondu k dispozici.  Maximální velikost databáze odkazuje na maximální velikost datových souborů a nezahrnuje místo, které využívají soubory protokolu. |
|||

## <a name="next-steps"></a>Další kroky

* Omezení prostředků vCore pro izolovanou databázi najdete v tématu [omezení prostředků pro izolované databáze pomocí modelu nákupu Vcore](resource-limits-vcore-single-databases.md) .
* Omezení prostředků DTU pro jednu databázi najdete v tématu [omezení prostředků pro izolované databáze pomocí modelu nákupu DTU](resource-limits-dtu-single-databases.md) .
* Omezení prostředků vCore pro elastické fondy najdete v tématu [omezení prostředků pro elastické fondy pomocí modelu nákupu Vcore](resource-limits-vcore-elastic-pools.md) .
* Omezení prostředků pro spravované instance ve spravované instanci SQL Azure najdete v tématu [omezení prostředků spravované instance SQL](../managed-instance/resource-limits.md).
* Informace o obecných omezeních Azure najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md).
* Informace o omezeních prostředků na logickém SQL serveru najdete v tématu [Přehled omezení prostředků na logickém SQL serveru](resource-limits-logical-server.md) , kde najdete informace o omezeních na úrovni serveru a předplatného.