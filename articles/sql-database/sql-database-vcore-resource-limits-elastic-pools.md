---
title: Omezení na základě vCore prostředků Azure SQL Database - elastické fondy | Microsoft Docs
description: Tato stránka popisuje některé běžné limitů na základě vCore prostředků pro elastické fondy ve službě Azure SQL Database.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: 01f213c7cf5f6be3ef84601a50bb4455422faf22
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309915"
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-for-elastic-pools-preview"></a>Azure SQL Database vCore na základě nákupu modelu limity pro elastické fondy (preview)

Tento článek obsahuje podrobné prostředků limity pro elastické fondy Azure SQL Database a ve fondu databáze pomocí modelu na základě vCore nákupu.

Na základě DTU omezení nákupu modelu najdete v části [limitů prostředků na základě DTU databáze SQL - elastické fondy](sql-database-dtu-resource-limits-elastic-pools.md).


## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>Elastický fond: velikosti úložiště a úrovně výkonu

Pro databáze SQL elastické fondy následující tabulky popisují dostupné prostředky na úrovni jednotlivých služeb a výkonu. Můžete nastavit úroveň služby, úroveň výkonu a velikost úložiště pomocí [portál Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [prostředí PowerShell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), [rozhraní příkazového řádku Azure](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases), nebo [REST API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!NOTE]
> Omezení prostředků jednotlivých databází v elastické fondy jsou obvykle stejné jako u izolovaných databází mimo fondy, které má stejnou úroveň výkonu. Maximální počet souběžných pracovních procesů pro databázi GP_Gen4_1 je například 200 pracovních procesů. Maximální počet souběžných pracovních procesů pro databázi ve fondu GP_Gen4_1 je tedy také 200 pracovních procesů. Všimněte si, že se celkový počet souběžných pracovních procesů ve fondu GP_Gen4_1 je 210.

### <a name="general-purpose-service-tier"></a>Obecné účely úroveň služby

#### <a name="generation-4-compute-platform"></a>Generování 4 výpočetní platforma
|Úroveň výkonu|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|GP_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generování H i zápis|4|4|4|4|4|4|
|Virtuální jádra|1|2|4|8|16|24|
|Paměť (GB)|7|14|28|56|112|168|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Typ úložiště|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|
|Maximální velikost dat (GB)|512|756|1536|2 048|3584|4 096|
|Maximální velikost protokolu|154|227|461|614|1075|1229|
|Databáze TempDB size(DB)|32|64|128|256|384|384|
|Cíl IOPS (64 KB)|500|1000|2000|4000|7000|7000|
|Latence vstupně-výstupní operace (přibližnou)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|
|Maximální počet souběžných pracovních procesů (počet požadavků)|210|420|840|1680|3360|5040|
|Maximální povolená relací|30000|30000|30000|30000|30000|30000|
|Maximální počet fondu hustotu|100|200|500|500|500|500|
|Klikněte na tlačítko zastaví minimální nebo maximální elastického fondu|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|
|Počet replik|1|1|1|1|1|1|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Přečtěte si Škálováním na více systémů|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Zahrnuté úložiště záloh|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|
|||

#### <a name="generation-5-compute-platform"></a>Generování 5 výpočetní platforma
|Úroveň výkonu|GP_Gen5_2|GP_Gen5_4|GP_Gen5_8|GP_Gen5_16|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|Generování H i zápis|5|5|5|5|5|5|5|5|
|Virtuální jádra|2|4|8|16|24|32|40|80|
|Paměť (GB)|11|22|44|88|132|176|220|440|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Typ úložiště|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|
|Maximální velikost dat (GB)|512|756|1536|2 048|3072|4 096|4 096|4 096|
|Maximální velikost protokolu|154|227|461|614|922|1229|1229|1229|
|Databáze TempDB size(DB)|64|128|256|384|384|384|384|384|
|Cíl IOPS (64 KB)|500|1000|2000|4000|6000|7000|7000|7000|
|Latence vstupně-výstupní operace (přibližnou)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|
|Maximální počet souběžných pracovních procesů (počet požadavků)|210|420|840|1680|2520|3360|4200|8400
|Maximální povolená relací|30000|30000|30000|30000|30000|30000|30000|30000|
|Maximální počet fondu hustotu|100|200|500|500|500|500|500|500|
|Klikněte na tlačítko zastaví minimální nebo maximální elastického fondu|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|0, 0,5, 1, 2, 4, 8, 16, 24, 32.|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 40|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 40, 80|
|Počet replik|1|1|1|1|1|1|1|1|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Přečtěte si Škálováním na více systémů|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Zahrnuté úložiště záloh|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|
|||

### <a name="business-critical-service-tier"></a>Obchodní vrstva kritické služby

#### <a name="generation-4-compute-platform"></a>Generování 4 výpočetní platforma
|Úroveň výkonu|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Generování H i zápis|4|4|4|4|4|4|
|Virtuální jádra|1|2|4|8|16|24|
|Paměť (GB)|7|14|28|56|112|168|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|1|2|4|8|20|36|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Maximální velikost dat (GB)|1024|1024|1024|1024|1024|1024|
|Maximální velikost protokolu|307|307|307|307|307|307|
|Databáze TempDB size(DB)|32|64|128|256|384|384|
|Cíl IOPS (64 KB)|5000|10000|20000|40000|80000|120000|
|Latence vstupně-výstupní operace (přibližnou)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|
|Maximální počet souběžných pracovních procesů (počet požadavků)|210|420|840|1680|3360|5040|
|Maximální povolená relací|30000|30000|30000|30000|30000|30000|
|Maximální počet fondu hustotu|neuvedeno|50|100|100|100|100|
|Klikněte na tlačítko zastaví minimální nebo maximální elastického fondu|neuvedeno|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|
|Počet replik|3|3|3|3|3|3|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Přečtěte si Škálováním na více systémů|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště záloh|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|
|||

#### <a name="generation-5-compute-platform"></a>Generování 5 výpočetní platforma
|Úroveň výkonu|BC_Gen5_2|BC_Gen5_4|BC_Gen5_8|BC_Gen5_16|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |--: |--: |--: |--: |
|Generování H i zápis|5|5|5|5|5|5|5|5|
|Virtuální jádra|2|4|8|16|24|32|40|80|
|Paměť (GB)|11|22|44|88|132|176|220|440|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|1.571|3,142|6.284|15.768|25.252|37.936|52.22|131.64|
|Typ úložiště|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|Místní disk SSD|
|Latence vstupně-výstupní operace (přibližnou)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|
|Maximální velikost dat (GB)|1024|1024|1024|1024|2 048|4 096|4 096|4 096|
|Maximální velikost protokolu|307|307|307|307|614|1229|1229|1229|
|Databáze TempDB size(DB)|64|128|256|384|384|384|384|384|
|Cíl IOPS (64 KB)|5000|10000|20000|40000|60000|80000|100000.|200000
|Maximální počet souběžných pracovních procesů (počet požadavků)|210|420|840|1680|2520|3360|5040|8400|
|Maximální povolená relací|30000|30000|30000|30000|30000|30000|30000|30000|
|Maximální počet fondu hustotu|neuvedeno|50|100|100|100|100|100|100|
|Klikněte na tlačítko zastaví minimální nebo maximální elastického fondu|neuvedeno|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|0, 0,25, 0,5, 1, 2, 4, 8, 16, 24|0, 0,5, 1, 2, 4, 8, 16, 24, 32.|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 40|0, 0,5, 1, 2, 4, 8, 16, 24, 32, 40, 80|
|Počet replik|3|3|3|3|3|3|3|3|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Přečtěte si Škálováním na více systémů|Ano|Ano|Ano|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště záloh|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|
|||

Pokud jsou všechny vCores elastického fondu zaneprázdněný, každá databáze ve fondu obdrží stejné množství výpočetních prostředků pro zpracování dotazů. Služba SQL Database poskytuje spravedlivé sdílení prostředků mezi databázemi tím, že zajišťuje rovnoměrné rozdělení výpočetního času. Sdílení rovnosti elastický fond zdrojů je kromě libovolného počtu prostředků, v opačném případě záruku, že každou databázi minimální vCore na databázi je nastavena na hodnotu nula.

### <a name="database-properties-for-pooled-databases"></a>Vlastnosti databáze pro databáze ve fondu

Následující tabulka popisuje vlastnosti pro databáze ve fondu.

| Vlastnost | Popis |
|:--- |:--- |
| Maximální počet vCores na databázi |Maximální počet vCores, který může použít všechny databáze ve fondu, pokud je k dispozici na základě využití jiných databází ve fondu. Maximální počet vCores na databázi není záruku prostředků pro databázi. Toto nastavení je globální a platí pro všechny databáze ve fondu. Nastavte maximální vCores na databázi dostatečně vysoký pro zpracování vrcholů databáze využití. Očekává se určitý stupeň předimenzování, protože fond obecně předpokládá studené a horké vzory používání, při nichž nenastávají špičky u všech databází ve stejnou chvíli.|
| Minimální vCores na databázi |Minimální počet vCores, který zaručeně všechny databáze ve fondu. Toto nastavení je globální a platí pro všechny databáze ve fondu. Min vCores za databáze může být nastaven na hodnotu 0 který je taky výchozí hodnota. Tato vlastnost je nastavená na libovolné místo mezi 0 a vCores průměrné využití za databáze. Produkt počet databází ve fondu a minimální vCores na databázi nesmí překročit vCores na každý fond.|
| Maximální počet úložiště na databázi |Maximální velikost nastavený uživatelem databáze ve fondu. Ve fondu databáze sdílet úložiště přidělené fondu, takže velikost databáze dosáhnout je omezena na menší ze zbývajících fond úložiště a velikost databáze. Maximální velikost databáze odkazuje na maximální velikost datové soubory a nezahrnuje místo využité soubory protokolu. |
|||
 
## <a name="next-steps"></a>Další postup

- V tématu [nejčastější dotazy k databázi SQL](sql-database-faq.md) odpovědi na nejčastější dotazy.
- Informace o obecných omezeních Azure najdete v tématu [předplatného Azure a omezení služby, kvóty a omezení](../azure-subscription-service-limits.md).
