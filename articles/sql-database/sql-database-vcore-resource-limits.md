---
title: Omezení na základě vCore prostředků Azure SQL Database | Microsoft Docs
description: Tato stránka popisuje některé běžné limitů na základě vCore prostředků pro databázi SQL Azure.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/04/2018
ms.author: carlrab
ms.openlocfilehash: 204702eee1cf502ac873e0c1f5e3fd257ecce33c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="azure-sql-database-vcore-based-purchasing-model-limits-preview"></a>Azure SQL Database vCore na základě nákupu modelu omezení (preview)

> [!IMPORTANT]
> Na základě DTU omezení nákupu modelu najdete v části [limitů prostředků na základě DTU databáze SQL](sql-database-dtu-resource-limits.md).

## <a name="single-database-storage-sizes-and-performance-levels"></a>Izolované databáze: velikosti úložiště a úrovně výkonu

Pro izolované databáze v následujících tabulkách prostředky k dispozici pro jednu databázi na úrovni jednotlivých služeb a výkonu. Můžete nastavit úroveň služby, úroveň výkonu a velikost úložiště pro jednu databázi pomocí [portál Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](sql-database-single-database-resources.md#manage-single-database-resources-using-transact-sql), [prostředí PowerShell](sql-database-single-database-resources.md#manage-single-database-resources-using-powershell), [Rozhraní příkazového řádku azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-cli), nebo [rozhraní REST API](sql-database-single-database-resources.md#manage-single-database-resources-using-the-rest-api).

### <a name="general-purpose-service-tier"></a>Obecné účely úroveň služby
|Úroveň výkonu|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|
|:--- | --: |--: |--: |--: |--: |
|Generování H i zápis|4|4|4|4|4|
|vCores|1|2|4|8|16|
|Paměť (GB)|7|14|28|56|112|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Typ úložiště|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|
|Latence vstupně-výstupní operace (přibližnou)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|
|Maximální velikost dat (GB)|1024|1024|1536|3072|4 096|
|Maximální velikost protokolu|307|307|461|922|1229|
|Databáze TempDB size(DB)|32|64|128|256|384|
|Cíl IOPS|320|640|1280|2560|5120|
|Latence vstupně-výstupní operace (přibližnou)|ms 5 – 7 (zápisu)
|Maximální počet souběžných pracovních procesů (počet požadavků)|200|400|800|1600|3200|
|Maximální povolená relací|30000|30000|30000|30000|30000|
|Počet replik|1|1|1|1|1|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Přečtěte si Škálováním na více systémů|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Zahrnuté úložiště záloh|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|
|||

### <a name="business-critical-service-tier"></a>Obchodní vrstva kritické služby
|Úroveň výkonu|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|
|:--- | --: |--: |--: |--: |--: |
|Generování H i zápis|4|4|4|4|4|
|vCores|1|2|4|8|16|
|Paměť (GB)|7|14|28|56|112|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|1|2|4|8|20|
|Typ úložiště|Připojené SSD|Připojené SSD|Připojené SSD|Připojené SSD|Připojené SSD|
|Maximální velikost dat (GB)|1024|1024|1024|1024|1024|
|Maximální velikost protokolu|307|307|307|307|307|
|Databáze TempDB size(DB)|32|64|128|256|384|
|Cíl IOPS|5000|10000|20000|40000|80000|
|Latence vstupně-výstupní operace (přibližnou)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|
|Maximální počet souběžných pracovních procesů (počet požadavků)|200|400|800|1600|3200|
|Maximální povolená relací|30000|30000|30000|30000|30000|
|Počet replik|3|3|3|3|3|
|Více AZ|Ano|Ano|Ano|Ano|Ano|
|Přečtěte si Škálováním na více systémů|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště záloh|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|
|||

## <a name="single-database-change-storage-size"></a>Izolované databáze: Změna velikosti úložiště

- Úložiště se dá zřídit až do limitu maximální velikost pomocí 1 GB přírůstcích. Minimální konfigurovat úložiště je 5GB 
- Úložiště pro izolované databáze může být zřízen zvýšením nebo snížením jeho maximální velikost pomocí [portál Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [prostředí PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Rozhraní příkazového řádku azure](/cli/azure/sql/db#az_sql_db_update), nebo [rozhraní REST API](/rest/api/sql/databases/update).
- SQL Database automaticky přiděluje 30 % dodatečné úložiště pro soubory protokolů a 32GB za vCore pro databázi TempDB, ale nechcete překročit 384GB. Databáze TempDB se nachází na připojené SSD na všech úrovních služby.
- Cena úložiště pro jednu databázi je součet množství dat úložiště a protokol úložiště násobí hodnotou jednotkové ceny úložiště vrstvy služby. Náklady na databázi TempDB je součástí vCore cenu. Podrobnosti na cenu dodatečné úložiště najdete v tématu [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-vcores"></a>Izolované databáze: změnit vCores

Po výběru původně počet vCores, je možné škálovat jedné databáze nahoru nebo dolů dynamicky podle skutečné prostředí pomocí [portál Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [prostředí PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [rozhraní příkazového řádku Azure](/cli/azure/sql/db#az_sql_db_update), nebo [rozhraní REST API](/rest/api/sql/databases/update). 

Se změnou úrovně služeb nebo úrovně výkonu databáze se vytvoří replika původní databáze na nové úrovni výkonu a následně se přepnou připojení na repliku. Během tohoto procesu se neztratí žádná data, ale během krátké chvíle, kdy se přepíná na repliku, jsou zakázána připojení k databázi, takže může dojít k vrácení některých probíhajících transakcí zpět. Doba pro přechod se liší, ale je obecně méně než 30 sekundách 99 % času je v části 4 sekundy. Pokud máte velké množství transakcí pohybující se v okamžiku připojení jsou zakázané, může být delší dobu pro přechod. 

Délka trvání celého procesu vertikálního navyšování kapacity závisí na velikosti a úrovni služeb databáze před změnou a po ní. Například 250 GB databázi, která mění k, z a do vrstvy služeb obecné účely, by se měla dokončit v rámci šest hodin. Pro databázi stejnou velikost, která je změna úrovně výkonu v rámci kritické obchodní vrstvy služeb škálování by se měla dokončit do tří hodin.

> [!TIP]
> Monitorování operací v mailech naleznete v tématu: [spravovat operace pomocí SQL REST API](/rest/api/sql/Operations/List), [spravovat operace pomocí rozhraní příkazového řádku](/cli/azure/sql/db/op), [sledovat operace pomocí T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) a tyto dvě Příkazy prostředí PowerShell: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) a [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Pokud provádíte upgrade na vyšší úroveň výkonu a vrstvu služby, maximální velikost databáze se nezvyšuje, pokud je explicitně zadat větší velikost (maxsize).
* Přejít na starší verzi databáze, databáze používá prostor musí být menší než maximální povolená velikost úroveň cílové služby a výkonu. 
* Při upgradování databáze s [geografická replikace](sql-database-geo-replication-portal.md) povoleno, upgradujte její sekundární databáze úrovně požadovaný výkon před upgradem primární databáze (Obecné pokyny pro nejlepší výkon). Při upgradu na jiný, upgrade sekundární databázi nejprve je vyžadováno.
* Když přechod na starší verzi databáze s [geografická replikace](sql-database-geo-replication-portal.md) povoleno, starší verzi její primární databáze k vrstvě požadovaný výkon před snížením sekundární databáze (Obecné pokyny pro nejlepší výkon). Při přechod na starší verzi na jinou edici, přechod na starší verzi primární databáze nejprve je vyžadováno.
* Nové vlastnosti databáze se nepoužijí, dokud nebudou změny dokončeny.

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>Elastický fond: velikosti úložiště a úrovně výkonu

Pro databáze SQL elastické fondy následující tabulky popisují dostupné prostředky na úrovni jednotlivých služeb a výkonu. Můžete nastavit úroveň služby, úroveň výkonu a velikost úložiště pomocí [portál Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [prostředí PowerShell](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-powershell), [rozhraní příkazového řádku Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-cli), nebo [REST API](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-rest-api).

> [!NOTE]
> Omezení prostředků jednotlivých databází v elastické fondy jsou obvykle stejné jako u izolovaných databází mimo fondy, které má stejnou úroveň výkonu. Maximální počet souběžných pracovních procesů pro databázi GP_Gen4_1 je například 200 pracovních procesů. Maximální počet souběžných pracovních procesů pro databázi ve fondu GP_Gen4_1 je tedy také 200 pracovních procesů. Všimněte si, že se celkový počet souběžných pracovních procesů ve fondu GP_Gen4_1 je 210.

### <a name="general-purpose-service-tier"></a>Obecné účely úroveň služby
|Úroveň výkonu|GP_Gen4_1|GP_Gen4_2|GP_Gen4_4|GP_Gen4_8|GP_Gen4_16|
|:--- | --: |--: |--: |--: |--: |
|Generování H i zápis|4|4|4|4|4|
|vCores|1|2|4|8|16|
|Paměť (GB)|7|14|28|56|112|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Typ úložiště|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|Storage úrovně Premium (vzdálený)|
|Maximální velikost dat (GB)|512|756|1536|2 048|3584|
|Maximální velikost protokolu|154|227|461|614|1075|
|Databáze TempDB size(DB)|32|64|128|256|384|
|Cíl IOPS|320|640|1280|2560|5120|
|Latence vstupně-výstupní operace (přibližnou)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|ms 5 – 7 (zápisu)<br>5 až 10 ms (přečíst)|
|Maximální počet souběžných pracovních procesů (počet požadavků)|210|420|840|1680|3360|
|Maximální povolená relací|30000|30000|30000|30000|30000|
|Maximální počet fondu hustotu|100|200|500|500|500|
|Klikněte na tlačítko zastaví minimální nebo maximální elastického fondu|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|
|Počet replik|1|1|1|1|1|
|Více AZ|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Přečtěte si Škálováním na více systémů|neuvedeno|neuvedeno|neuvedeno|neuvedeno|neuvedeno|
|Zahrnuté úložiště záloh|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|
|||

### <a name="business-critical-service-tier"></a>Obchodní vrstva kritické služby
|Úroveň výkonu|BC_Gen4_1|BC_Gen4_2|BC_Gen4_4|BC_Gen4_8|BC_Gen4_16|
|:--- | --: |--: |--: |--: |--: |
|Generování H i zápis|4|4|4|4|4|
|vCores|1|2|4|8|16|
|Paměť (GB)|7|14|28|56|112|
|Podpora Columnstore|Ano|Ano|Ano|Ano|Ano|
|Úložiště OLTP v paměti (GB)|1|2|4|8|20|
|Typ úložiště|Připojené SSD|Připojené SSD|Připojené SSD|Připojené SSD|Připojené SSD|
|Maximální velikost dat (GB)|1024|1024|1024|1024|1024|
|Maximální velikost protokolu|307|307|307|461|614|
|Databáze TempDB size(DB)|32|64|128|256|384|
|Cíl IOPS|320|640|1280|2560|5120|
|Latence vstupně-výstupní operace (přibližnou)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|1 – 2 ms (zápisu)<br>ms 1 – 2 (čtení)|
|Maximální počet souběžných pracovních procesů (počet požadavků)|210|420|840|1680|3360|
|Maximální povolená relací|30000|30000|30000|30000|30000|
|Maximální počet fondu hustotu|neuvedeno|50|100|100|100|
|Klikněte na tlačítko zastaví minimální nebo maximální elastického fondu|0, 0,25, 0,5, 1|0, 0,25, 0,5, 1, 2|0, 0,25, 0,5, 1, 2, 4|0, 0,25, 0,5, 1, 2, 4, 8|0, 0,25, 0,5, 1, 2, 4, 8, 16|
|Více AZ|Ano|Ano|Ano|Ano|Ano|
|Přečtěte si Škálováním na více systémů|Ano|Ano|Ano|Ano|Ano|
|Zahrnuté úložiště záloh|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|1 X DB velikost|
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
 
## <a name="elastic-pool-change-storage-size"></a>Elastický fond: Změna velikosti úložiště

- Úložiště může být zřízen až do limitu maximální velikost: 
 - Pro standardní úložiště zvýšení nebo snížení velikosti v přírůstcích po 10 GB
 - Pro storage úrovně Premium zvětšit nebo zmenšit velikost v přírůstcích po 250 GB
- Úložiště pro elastický fond může být zřízen zvýšením nebo snížením jeho maximální velikost pomocí [portál Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [prostředí PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [rozhraní příkazového řádku Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), nebo [ Rozhraní REST API](/rest/api/sql/elasticpools/update).
- Cena úložiště pro elastický fond je množství úložiště násobí hodnotou jednotkové ceny úložiště vrstvy služby. Podrobnosti na cenu dodatečné úložiště najdete v tématu [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="elastic-pool-change-vcores"></a>Elastický fond: změnit vCores

Můžete zvýšit nebo snížit úroveň výkonu na elastickém fondu podle prostředků vyžaduje použití [portál Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [prostředí PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [rozhraní příkazového řádku Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), nebo [Rozhraní REST API](/rest/api/sql/elasticpools/update).

- Pokud změny měřítka vCores fondu, jsou stručně vyřadit připojení databáze. Toto je stejné chování jako nastane, když změny měřítka Dtu pro jednu databázi (ne ve fondu). Podrobnosti o doba trvání a dopad vynechaných připojení pro databázi během změny měřítka operací, najdete v části [změny měřítka Dtu pro jednu databázi](#single-database-change-storage-size). 
- Můžete nastavit velikost fondu vCores pro dobu trvání závisí na celkovém množství prostoru úložiště používané všechny databáze ve fondu. Obecně platí, rescaling latence zobrazí průměr 90 minut nebo méně na 100 GB. Například pokud celkové místo používá všechny databáze ve fondu je 200 GB, pak očekávané latence pro změny měřítka fondu je 3 hodiny nebo méně. V některých případech v rámci úroveň Standard nebo Basic může být rescaling latence bez ohledu na množství prostoru v části pět minut.
- Obecně platí, doba trvání změnit vCores minimum za databáze nebo max vCores na databázi je pět minut nebo méně.
- Při downsizing vCores fondu, musí být menší než maximální povolená velikost vCores vrstvy a fond služby cílové místo fondu používá.

## <a name="what-is-the-maximum-number-of-servers-and-databases"></a>Jaký je maximální počet serverů a databází?

| Maximum | Hodnota |
| :--- | :--- |
| Databáze na serveru | 5000 |
| Počet serverů za předplatné podle oblastí | 20 |
|||

> [!IMPORTANT]
> Jako počet databází, se blíží limitu na serveru, může objevit následující:
> <br> • Latence zvýšení ve spuštění dotazů v hlavní databázi.  To zahrnuje zobrazení statistiky využití prostředků, jako je například sys.resource_stats.
> <br> • Zvýšení latence v operacích správy a vykreslování portálu hlediska, které se týkají databáze na serveru.

## <a name="what-happens-when-database-and-elastic-pool-resource-limits-are-reached"></a>Co se stane, když se dosáhne databáze a limitů elastického fondu prostředků?

### <a name="compute-vcores"></a>Výpočetní (vCores)

Když se stane vysoké využití výpočetních databáze (měřeno ve využití vCore), latence dotazu zvyšuje a můžete i vypršení časového limitu. Za těchto podmínek dotazy může být zařazeny do fronty pomocí služby a jsou poskytl uvolní prostředky pro spuštění jako prostředek.
Při zjištění vysokou výpočetní využití, tyto možnosti omezení rizik:

- Zvýšení úrovně výkonu databáze nebo elastický fond poskytnout další vCores databáze. V tématu [jedné databáze: změnit cVcores](#single-database-change-vcores) a [elastického fondu: změnit vCores](#elastic-pool-change-vcores).
- Optimalizace dotazy ke snížení využití prostředků každý dotaz. Další informace najdete v tématu [dotaz optimalizace/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Úložiště

Pokud místo databáze používá dosáhne omezení maximální velikosti, vloží databáze a dojde k selhání aktualizací, které se zvyšují velikost dat a klienti obdrží [chybová zpráva](sql-database-develop-error-messages.md). Databáze VYBERE a odstranění pokračujte proběhla úspěšně.

Při zjištění vysoké využití, tyto možnosti omezení rizik:

- Zvýšení maximální velikosti databáze nebo elastického fondu nebo změnit úroveň výkonu zvýšit maximální velikost úložiště. V tématu [limitů prostředků na základě vCore SQL Database](sql-database-vcore-resource-limits.md).
- Pokud se databáze nachází v elastickém fondu, pak případně databázi lze přesunout mimo fondu tak, aby jeho prostorem úložiště není sdílený s jiné databáze.

### <a name="sessions-and-workers-requests"></a>Relace a pracovníci (počet požadavků) 

Určuje maximální počet relací a pracovníkům úrovně služby a výkonu. Nové požadavky byly zamítnuty, když relace nebo pracovní limitu a klienti obdrží chybovou zprávu. Když počet připojení, které jsou k dispozici je řízena aplikace, je často těžší odhadnout a řídit počet souběžných pracovních procesů. To platí hlavně během období zatížení ve špičce, když databáze prostředků limitu a pracovníci hromadí kvůli delší spuštěné dotazy. 

Při zjištění vysoké využití relace nebo pracovního procesu, možnosti omezení rizik:
- Zvýšení úrovně služby vrstvě nebo výkonu databáze nebo elastického fondu. V tématu [jedné databáze: změnit velikost úložiště](#single-database-change-storage-size), [jedné databáze: změnit vCores](#single-database-change-vcores), [elastického fondu: změnit velikost úložiště](#elastic-pool-change-storage-size), a [elastického fondu: změnit vCores ](#elastic-pool-change-vcores).
- Optimalizace dotazy ke snížení využití prostředků každý dotaz, je-li příčinou využití vyšší pracovníka způsobeno kolizí pro výpočetní prostředky. Další informace najdete v tématu [dotaz optimalizace/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>Další postup

- V tématu [nejčastější dotazy k databázi SQL](sql-database-faq.md) odpovědi na nejčastější dotazy.
- Informace o obecných omezeních Azure najdete v tématu [předplatného Azure a omezení služby, kvóty a omezení](../azure-subscription-service-limits.md).
