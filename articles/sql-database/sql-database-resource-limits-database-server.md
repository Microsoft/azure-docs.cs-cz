---
title: Limity prostředků databáze Azure SQL | Dokumenty společnosti Microsoft
description: Tento článek poskytuje přehled omezení prostředků Azure SQL Database pro jednotlivé databáze a elastické fondy. Poskytuje také informace o tom, co se stane, když jsou tyto limity prostředků přístupů nebo překročení.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 11/19/2019
ms.openlocfilehash: 550c315023c0ae907c369778c81b16e137004bec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067253"
---
# <a name="sql-database-resource-limits-and-resource-governance"></a>Omezení prostředků databáze SQL a zásady správného řízení prostředků

Tento článek obsahuje přehled omezení prostředků databáze SQL pro sql databázový server, který spravuje jednotlivé databáze a elastické fondy. Poskytuje informace o tom, co se stane, když jsou tyto limity prostředků přístupů nebo překročena a popisuje mechanismy zásad správného řízení prostředků používané k vynucení těchto limitů.

> [!NOTE]
> Omezení spravovaných instancí naleznete [v tématu limity prostředků databáze SQL pro spravované instance](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Maximální limity prostředků

| Prostředek | Omezení |
| :--- | :--- |
| Databáze na server | 5000 |
| Výchozí počet serverů na odběr v libovolné oblasti | 20 |
| Maximální počet serverů na jedno předplatné v libovolné oblasti | 200 |  
| Kvóta DTU / eDTU na server | 54,000 |  
| kvóta virtuálních jader na server/instanci | 540 |
| Maximální počet bazénů na server | Omezeno počtem DTU nebo virtuálních jader. Například pokud každý fond je 1000 DTU, pak server může podporovat 54 fondů.|
|||

> [!IMPORTANT]
> Vzhledem k tomu, že počet databází se blíží limitu na databázový server SQL, může dojít k následujícímu:
>
> - Zvýšení latence při spouštění dotazů proti hlavní databázi.  To zahrnuje zobrazení statistiky využití zdrojů, jako je například sys.resource_stats.
> - Zvýšení latence v operacích správy a vykreslování zobrazení portálu, které zahrnují výčet databází na serveru.

> [!NOTE]
> Chcete-li získat více kvót DTU/eDTU, kvóty virtuálních jader nebo více serverů, než je výchozí částka, odešlete novou žádost o podporu na webu Azure Portal. Další informace naleznete v [tématu Požadavek na zvýšení kvóty pro Azure SQL Database](quota-increase-request.md).

### <a name="storage-size"></a>Velikost úložiště

Pro velikosti úložiště prostředků jednotlivých databází najdete omezení [prostředků založených na DTU](sql-database-dtu-resource-limits-single-databases.md) nebo [omezení prostředků založených na virtuálních jádrech](sql-database-vcore-resource-limits-single-databases.md) pro omezení velikosti úložiště na cenovou úroveň.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Co se stane, když jsou dosažena omezení prostředků databáze

### <a name="compute-dtus-and-edtus--vcores"></a>Výpočetní (DTU a eDTU / virtuální jádra)

Když využití výpočetních prostředků databáze (měřeno pomocí DTU a eDTU nebo virtuálních jader) se stane vysokou, latence dotazu se zvýší a dotazy mohou dokonce časový limit. Za těchto podmínek mohou být dotazy zařazeny do fronty službou a jsou poskytovány prostředky pro spuštění jako prostředky stanou volné.
Při setkání s vysokým využitím výpočetních prostředků zahrnují možnosti zmírnění rizik:

- Zvýšení výpočetní velikost databáze nebo elastického fondu poskytnout databázi více výpočetních prostředků. Viz [Škálování prostředků jedné databáze](sql-database-single-database-scale.md) a [škálování prostředků elastického fondu](sql-database-elastic-pool-scale.md).
- Optimalizace dotazů za účelem snížení využití prostředků každého dotazu. Další informace naleznete v [tématu Optimalizace dotazu/nápovědy](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Úložiště

Pokud využité místo v databázi dosáhne maximálního limitu velikosti, vloží se a aktualizuje databázi, které zvětšují velikost dat, se nezdaří a klienti obdrží [chybovou zprávu](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md). Příkazy SELECT a DELETE jsou nadále úspěšné.

Při setkání s vysokým využitím místa, možnosti zmírnění patří:

- Zvýšení maximální velikost databáze nebo elastického fondu nebo přidání další úložiště. Viz [Škálování prostředků jedné databáze](sql-database-single-database-scale.md) a [škálování prostředků elastického fondu](sql-database-elastic-pool-scale.md).
- Pokud je databáze v elastickém fondu, pak alternativně databáze lze přesunout mimo fond tak, aby jeho úložný prostor není sdílens jinými databázemi.
- Zmenšete databázi, abyste získali zpět nevyužité místo. Další informace najdete [v tématu Správa místa v souborech v Azure SQL Database.](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Sessions a pracovníci (požadavky)

Maximální počet relací a pracovníků je určen úrovní služby a velikostí výpočetních prostředků (DTU/eDTU nebo virtuálních jader). Nové požadavky jsou odmítnuty při dosažení omezení relace nebo pracovního procesu a klienti obdrží chybovou zprávu. Zatímco počet dostupných připojení lze řídit aplikací, počet souběžných pracovníků je často těžší odhadnout a řídit. To platí zejména během období zatížení ve špičce, kdy jsou dosaženy limity prostředků databáze a pracovníci hromadit z důvodu delší spuštěné dotazy, velké blokování řetězy nebo nadměrné paralelismu dotazu.

Při výskytu vysoké relace nebo využití pracovníka zahrnují možnosti zmírnění:

- Zvýšení úrovně služby nebo výpočetní velikost databáze nebo elastického fondu. Viz [Škálování prostředků jedné databáze](sql-database-single-database-scale.md) a [škálování prostředků elastického fondu](sql-database-elastic-pool-scale.md).
- Optimalizace dotazů ke snížení využití prostředků každého dotazu, pokud je příčinou zvýšené využití pracovníka kvůli tvrzení o výpočetních prostředků. Další informace naleznete v [tématu Optimalizace dotazu/nápovědy](sql-database-performance-guidance.md#query-tuning-and-hinting).
- Snížení nastavení [MAXDOP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) (maximální stupeň paralelismu).
- Optimalizace zatížení dotazu ke snížení počtu výskytů a trvání blokování dotazů.

### <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>Spotřeba prostředků podle uživatelských úloh a interních procesů

Využití procesoru a paměti podle zatížení uživatelů v každé databázi je hlášeno v [zobrazeních sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) a [sys.resource_stats,](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) ve `avg_cpu_percent` sloupcích a `avg_memory_usage_percent` ve sloupcích. U elastických fondů je v zobrazení [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) uvedena spotřeba prostředků na úrovni fondu. Využití procesoru zatížení uživatelů `cpu_percent` se také hlásí prostřednictvím [metriky](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases) Azure Monitor pro jednotlivé databáze a [elastické fondy](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) na úrovni fondu.

Azure SQL Database vyžaduje výpočetní prostředky k implementaci funkcí základní služby, jako je vysoká dostupnost a zotavení po havárii, zálohování a obnovení databáze, monitorování, úložiště dotazů, automatické ladění atd. Systém vyčleňuje určitou omezenou část celkových prostředků pro tyto interní procesy pomocí mechanismů [zásad správného řízení prostředků,](#resource-governance) takže zbývající prostředky jsou k dispozici pro zatížení uživatelů. V době, kdy interní procesy nepoužívají výpočetní prostředky, systém je zpřístupňuje úlohám uživatelů.

Celková spotřeba procesoru a paměti podle uživatelských úloh a interních procesů na instanci serveru SQL Server hostující jednu `avg_instance_cpu_percent` `avg_instance_memory_percent` databázi nebo elastický fond je uvedena v [zobrazeních sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) a [sys.resource_stats,](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) ve sloupcích a ve sloupcích. Tato data se také `sqlserver_process_core_percent` `sqlserver_process_memory_percent` hlásí prostřednictvím metriky a Azure Monitor, pro jednotlivé databáze a [elastické](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) [fondy](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases) na úrovni fondu.

Podrobnější rozdělení nedávné spotřeby prostředků podle zatížení uživatelů a interních procesů je uvedeno v [zobrazeních sys.dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) a [sys.dm_resource_governor_workload_groups_history_ex.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database) Podrobnosti o fondech prostředků a skupinách úloh, na které odkazuje tato zobrazení, naleznete v [tématu Zásadsprávné hodování prostředků](#resource-governance). Tato zobrazení sestavu využití prostředků podle uživatelských úloh a konkrétních interních procesů v přidružených fondech prostředků a skupinách úloh.

V souvislosti s monitorováním výkonu a odstraňováním potíží je důležité vzít v úvahu **spotřebu procesoru uživatele** (`avg_cpu_percent`, `cpu_percent`) a **celkovou spotřebu procesoru** podle uživatelských úloh a interních procesů (`avg_instance_cpu_percent`,`sqlserver_process_core_percent`).

**Spotřeba procesoru uživatele** se vypočítá jako procento omezení zatížení uživatele v každém účelu služby. **Využití procesoru uživatele** na 100 % označuje, že zatížení uživatele dosáhlo limitu cíle služby. Pokud však **celková spotřeba procesoru** dosáhne rozsahu 70–100 %, je možné zobrazit sloučení propustnosti zatížení uživatelů a zvýšení latence dotazu, a to i v případě, že **hlášená spotřeba procesoru uživatele** zůstane výrazně pod 100 %. K tomu pravděpodobně dochází při použití menších cílů služby s mírným přidělením výpočetních prostředků, ale relativně intenzivními uživatelskými úlohami, například v [hustých elastických fondech](sql-database-elastic-pool-resource-management.md). K tomu může dojít také u menších cílů služby, když interní procesy dočasně vyžadují další prostředky, například při vytváření nové repliky databáze.

Pokud je **celková spotřeba procesoru** vysoká, možnosti zmírnění jsou stejné jako výše uvedené a zahrnují zvýšení cíle služby nebo optimalizaci zatížení uživatelů.

## <a name="resource-governance"></a>Zásady správného řízení prostředků

K vynucení omezení prostředků azure SQL database používá implementaci zásad správného řízení prostředků, která je založena na SQL Server [Správce prostředků](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor), upravené a rozšířené pro spuštění databázové služby SQL Server v Azure. Na každé instanci serveru SQL Server ve službě existuje více [fondů prostředků](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool) a [skupin úloh](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group), s omezeními prostředků nastavenými na úrovni fondu i skupiny, aby byla [zajištěna vyvážená databáze jako služba](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/). Zatížení uživatelů a interní úlohy jsou rozděleny do samostatných fondů prostředků a skupin pracovních vytížení. Zatížení uživatelů na primární a čitelné sekundární repliky, včetně geografické `SloSharedPool1` repliky, je klasifikován do fondu prostředků a `UserPrimaryGroup.DBId[N]` skupiny úloh, kde `N` stojí hodnotu ID databáze. Kromě toho existuje více fondů prostředků a skupiny úloh pro různé interní úlohy.

Kromě použití správce prostředků k řízení prostředků v rámci procesu SQL Server používá Azure SQL Database také [objekty úloh systému](https://docs.microsoft.com/windows/win32/procthread/job-objects) Windows pro zásady správného řízení prostředků na úrovni procesu a Správce prostředků souborového serveru Windows [(FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) pro správu kvót úložiště.

Azure SQL Database zabezpečení prostředků je hierarchické povahy. Shora dolů jsou limity vynuceny na úrovni operačního systému a na úrovni svazku úložiště pomocí mechanismů zásad správného řízení prostředků operačního systému a guvernéra prostředků, pak na úrovni fondu prostředků pomocí programu Resource Governor a potom na úrovni skupiny úloh pomocí Správce zdrojů. Omezení zásad správného řízení prostředků platné pro aktuální databázi nebo elastický fond jsou kčlánku v zobrazení [sys.dm_user_db_resource_governance.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 

### <a name="data-io-governance"></a>Správa vi. pro data

Zásadsprávné řízení vi dat je proces v Azure SQL Database slouží k omezení čtení i zápisu fyzické vi proti datové soubory databáze. Limity IOPS jsou nastaveny pro každou úroveň služby minimalizovat efekt "hlučný soused", poskytovat spravedlivé přidělení prostředků ve službě s více klienty a zůstat v rámci možností základního hardwaru a úložiště.

Pro jednotlivé databáze omezení skupiny úloh se vztahují na všechny vstupně-maily úložiště proti databázi, zatímco omezení fondu prostředků `tempdb` platí pro všechny vstupně-maily úložiště proti všem databázím na stejné instanci SERVERU SQL, včetně databáze. Pro elastické fondy omezení skupiny úloh platí pro každou databázi ve fondu, `tempdb` zatímco omezení fondu prostředků platí pro celý elastický fond, včetně databáze, která je sdílena mezi všechny databáze ve fondu. Obecně platí omezení fondu prostředků nemusí být dosažitelné zatížení proti databázi (jeden nebo sdružené), protože omezení skupiny úloh jsou nižší než limity fondu prostředků a omezení vstupně-sad/propustnost dříve. Omezení fondu však může být dosaženo kombinované zatížení proti více databází na stejné instanci serveru SQL Server.

Například pokud dotaz generuje 1000 VOPS bez jakékoli všaze prostředků VIf, ale maximální limit viop skupiny úloh je nastavena na 900 VOPS, dotaz nebude moci generovat více než 900 VOPS. Pokud je však maximální limit viop fondu zdrojů nastaven na 1500 viops a celkový vod va ze všech skupin úloh přidružených k fondu prostředků překročí 1500 VOPS, může být vipo stejného dotazu sníženpod limit pracovní skupiny 900 VOPS.

Vstupně-up a hodnota min/max propustnosti vrácená [sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) zobrazení fungují jako limity/limity, nikoli jako záruky. Dále zásadsprávné hodžení prostředků nezaručuje žádné konkrétní latence úložiště. Nejlepší dosažitelná latence, VOPS a propustnost pro danou úlohu uživatele závisí nejen na omezeních zásad správného řízení vlimbů vi, ale také na kombinaci použitých velikostí vi a na možnostech základního úložiště. SQL Server používá vos, které se liší ve velikosti mezi 512 KB a 4 MB. Pro účely vynucení limitů viops každý vi se započítává bez ohledu na jeho velikost, s výjimkou databází s datovými soubory ve službě Azure Storage. V takovém případě vi větší než 256 KB jsou účtovány jako více 256 KB IOs, zarovnat s azure storage IO účetnictví.

Pro základní, standardní a obecné účely databází, které používají `primary_group_max_io` datové soubory ve službě Azure Storage, hodnota nemusí být dosažitelné, pokud databáze nemá dostatek datových souborů, aby kumulativně poskytnout tento počet VOPS, nebo pokud data není distribuovánrovnoměrně mezi soubory, nebo pokud úroveň výkonu základní objekty blobomezuje IOPS/propustnost pod limit zásad správného řízení prostředků. Podobně s malé vi protokolu generované časté potvrzení `primary_max_log_rate` transakce, hodnota nemusí být dosažitelné zatížení z důvodu limitu VOPS na základní objekt blob úložiště Azure.

Hodnoty využití zdrojů, `avg_data_io_percent` `avg_log_write_percent`například a , uvedené v [zobrazeních sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)a [sys.elastic_pool_resource_stats,](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) se počítají jako procenta maximálních limitů zásad správného řízení zdrojů. Proto když faktory jiné než omezení zásad správného řízení prostředků vstupně-výstupních operací/ propustnost, je možné zobrazit vstupně-výstupních operací a latence zvyšuje s rostoucím zatížením, i když hlášené využití prostředků zůstává pod 100 %. 

Chcete-li zobrazit čtení a zápis VOPS, propustnost a latence na databázový soubor, použijte funkci [sys.dm_io_virtual_file_stats().](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) Tato funkce zobrazí všechny vi proti databázi, včetně videa na pozadí, která není zaúčtována k `avg_data_io_percent`, ale používá VOPS a propustnost základního úložiště a může ovlivnit pozorované latence úložiště. Funkce také zobrazí další latence, které mohou být zavedeny vstupně-výstupních prostředků pro čtení a zápisy `io_stall_queued_read_ms` ve sloupcích a `io_stall_queued_write_ms` ve sloupcích.

### <a name="transaction-log-rate-governance"></a>Zásadcovařízení sazeb protokolu transakcí

Zásadsprávné řízení rychlosti transakčního protokolu je proces v Azure SQL Database, který se používá k omezení vysokých ingestování pro úlohy, jako je hromadné vkládání, VÝBĚR INTO a sestavení indexu. Tato omezení jsou sledovány a vynuceny na poddruhé úrovni rychlost generování záznamu protokolu, omezení propustnosti bez ohledu na to, kolik vstupně-line může být vydáno proti datovým souborům.  Rychlost generování protokolu transakcí aktuálně lineárně škálovat až do bodu, který je závislý na hardwaru, přičemž maximální povolená rychlost protokolu je 96 MB/s s nákupním modelem virtuálních jader. 

> [!NOTE]
> Skutečné fyzické vifikovat položky do souborů protokolu transakcí nejsou řízeny nebo omezeny.

Sazby protokolu jsou nastaveny tak, aby je bylo možné dosáhnout a udržet v různých scénářích, zatímco celkový systém může zachovat své funkce s minimálním dopadem na zatížení uživatele. Zásady správného řízení rychlosti protokolů zajišťují, že zálohy protokolů transakcí zůstanou v rámci publikovaných sla obnovitelné služby.  Toto zásadsprávné řízení také zabraňuje nadměrné nevyřízené položky na sekundární repliky.

Jako záznamy protokolu jsou generovány, každá operace je vyhodnocena a vyhodnocena, zda by měla být zpožděna, aby byla zachována maximální požadovaná rychlost protokolu (MB/s za sekundu). Zpoždění nejsou přidány při záznamy protokolu jsou vyprázdněny do úložiště, spíše zásadsprávné řízení rychlosti protokolu se použije během generování rychlosti protokolu sám.

Skutečné rychlosti generování protokolu uložené za běhu mohou být také ovlivněny mechanismy zpětné vazby, které dočasně snižují přípustné rychlosti protokolů, aby se systém mohl stabilizovat. Správa prostoru protokolu, vyhnout se spuštění mimo podmínky místa protokolu a dostupnost skupiny replikační mechanismy mohou dočasně snížit celkové limity systému.

Rychlost log regulátoru tvarování provozu se objeví pomocí následujících typů čekání (vystavené v [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV):

| Typ čekání | Poznámky |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Omezení databáze |
| POOL_LOG_RATE_GOVERNOR | Omezení fondu |
| INSTANCE_LOG_RATE_GOVERNOR | Omezení úrovně instance |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Řízení zpětné vazby, fyzická replikace skupiny dostupnosti v premium/business critical nedrží krok |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Řízení zpětné vazby, omezení sazeb, aby se zabránilo mimo protokol prostor stavu |
|||

Při výskytu omezení rychlosti protokolu, který brání požadované škálovatelnosti, zvažte následující možnosti:
- Vertikálně navýšit kapacitu na vyšší úroveň služeb, abyste získali maximální rychlost protokolu 96 MB/s nebo přepněte na jinou úroveň služeb. Úroveň služby [Hyperscale](sql-database-service-tier-hyperscale.md) poskytuje rychlost protokolu 100 MB/s bez ohledu na zvolenou úroveň služby.
- Pokud jsou načtená data přechodná, například pracovní data v procesu ETL, lze je načíst do databáze tempdb (která je minimálně protokolována). 
- Pro analytické scénáře načtěte do clusterované tabulky pokryté columnstore. Tím se sníží požadovaná rychlost protokolu z důvodu komprese. Tato technika zvyšuje využití procesoru a je použitelná pouze pro datové sady, které využívají clusterované indexy columnstore. 

## <a name="next-steps"></a>Další kroky

- Informace o obecných omezeních Azure najdete v tématu [Omezení předplatného a služeb Azure, kvóty a omezení](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Informace o dtu a elektronické dálné, viz [DTU a eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model).
- Informace o omezení velikosti tempdb naleznete [v tématu TempDB v Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
