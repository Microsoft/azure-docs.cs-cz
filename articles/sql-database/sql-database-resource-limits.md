---
title: "Omezení prostředků Azure SQL Database | Microsoft Docs"
description: "Tato stránka popisuje některé běžné limitů prostředků pro databázi SQL Azure."
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 03/15/2018
ms.author: carlrab
ms.openlocfilehash: 6e6aaeea0375e92d7913e47c6b929a469ceb5c94
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="azure-sql-database-resource-limits"></a>Omezení prostředků Azure SQL Database

> [!IMPORTANT]
> Omezení prostředků s Azure spravované Instance databáze SQL, najdete v části [spravované Instance databáze SQL](sql-database-managed-instance.md#managed-instance-service-tier).

## <a name="single-database-storage-sizes-and-performance-levels"></a>Izolované databáze: velikosti úložiště a úrovně výkonu

Pro izolované databáze v následujících tabulkách prostředky k dispozici pro jednu databázi na úrovni jednotlivých služeb a výkonu. Můžete nastavit úroveň služby, úroveň výkonu a velikost úložiště pro jednu databázi pomocí [portál Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](sql-database-single-database-resources.md#manage-single-database-resources-using-transact-sql), [prostředí PowerShell](sql-database-single-database-resources.md#manage-single-database-resources-using-powershell), [Rozhraní příkazového řádku azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-cli), nebo [rozhraní REST API](sql-database-single-database-resources.md#manage-single-database-resources-using-the-rest-api).

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

## <a name="single-database-change-storage-size"></a>Izolované databáze: Změna velikosti úložiště

- Cena jednotky DTU pro jednu databázi zahrnuje množství úložiště bez dalších poplatků. Nad rámec zahrnuté množství dodatečné úložiště se dá zřídit další náklady až do limitu maximální velikost v přírůstcích po 250 GB až 1 TB a pak v přírůstcích po 256 GB nad rámec 1 TB. Objemy zahrnuté úložiště a omezení maximální velikosti najdete v tématu [jedné databáze: velikosti úložiště a úrovně výkonu](#single-database-storage-sizes-and-performance-levels).
- Dodatečné úložiště pro izolované databáze může být zřízen zvýšením jeho maximální velikost pomocí [portál Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [prostředí PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure Rozhraní příkazového řádku](/cli/azure/sql/db#az_sql_db_update), nebo [rozhraní REST API](/rest/api/sql/databases/update).
- Cena dodatečné úložiště pro jednu databázi je velikost úložiště navíc násobí hodnotou jednotkové ceny dodatečné úložiště vrstvy služby. Podrobnosti na cenu dodatečné úložiště najdete v tématu [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="single-database-change-dtus"></a>Izolované databáze: změnit počet jednotek Dtu

Po výběru původně vrstvy služeb, úroveň výkonu a velikost úložiště, můžete škálovat jedné databáze nahoru nebo dolů dynamicky podle skutečné prostředí pomocí [portál Azure](sql-database-single-database-resources.md#manage-single-database-resources-using-the-azure-portal), [Transact-SQL](/sql/t-sql/statements/alter-database-azure-sql-database#examples), [Prostředí PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [rozhraní příkazového řádku Azure](/cli/azure/sql/db#az_sql_db_update), nebo [rozhraní REST API](/rest/api/sql/databases/update). 

Následující video ukazuje, dynamická změna úroveň výkonu zvýšit dostupné Dtu pro jednu databázi.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

Se změnou úrovně služeb nebo úrovně výkonu databáze se vytvoří replika původní databáze na nové úrovni výkonu a následně se přepnou připojení na repliku. Během tohoto procesu se neztratí žádná data, ale během krátké chvíle, kdy se přepíná na repliku, jsou zakázána připojení k databázi, takže může dojít k vrácení některých probíhajících transakcí zpět. Doba pro přechod se liší, ale je obecně méně než 30 sekundách 99 % času je v části 4 sekundy. Pokud máte velké množství transakcí pohybující se v okamžiku připojení jsou zakázané, může být delší dobu pro přechod. 

Délka trvání celého procesu vertikálního navyšování kapacity závisí na velikosti a úrovni služeb databáze před změnou a po ní. Například 250 GB databázi, která mění k, z a do vrstvy standardní služby, by se měla dokončit v rámci šest hodin. Pro databázi stejnou velikost, která je změna úrovně výkonu v rámci úroveň služeb Premium by se měla dokončit škálování do tří hodin.

> [!TIP]
> Monitorování operací v mailech naleznete v tématu: [spravovat operace pomocí SQL REST API](/rest/api/sql/Operations/List), [spravovat operace pomocí rozhraní příkazového řádku](/cli/azure/sql/db/op), [sledovat operace pomocí T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) a tyto dvě Příkazy prostředí PowerShell: [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) a [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity).

* Pokud provádíte upgrade na vyšší úroveň výkonu a vrstvu služby, maximální velikost databáze se nezvyšuje, pokud je explicitně zadat větší velikost (maxsize).
* Přejít na starší verzi databáze, databáze používá prostor musí být menší než maximální povolená velikost úroveň cílové služby a výkonu. 
* Když přechod na starší verzi z **Premium** k **standardní** vrstvy, náklady na úložiště navíc platí v případě, že (1) je maximální velikost databáze je podporována v úroveň výkonu cílové i (2) je maximální velikost překračuje úložiště zahrnuté množství na úroveň výkonu cílové. Například pokud databázi P1 a maximální velikost 500 GB je downsized k S3, pak náklady na úložiště navíc platí od S3 podporuje maximální velikost 500 GB a jeho součástí úložiště je pouze 250 GB. Ano velikost úložiště je 500 GB – 250 GB = 250 GB. Ceny navíc úložiště, najdete v části [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/). Pokud skutečná velikost využitého místa je menší než množství součástí úložiště, pak tato zpoplatněné se vyhnout snížením maximální velikost databáze na zahrnuté množství. 
* Při upgradování databáze s [geografická replikace](sql-database-geo-replication-portal.md) povoleno, upgradujte její sekundární databáze úrovně požadovaný výkon před upgradem primární databáze (Obecné pokyny). Při upgradu na jiný, upgrade sekundární databázi nejprve je vyžadováno.
* Když přechod na starší verzi databáze s [geografická replikace](sql-database-geo-replication-portal.md) povoleno, starší verzi její primární databáze k vrstvě požadovaný výkon před snížením sekundární databáze (Obecné pokyny). Při přechod na starší verzi na jinou edici, přechod na starší verzi primární databáze nejprve je vyžadováno.
* Nabídky služeb pro obnovení se u různých úrovní služeb liší. Pokud jsou Downgrade k **základní** vrstvy, není obdobím uchovávání záloh – viz [zálohy databáze SQL Azure](sql-database-automated-backups.md).
* Nové vlastnosti databáze se nepoužijí, dokud nebudou změny dokončeny.

## <a name="single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>Izolované databáze: omezení P11 a P15 při maximální velikosti větší než 1 TB

Maximální velikost větší než 1 TB P11 a P15 databáze je podporováno v následujících oblastech: nám East2, západní USA, nám verze pro státní správu Virginia, západní Evropa, Německo centrální, Jižní východní Asie, Japonsko – východ, Kanada centrální a Východní Kanada. Následující požadavky a omezení platí pro databáze P11 a P15 s maximální velikostí větší než 1 TB:

- Pokud si zvolíte maximální velikost větší než 1 TB, když se vytváří databáze (pomocí hodnotu 4 TB nebo 4096 GB), provedení příkazu pro vytvoření selže s chybou, pokud databáze je zajištěna v nepodporované oblast.
- Pro existující P11 a P15 databáze nachází v jedné z podporovaných oblastí, můžete zvýšit maximální velikost úložiště na nad rámec 1 TB v přírůstcích po 256 GB až 4 TB. Pokud chcete zobrazit, pokud větší velikost je podporován ve vašem regionu, použijte [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) funkce nebo zkontrolujte velikost databáze na portálu Azure. Upgrade na existující P11 nebo P15 databáze lze provést pouze pomocí hlavního přihlášení na úrovni serveru nebo pomocí členové databázové role dbmanager. 
- Pokud se operace upgradu se spustí v podporované oblasti konfigurace aktualizoval okamžitě. Během procesu upgradu zůstane online databáze. Nelze však využívat celkovou velikost úložiště na více než 1 TB úložiště, dokud se neupgradují skutečné databázové soubory do nové maximální velikost. Délka čas potřebný závisí na velikosti databáze během upgradu. 
- Při vytváření nebo aktualizaci P11 nebo P15 databáze, můžete zvolit pouze 1 TB a 4 TB maximální velikost v přírůstcích po 256 GB. Při vytváření P11/P15, je předem vybranou výchozí možnost úložiště o velikosti 1 TB. Pro databáze nachází v jedné z podporovaných oblastí můžete zvýšit maximální úložiště pro maximálně 4 TB pro jednu databázi nové nebo existující. Všechny ostatní země nelze zvýšit maximální velikost, vyšší než 1 TB. Ceny nezmění, když vyberete 4 TB součástí úložiště.
- Pokud je maximální velikost databáze nastavené na větší než 1 TB, pak jej nelze změnit na 1 TB i v případě, že skutečné úložiště používané je menší než 1 TB. Proto není možné snížit P11 nebo P15 s maximální velikostí větší než 1 TB 1 TB P11 nebo 1 TB P15 nebo snížení výkonu vrstvy, jako je například P1 P6). Toto omezení platí také pro obnovení a scénáře kopírováním včetně bodu v čase, geografické obnovení, dlouhodobý-dlouhodobé zálohování uchovávání a kopie databáze. Jakmile je databáze nakonfigurovaná se maximální velikost větší než 1 TB, musí být spuštěn všechny operace obnovení této databáze do P11 nebo P15 s maximální velikostí větší než 1 TB.
- Pro aktivní geografickou replikací scénáře:
   - Nastavení vztahu geografická replikace: Pokud primární databáze je P11 nebo P15, secondary(ies) musí být také P11 nebo P15; nižší úrovně výkonu byly zamítnuty jako sekundární databáze, protože nejsou schopný zajistit podporu více než 1 TB.
   - Upgrade z primární databáze ve vztahu geografická replikace: Změna maximální velikosti více než 1 TB na primární databázi aktivuje stejné změny v sekundární databázi. Obě upgrady musí být úspěšná změna na primárním vstoupily v platnost. Platí omezení oblast pro možnost více než 1 TB. Je-li sekundární v oblasti, která nepodporuje více než 1 TB, není aktualizován primární.
- Použití službu Import/Export načítání P11/P15 databáze s více než 1 TB není podporováno. Použít SqlPackage.exe k [importovat](sql-database-import.md) a [exportovat](sql-database-export.md) data.

## <a name="elastic-pool-storage-sizes-and-performance-levels"></a>Elastický fond: velikosti úložiště a úrovně výkonu

Pro databáze SQL elastické fondy následující tabulky popisují dostupné prostředky na úrovni jednotlivých služeb a výkonu. Můžete nastavit úroveň služby, úroveň výkonu a velikost úložiště pomocí [portál Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [prostředí PowerShell](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-powershell), [rozhraní příkazového řádku Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-cli), nebo [REST API](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-rest-api).

> [!NOTE]
> Omezení prostředků jednotlivých databází v elastické fondy jsou obvykle stejné jako u izolovaných databází mimo fondů na základě Dtu a vrstvy služeb. Maximální počet souběžných pracovních procesů pro databázi S2 je například 120 pracovních procesů. Ano maximální počet souběžných pracovních procesů pro databázi v standardní fond je také 120 pracovníci Pokud 50 Dtu (což odpovídá S2) je maximální počet jednotek DTU na databázi ve fondu.

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

Pokud se využívají všechny DTU elastického fondu, pak každá databáze ve fondu obdrží stejné množství prostředků ke zpracování dotazů. Služba SQL Database poskytuje spravedlivé sdílení prostředků mezi databázemi tím, že zajišťuje rovnoměrné rozdělení výpočetního času. Pokud je minimální počet DTU na databázi nastaven na nenulovou hodnotu, pro každou databázi bude garantováno spravedlivé sdílení prostředků elastického fondu je společně s libovolným množstvím prostředků.

### <a name="database-properties-for-pooled-databases"></a>Vlastnosti databáze pro databáze ve fondu

Následující tabulka popisuje vlastnosti pro databáze ve fondu.

| Vlastnost | Popis |
|:--- |:--- |
| Maximální počet eDTU na databázi |Maximální počet eDTU, které může využívat libovolná databáze ve fondu za předpokladu, že jsou dostupné v závislosti na využití ostatními databázemi ve fondu. Maximální počet eDTU na databázi není garancí prostředků pro databázi. Toto nastavení je globální a platí pro všechny databáze ve fondu. Nastavte maximální počet eDTU na databázi dostatečně vysoký, aby databáze zvládly využití ve špičkách. Očekává se určitý stupeň předimenzování, protože fond obecně předpokládá studené a horké vzory používání, při nichž nenastávají špičky u všech databází ve stejnou chvíli. Předpokládejme například, že využití ve špičce na databázi je 20 eDTU a špička v jednu chvíli nastává pouze u 20 % ze 100 databází ve fondu. Pokud je maximální počet eDTU na databázi nastaven na 20 eDTU, je rozumné fond 5krát předimenzovat a nastavit počet eDTU na fond na 400. |
| Minimální počet eDTU na databázi |Minimální počet eDTU garantovaných pro každou databázi ve fondu. Toto nastavení je globální a platí pro všechny databáze ve fondu. Minimální počet eDTU na databázi může být nastaven na 0, což je i výchozí hodnota. Tato vlastnost je nastavena na libovolnou hodnotu mezi 0 a průměrným využitím eDTU na databázi. Násobek počtu databází ve fondu a minimálním počtem eDTU na databázi nemůže překročit počet eDTU na fond. Pokud je například ve fondu 20 databází a minimální počet eDTU na databázi je nastaven na 10 eDTU, pak musí být počet eDTU na fond alespoň 200 eDTU. |
| Maximální počet úložiště na databázi |Maximální velikost úložiště pro databázi ve fondu. Databáze ve fondu sdílí úložiště fondu, proto je velikost úložiště databáze omezena na menší z hodnot zbývajícího úložiště fondu a maximální velikosti úložiště na databázi. Maximální velikost úložiště na databázi odkazuje na maximální velikost datových souborů a nezahrnuje místo, které využívají soubory protokolu. |
|||
 
## <a name="elastic-pool-change-storage-size"></a>Elastický fond: Změna velikosti úložiště

- Cena eDTU fondu elastické databáze zahrnuje množství úložiště bez dalších poplatků. Nad rámec zahrnuté množství dodatečné úložiště se dá zřídit další náklady až do limitu maximální velikost v přírůstcích po 250 GB až 1 TB a pak v přírůstcích po 256 GB nad rámec 1 TB. Objemy zahrnuté úložiště a omezení maximální velikosti najdete v tématu [elastického fondu: velikosti úložiště a úrovně výkonu](#elastic-pool-storage-sizes-and-performance-levels).
- Dodatečné úložiště pro elastický fond může být zřízen zvýšením jeho maximální velikost pomocí [portál Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [prostředí PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [rozhraní příkazového řádku Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), nebo [REST API ](/rest/api/sql/elasticpools/update).
- Cena dodatečné úložiště pro elastický fond je velikost úložiště navíc násobí hodnotou jednotkové ceny dodatečné úložiště vrstvy služby. Podrobnosti na cenu dodatečné úložiště najdete v tématu [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="elastic-pool-change-edtus"></a>Elastický fond: změnit počet jednotek Edtu

Můžete zvýšit nebo snížit fondu elastické databáze podle prostředků vyžaduje použití dostupné prostředky [portál Azure](sql-database-elastic-pool.md#manage-elastic-pools-and-databases-using-the-azure-portal), [prostředí PowerShell](/powershell/module/azurerm.sql/set-azurermsqlelasticpool), [rozhraní příkazového řádku Azure](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update), nebo [ Rozhraní REST API](/rest/api/sql/elasticpools/update).

- Pokud změny měřítka fondu, jsou stručně vyřadit připojení databáze. Toto je stejné chování jako nastane, když změny měřítka Dtu pro jednu databázi (ne ve fondu). Podrobnosti o doba trvání a dopad vynechaných připojení pro databázi během změny měřítka operací, najdete v části [změny měřítka Dtu pro jednu databázi](#single-database-change-storage-size). 
- Můžete nastavit velikost fondu pro dobu trvání závisí na celkovém množství prostoru úložiště používané všechny databáze ve fondu. Obecně platí, rescaling latence zobrazí průměr 90 minut nebo méně na 100 GB. Například pokud celkové místo používá všechny databáze ve fondu je 200 GB, pak očekávané latence pro změny měřítka fondu je 3 hodiny nebo méně. V některých případech v rámci úroveň Standard nebo Basic může být rescaling latence bez ohledu na množství prostoru v části pět minut.
- Obecně platí, doba trvání, chcete-li změnit minimální počet jednotek Edtu na databázi nebo max Edtu na databázi je pět minut nebo méně.
- Při downsizing fondu, musí být menší než maximální povolená velikost cílové služby vrstvy a fond Edtu fondu používá místo.
- Pokud změny měřítka fondu, náklady na úložiště navíc platí v případě, že cílový fond podporuje (1) je maximální velikost fondu úložiště, a (2) je maximální velikost úložiště přesahuje úložiště zahrnuté množství cílový fond. Například pokud 100 eDTU a maximální velikost 100 GB standardní fond je downsized na 50 eDTU standardní fond, pak náklady na úložiště navíc platí vzhledem k tomu, že cílový fond podporuje maximální velikost 100 GB a jeho součástí úložiště je jenom 50 GB. Velikost úložiště, takže je 100 GB – 50 GB = 50 GB. Ceny navíc úložiště, najdete v části [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/). Pokud skutečná velikost využitého místa je menší než množství součástí úložiště, pak tato zpoplatněné se vyhnout snížením maximální velikost databáze na zahrnuté množství. 

## <a name="what-is-the-maximum-number-of-servers-and-databases"></a>Jaký je maximální počet serverů a databází?

| Maximum | Hodnota |
| :--- | :--- |
| Databáze na serveru | 5000 |
| Počet serverů za předplatné podle oblastí | 21 |
|||

> [!IMPORTANT]
> Jako počet databází, se blíží limitu na serveru, může objevit následující:
> <br> • Latence zvýšení ve spuštění dotazů v hlavní databázi.  To zahrnuje zobrazení statistiky využití prostředků, jako je například sys.resource_stats.
> <br> • Zvýšení latence v operacích správy a vykreslování portálu hlediska, které se týkají databáze na serveru.

## <a name="what-happens-when-database-and-elastic-pool-resource-limits-are-reached"></a>Co se stane, když se dosáhne databáze a limitů elastického fondu prostředků?

### <a name="compute-dtus-and-edtus"></a>Výpočetní (Dtu a Edtu)

Když se stane vysoké využití výpočetních databáze (měřeno Dtu a Edtu), latence dotazu zvyšuje a můžete i vypršení časového limitu. Za těchto podmínek dotazy může být zařazeny do fronty pomocí služby a jsou poskytl uvolní prostředky pro spuštění jako prostředek.
Při zjištění vysokou výpočetní využití, tyto možnosti omezení rizik:

- Zvýšení úrovně výkonu databáze nebo elastický fond poskytnout další Dtu nebo Edtu databáze. V tématu [jedné databáze: změnit počet jednotek Dtu](#single-database-change-dtus) a [elastického fondu: změnit počet jednotek Edtu](#elastic-pool-change-edtus).
- Optimalizace dotazy ke snížení využití prostředků každý dotaz. Další informace najdete v tématu [dotaz optimalizace/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Úložiště

Pokud místo databáze používá dosáhne omezení maximální velikosti, vloží databáze a dojde k selhání aktualizací, které se zvyšují velikost dat a klienti obdrží [chybová zpráva](sql-database-develop-error-messages.md). Databáze VYBERE a odstranění pokračujte proběhla úspěšně.

Při zjištění vysoké využití, tyto možnosti omezení rizik:

- Zvýšení maximální velikosti databáze nebo elastického fondu nebo změnit úroveň výkonu získat více součástí úložiště. V tématu [limitů prostředků databáze SQL](sql-database-resource-limits.md).
- Pokud se databáze nachází v elastickém fondu, pak případně databázi lze přesunout mimo fondu tak, aby jeho prostorem úložiště není sdílený s jiné databáze.

### <a name="sessions-and-workers-requests"></a>Relace a pracovníci (počet požadavků) 

Určuje maximální počet souběžných relací a pracovních procesů a výkonu úrovně služby (Dtu a Edtu).  Nové požadavky byly zamítnuty, když relace nebo pracovní limitu a klienti obdrží chybovou zprávu. Když počet připojení, které jsou k dispozici je řízena aplikace, je často těžší odhadnout a řídit počet souběžných pracovních procesů. To platí hlavně během období zatížení ve špičce, když databáze prostředků limitu a pracovníci hromadí kvůli delší spuštěné dotazy. 

Při zjištění vysoké využití relace nebo pracovního procesu, možnosti omezení rizik:
- Zvýšení úrovně služby vrstvě nebo výkonu databáze nebo elastického fondu. V tématu [jedné databáze: změnit velikost úložiště](#single-database-change-storage-size), [jedné databáze: změnit počet jednotek Dtu](#single-database-change-dtus), [elastického fondu: změnit velikost úložiště](#elastic-pool-change-storage-size), a [elastického fondu: změnit počet jednotek Edtu ](#elastic-pool-change-edtus).
- Optimalizace dotazy ke snížení využití prostředků každý dotaz, je-li příčinou využití vyšší pracovníka způsobeno kolizí pro výpočetní prostředky. Další informace najdete v tématu [dotaz optimalizace/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="next-steps"></a>Další kroky

- Informace o úrovních služeb najdete v tématu [úrovních služeb](sql-database-service-tiers.md).
- Informace o izolovaných databází najdete v tématu [jedna databáze prostředků](sql-database-resource-limits.md).
- Informace o elastické fondy najdete v tématu [elastické fondy](sql-database-elastic-pool.md).
- Informace o obecných omezeních Azure najdete v tématu [předplatného Azure a omezení služby, kvóty a omezení](../azure-subscription-service-limits.md).
- Informace o Dtu a Edtu najdete v tématu [Dtu a Edtu](sql-database-what-is-a-dtu.md).
- Informace o databázi tempdb velikosti omezení naleznete v tématu https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database.
