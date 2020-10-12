---
title: Omezení prostředků pro logické servery v Azure
description: Tento článek poskytuje přehled omezení prostředků pro logický Server v Azure, který používají Azure SQL Database a Azure synapse Analytics. Poskytuje také informace o tom, co se stane, když dojde k dosažení nebo překročení těchto omezení prostředků.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 09/15/2020
ms.openlocfilehash: 6589211839a5c1667a6b5cef22220fd917f7e4af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91618956"
---
# <a name="resource-limits-for-azure-sql-database-and-azure-synapse-analytics-servers"></a>Omezení prostředků pro Azure SQL Database a Azure synapse Analytics Server
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Tento článek poskytuje přehled omezení prostředků pro logický Server, který používá Azure SQL Database a Azure synapse Analytics. Poskytuje informace o tom, co se stane, když dojde k dosažení těchto omezení prostředků, a popisuje mechanismy řízení prostředků, které se používají k vykonání těchto limitů.

> [!NOTE]
> Omezení pro Azure SQL Managed instance najdete v tématu [SQL Database omezení prostředků pro spravované instance](../managed-instance/resource-limits.md).

## <a name="maximum-resource-limits"></a>Maximum omezení prostředků

| Prostředek | Omezení |
| :--- | :--- |
| Databáze na server | 5000 |
| Výchozí počet serverů na předplatné v libovolné oblasti | 20 |
| Maximální počet serverů na předplatné v jakékoli oblasti | 200 |  
| Kvóta DTU/eDTU na server | 54 000 |  
| vCore kvóta na server/instanci | 540 |
| Maximální počet fondů na server | Omezeno počtem DTU nebo virtuální jádra. Pokud je například každý fond 1000 DTU, může server podporovat fondy 54.|
|||

> [!IMPORTANT]
> Vzhledem k tomu, že počet databází se blíží limitu na server, může dojít k následujícím akcím:
>
> - Zvýšení latence při spouštění dotazů na hlavní databázi.  Patří sem zobrazení statistik využití prostředků, například sys.resource_stats.
> - Zvýšení latence při operacích správy a v pohledech na portál pro vykreslování, které zahrnují vytváření výčtu databází na serveru.

> [!NOTE]
> Pokud chcete získat větší kvótu DTU/eDTU, vCore kvótu nebo více serverů než výchozí množství, odešlete novou žádost o podporu v Azure Portal. Další informace najdete v tématu [zvýšení kvóty žádostí o Azure SQL Database](quota-increase-request.md).

### <a name="storage-size"></a>Velikost úložiště

V případě velikostí úložiště prostředků pro izolované databáze se v omezeních velikosti úložiště na cenové úrovni podívejte na omezení [prostředků na základě DTU](resource-limits-dtu-single-databases.md) nebo na [Vcore omezení prostředků](resource-limits-vcore-single-databases.md) .

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Co se stane, když se dosáhnou omezení prostředků databáze

### <a name="compute-cpu"></a>Výpočetní procesor

Když je využití výpočetního procesoru databází vysoké, zvyšuje se latence dotazu a dotazy můžou dokonce zamezit i vypršení časového limitu. Za těchto podmínek můžou být dotazy ve službě zařazené do fronty a k dispozici jsou prostředky pro spuštění, protože prostředky budou bezplatné.
Pokud se setkáte s vysokým využitím výpočetních prostředků, zahrnují možnosti zmírnění:

- Zvýšením výpočetní velikosti databáze nebo elastického fondu poskytnete databázi s více výpočetními prostředky. Viz téma [škálování prostředků jedné databáze](single-database-scale.md) a [škálování prostředků elastického fondu](elastic-pool-scale.md).
- Optimalizace dotazů pro snížení využití prostředků procesoru u každého dotazu. Další informace najdete v tématu [ladění a hinty dotazů](performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Storage

Když využité místo v databázi dosáhne limitu maximální velikosti, vkládání a aktualizace databáze, které zvyšují velikost dat, selžou a klienti obdrží [chybovou zprávu](troubleshoot-common-errors-issues.md). Příkazy SELECT a DELETE budou i nadále úspěšné.

Při zjištění vysokého využití místa mají tyto možnosti omezení:

- Zvýšení maximální velikosti databáze nebo elastického fondu nebo Přidání úložiště. Viz téma [škálování prostředků jedné databáze](single-database-scale.md) a [škálování prostředků elastického fondu](elastic-pool-scale.md).
- Pokud se databáze nachází v elastickém fondu, pak je možné databázi přesunout mimo fond, aby její prostor úložiště nebyl sdílen s ostatními databázemi.
- Zmenšete databázi pro uvolnění nevyužitého místa. Další informace najdete v tématu [Správa místa v souboru v Azure SQL Database](file-space-manage.md)

### <a name="sessions-and-workers-requests"></a>Relace a pracovní procesy (požadavky)

Maximální počet relací a pracovních procesů závisí na úrovni služby a velikosti výpočtů (DTU/eDTU nebo virtuální jádra). Nové žádosti jsou odmítnuty, když jsou dosažena omezení relace nebo pracovního procesu, a klienti obdrží chybovou zprávu. I když může být počet dostupných připojení řízen aplikací, počet souběžných pracovních procesů je často těžší odhadnout a řídit. To platí hlavně během období špičky načítání při dosažení limitů prostředků databáze a pracovníci se budou moct dodávat v důsledku delšího spouštění dotazů, velkých blokujících řetězců nebo nadměrného paralelismu dotazů.

Pokud se setkáte s vysokým využitím relace nebo pracovního procesu, zahrnují možnosti zmírňování:

- Zvýšení úrovně služby nebo výpočetní velikosti databáze nebo elastického fondu. Viz téma [škálování prostředků jedné databáze](single-database-scale.md) a [škálování prostředků elastického fondu](elastic-pool-scale.md).
- Optimalizujte dotazy, aby se snížilo využití prostředků každého dotazu, pokud příčinou zvýšení využití pracovního procesu je spor pro výpočetní prostředky. Další informace najdete v tématu [ladění a hinty dotazů](performance-guidance.md#query-tuning-and-hinting).
- Zmenšení nastavení [MAXDOP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) (maximální úroveň paralelismu).
- Optimalizace úlohy dotazů za účelem snížení počtu výskytů a trvání blokování dotazů.

### <a name="memory"></a>Paměť

Na rozdíl od jiných prostředků (procesor, pracovní procesy, úložiště), které dosáhnou limitu paměti, nemá negativně vliv na výkon dotazů a nezpůsobuje chyby a selhání. Jak je podrobně popsáno v [Průvodci architekturou správy paměti](https://docs.microsoft.com/sql/relational-databases/memory-management-architecture-guide), SQL Server databázový stroj často používá veškerou dostupnou paměť, podle návrhu. Paměť se primárně používá pro ukládání dat do mezipaměti, aby nedocházelo k dražšímu přístupu k úložišti. Vyšší využití paměti proto obvykle zlepšuje výkon dotazů z důvodu rychlejšího čtení z paměti, nikoli pomalejšího čtení z úložiště.

Po spuštění databázového stroje, protože zatížení začne číst data ze služby Storage, databázový stroj agresivní data do paměti ukládá do mezipaměti. Po počátečním období rozkladu je běžné a očekávané, že se `avg_memory_usage_percent` `avg_instance_memory_percent` sloupce a v [Sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) zavřou nebo rovnají 100%, zejména pro databáze, které nejsou nečinné a které se plně nevejdou do paměti.

Kromě mezipaměti dat se paměť používá v jiných součástech databázového stroje. Když je pro paměť k dispozici poptávka a veškerá dostupná paměť byla využívána mezipamětí dat, databázový stroj bude dynamicky zmenšovat velikost mezipaměti dat, aby byla dostupná paměť pro jiné komponenty, a dynamicky rozšiřují mezipaměť dat, když ostatní součásti uvolní paměť.

Ve výjimečných případech může dostatečně náročné zatížení způsobit nedostatek paměti, což by vedlo k chybám způsobeným nedostatkem paměti. K tomu může dojít na jakékoli úrovni využití paměti mezi 0% a 100%. K tomu dochází s větším množstvím výpočetních velikostí, které mají poměrně menší limity paměti, a/nebo s úlohami, které využívají více paměti pro zpracování dotazů, jako jsou například [husté elastické fondy](elastic-pool-resource-management.md).

Při zjištění chyb při nedostatku paměti patří mezi ně i tyto možnosti:
- Zvýšení úrovně služby nebo výpočetní velikosti databáze nebo elastického fondu. Viz téma [škálování prostředků jedné databáze](single-database-scale.md) a [škálování prostředků elastického fondu](elastic-pool-scale.md).
- Optimalizace dotazů a konfigurací za účelem snížení využití paměti. Společná řešení jsou popsaná v následující tabulce.

|Řešení|Description|
| :----- | :----- |
|Snížení velikosti paměti pro udělení|Další informace o grantech paměti najdete v příspěvku na blogu [principy SQL Server paměti pro přidělení](https://techcommunity.microsoft.com/t5/sql-server/understanding-sql-server-memory-grant/ba-p/383595) . Běžným řešením pro zamezení nadměrného množství paměti je udržování [statistik](https://docs.microsoft.com/sql/relational-databases/statistics/statistics) v aktuálním stavu. Výsledkem je přesnější odhad spotřeby paměti modulem dotazu, což vyloučí nenutně náročné nároky na paměť.</br></br>V databázích využívajících úroveň kompatibility 140 a novější může databázový stroj automaticky upravovat velikost přidělené paměti pomocí [zpětné vazby přidělení paměti v režimu dávky](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-memory-grant-feedback). Databázový stroj v databázích využívajících úroveň kompatibility 150 a novější používá [zpětnou vazbu přidělení paměti v režimu řádků](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)pro další běžné dotazy v režimu řádků. Tato vestavěná funkce pomáhá předejít chybám způsobeným nedostatkem paměti z důvodu zbytečného udělení vysoké velikosti paměti.|
|Zmenšení velikosti mezipaměti plánu dotazů|Databázový stroj ukládá do mezipaměti plány dotazů v paměti, aby nedocházelo k kompilování plánu dotazů pro každé spuštění dotazu. Aby nedocházelo k dispozici determinističtější mezipaměti plánů dotazů, které jsou způsobené plány ukládání do mezipaměti, které se používají jenom jednou, povolte konfiguraci OPTIMIZE_FOR_AD_HOC_WORKLOADS v [oboru databáze](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).|
|Zmenšení velikosti paměti zámku|Databázový stroj používá paměť pro [zámky](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#Lock_Engine). Pokud je to možné, vyhněte se velkým transakcím, které mohou získat velký počet zámků a způsobit vysoké využití paměti.|


## <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>Spotřeba prostředků podle uživatelských zatížení a interních procesů

Využití CPU a paměti podle uživatelských úloh v každé databázi se oznamuje v zobrazeních [Sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) a [Sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) , ve `avg_cpu_percent` sloupcích a `avg_memory_usage_percent` . Pro elastické fondy se spotřeba prostředků na úrovni fondu hlásí v zobrazení [Sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) . Spotřeba procesoru uživatele je také hlášena prostřednictvím `cpu_percent` Azure monitor metriky pro izolované [databáze](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases) a [elastické fondy](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) na úrovni fondu.

Azure SQL Database vyžaduje výpočetní prostředky k implementaci základních funkcí služby, jako je vysoká dostupnost a zotavení po havárii, zálohování a obnovení databáze, monitorování, úložiště dotazů, automatické ladění atd. Systém nastavuje určitou omezené část celkových prostředků pro tyto interní procesy pomocí mechanismů [zásad správného řízení prostředků](#resource-governance) a zpřístupňuje zbývající prostředky pro úlohy uživatelů. V době, kdy interní procesy nepoužívají výpočetní prostředky, je systém zpřístupní pro úlohy uživatelů.

Celková spotřeba procesoru a paměti podle uživatelských zatížení a interních procesů je hlášena v zobrazení [Sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) a [Sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) , ve `avg_instance_cpu_percent` `avg_instance_memory_percent` sloupcích a. Tato data jsou také uvedena prostřednictvím `sqlserver_process_core_percent` metrik a `sqlserver_process_memory_percent` Azure monitor pro izolované [databáze](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases) a [elastické fondy](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) na úrovni fondu.

Podrobnější rozpis poslední spotřeby prostředků uživatelskými úlohami a interními procesy se oznamuje v zobrazeních [Sys.dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) a [Sys.dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database) . Podrobnosti o fondech prostředků a skupinách úloh, na které se odkazuje v těchto zobrazeních, najdete v tématu [řízení prostředků](#resource-governance). Tato zobrazení vykazují informace o využití prostředků uživateli a specifických interních procesech v přidružených fondech zdrojů a skupinách úloh.

V souvislosti s monitorováním výkonu a odstraňováním potíží je důležité vzít v úvahu využití **procesoru** uživatelů ( `avg_cpu_percent` , `cpu_percent` ) i **celkovou spotřebu procesoru** podle uživatelských zatížení a interních procesů ( `avg_instance_cpu_percent` , `sqlserver_process_core_percent` ).

**Spotřeba uživatelského procesoru** se počítá jako procento omezení uživatelských úloh v jednotlivých účelech služby. **Využití procesoru uživatele** v 100% znamená, že uživatelské zatížení dosáhlo limitu cíle služby. Pokud ale **Celková spotřeba procesoru** dosáhne 70-100% rozsahu, je možné zobrazit propustnost úloh uživatelů a zvýšit latenci dotazů, a to i v případě, že využití **procesoru uživatelem** zůstává výrazně pod 100%. Tato situace je pravděpodobnější, když používáte menší cíle služeb s mírným přidělením výpočetních prostředků, ale poměrně náročné úlohy uživatelů, například v [hustých elastických fondech](elastic-pool-resource-management.md). Tato situace může nastat i u menších cílů služby, když interní procesy dočasně vyžadují další prostředky, například při vytváření nové repliky databáze.

Pokud je **Celková spotřeba procesoru** vysoká, jsou možnosti zmírnění stejné jako v předchozích verzích a zahrnují optimalizaci cíle služby nebo optimalizaci uživatelských úloh.

## <a name="resource-governance"></a>Zásady správného řízení prostředků

Aby bylo možné vyhodnotit omezení prostředků, Azure SQL Database využívá implementaci zásad správného řízení prostředků, která je založena na SQL Server [Správce prostředků](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor), upraveno a rozšířeno na spouštění v Azure SQL Database. V SQL Database je víc [fondů zdrojů](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool) a [skupin úloh](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group)s omezeními prostředků nastavenými na úrovni fondu i skupin a poskytuje [vyváženou databázi jako službu](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/). Úlohy uživatelů a interní úlohy jsou klasifikovány do samostatných fondů zdrojů a skupin úloh. Uživatelské zatížení primárních a čitelných sekundárních replik, včetně geografických replik, je klasifikované do `SloSharedPool1` fondu zdrojů a `UserPrimaryGroup.DBId[N]` skupiny úloh, kde je `N` hodnota ID databáze. Kromě toho existuje více fondů zdrojů a skupin úloh pro různé interní úlohy.

Kromě použití Správce zdrojů k řízení prostředků v rámci procesu SQL používá Azure SQL Database také [objekty úloh](https://docs.microsoft.com/windows/win32/procthread/job-objects) Windows pro řízení prostředků na úrovni procesu a správce [souborového správce prostředků serveru](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) Windows pro správu kvót úložiště.

Zásady správného řízení prostředků Azure SQL Database jsou hierarchické. Od shora dolů se limity vynutily na úrovni operačního systému a na úrovni svazku úložiště pomocí mechanismů zásad správného řízení prostředků operačního systému a správce prostředků, pak na úrovni fondu zdrojů pomocí Správce zdrojů a pak na úrovni skupiny úloh pomocí Správce zdrojů. Omezení zásad správného řízení prostředků pro aktuální databázi nebo elastický fond jsou v zobrazení [Sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) .

### <a name="data-io-governance"></a>Řízení v/v data

Řízení v/v data je proces v Azure SQL Database slouží k omezení fyzické vstupně-výstupní operace čtení i zápisu proti datovým souborům databáze. Omezení IOPS se nastavují pro každou úroveň služby, aby se minimalizoval účinek "hlučného souseda", aby se zajistilo přidělení prostředků v rámci víceklientské služby a bylo možné zůstat v rámci schopností základního hardwaru a úložiště.

U izolovaných databází se limity skupin úloh použijí pro všechny vstupně-výstupní operace v databázi, zatímco limity fondu zdrojů se vztahují na všechny datové v/v služby úložiště ve stejném fondu SQL, včetně `tempdb` databáze. U elastických fondů se limity skupin úloh vztahují na každou databázi ve fondu, zatímco limit fondu zdrojů platí pro celý elastický fond, včetně `tempdb` databáze, která je sdílená mezi všemi databázemi ve fondu. Obecně platí, že omezení fondu zdrojů nemusí být pro úlohy dosažitelná v databázi (buď samostatně nebo ve fondu), protože limity skupin úloh jsou nižší než limity fondu zdrojů a omezují IOPS/propustnost dřív. Limity fondu ale můžou být dostupné v rámci kombinovaného zatížení s více databázemi ve stejném fondu.

Pokud dotaz například vygeneruje 1000 IOPS bez zásad správného řízení prostředků v/v, ale hodnota maximálního počtu IOPS pro skupinu úloh je nastavena na 900 IOPS, dotaz nebude moci generovat více než 900 IOPS. Pokud je ale limit počtu IOPS pro fond zdrojů nastavený na 1500 IOPS a celkový počet vstupně-výstupních operací ze všech skupin úloh přidružených ke fondu zdrojů překračuje 1500 IOPS, pak se v/v v případě stejného dotazu může snížit pod limit pracovní skupiny 900 IOPS.

Hodnoty IOPS a min/max propustnosti vracené zobrazením [Sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) slouží jako limity a velká písmena, nikoli jako záruky. Zásady správného řízení prostředků nezaručují žádnou konkrétní latenci úložiště. Nejlepší dosažitelná latence, IOPS a propustnost pro danou úlohu uživatele závisí nejen na omezeních zásad správného řízení prostředků v/v, ale také na kombinaci používaných velikostí v/v a na funkcích základního úložiště. SQL Database používá IOs, které mají různou velikost mezi 512 KB a 4 MB. Pro účely vynucení limitu IOPS jsou všechny vstupně-výstupní operace zaúčtovány bez ohledu na její velikost, s výjimkou databází s datovými soubory v Azure Storage. V takovém případě se IOs větším než 256 KB účtují jako několik 256-KB IOs, aby se mohla zarovnat Azure Storage v/v monitorování účtů.

Pro databáze Basic, Standard a Pro obecné účely, které používají datové soubory v Azure Storage, `primary_group_max_io` nemusí být tato hodnota dosažitelná, pokud databáze nemá k dispozici dostatek datových souborů k kumulativnímu poskytnutí tohoto počtu IOPS, nebo pokud se data nedistribuují rovnoměrně napříč soubory nebo pokud úroveň výkonu podkladových objektů BLOB omezuje za limit řízení prostředků vstupně-výstupní operace. Podobně s malým protokolem IOs generovaným častým zápisem transakce `primary_max_log_rate` nemusí být hodnota dosažitelná úlohou kvůli limitu IOPS v základním objektu BLOB služby Azure Storage.

Hodnoty využití prostředků, jako jsou `avg_data_io_percent` a `avg_log_write_percent` , hlášené v zobrazeních [Sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database),  [Sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)a [Sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) , se počítají jako procentuální hodnoty maximálního řízení prostředků. Proto pokud jiné faktory než řízení prostředků omezují počet vstupně-výstupních operací za sekundu, je možné zobrazit sloučení vstupně-výstupních operací za sekundu a latence při zvýšení zatížení, a to i v případě, že hlášené využití prostředků zůstává nižší než 100%.

Chcete-li zobrazit vstupně-výstupní operace čtení a zápisu, propustnost a latenci na databázový soubor, použijte funkci [Sys.dm_io_virtual_file_stats ()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) . Tato funkce obchází všechny vstupně-výstupní operace na databázi, včetně vstupně-výstupních operací na pozadí, které nepatří k `avg_data_io_percent` , ale používá IOPS a propustnost základního úložiště, a může mít vliv na zjištěnou latenci úložiště. Tato funkce také rozsvítí další latenci, která může být zavedena zásadou správy vstupně-výstupních prostředků pro čtení a zápisy ve `io_stall_queued_read_ms` sloupcích a v `io_stall_queued_write_ms` uvedeném pořadí.

### <a name="transaction-log-rate-governance"></a>Řízení sazeb transakčního protokolu

Řízení sazeb za transakční protokol je proces v Azure SQL Database, který slouží k omezení vysoké sazby příjmu pro úlohy, jako jsou hromadné vložení, výběr a sestavení indexů. Tato omezení jsou sledována a vyhodnocena na základě druhé úrovně na míru četnosti vytváření záznamů protokolu, což omezuje propustnost bez ohledu na to, kolik IOs může být pro datové soubory vystaveno.  Frekvence generování protokolu transakcí aktuálně škáluje lineárně až do bodu, který je závislý na hardwaru, přičemž maximální povolený počet protokolů je 96 MB/s s modelem nákupu vCore.

> [!NOTE]
> Skutečné fyzické soubory protokolu IOs na transakce se neřídí ani nepodporují.

Sazby za protokol jsou nastavené tak, aby je bylo možné dosáhnout a udržovat v nejrůznějších scénářích, zatímco celkový systém může udržovat svou funkčnost s minimalizovaným dopadem na uživatelské zatížení. Zásady správného řízení protokolu zajistí, že zálohy protokolu transakcí zůstávají v rámci publikované možnosti obnovování SLA.  Tento zásady správného řízení taky brání nadměrnému počtu nevyřízených položek na sekundárních replikách.

Po vygenerování záznamů protokolu se každá operace vyhodnotí a vyhodnotí, jestli by se měla zpozdit, aby se zachovala maximální požadovaná rychlost protokolu (MB/s za sekundu). Zpoždění se nepřidaly, když se záznamy protokolu vyprázdní do úložiště, místo toho se uplatní zásady správného řízení protokolu, a to během generování rychlost protokolování.

Skutečné sazby za generování protokolů, které jsou uvedené v době běhu, můžou být ovlivněné pomocí mechanismů zpětné vazby, ale dočasné snížení přípustných sazeb protokolu, aby systém mohl stabilizovat. Správa místa souboru protokolu, což nezpůsobuje nevyužité podmínky místa v protokolu a mechanismy replikace skupin dostupnosti dočasně snižují celkový počet systémových omezení.

Tvarování provozu správce míry přenosu dat se prochází prostřednictvím následujících typů čekání (zveřejněných v zobrazeních [Sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) a [Sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) ):

| Typ čekání | Poznámky |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Omezení databáze |
| POOL_LOG_RATE_GOVERNOR | Omezení fondu |
| INSTANCE_LOG_RATE_GOVERNOR | Omezení na úrovni instance |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Řízení zpětné vazby, fyzická replikace skupiny dostupnosti v Premium/Pro důležité obchodní informace Neudržovat |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Řízení zpětné vazby, omezování sazeb pro zamezení nedostatku místa v protokolu |
| HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO | Řízení zpětné vazby na geografickou replikaci, omezování míry protokolu, aby nedocházelo k vysoké latenci dat a nedostupnost geografických sekundárních souborů|
|||

Pokud se setkáte s limitem přenosové rychlosti, která brání požadovanou škálovatelnost, vezměte v úvahu následující možnosti:

- Škálujte až na vyšší úroveň služby, abyste získali maximální rychlost protokolu 96 MB/s, nebo můžete přejít na jinou úroveň služby. Úroveň služby pro [škálování](service-tier-hyperscale.md) na úrovni služeb poskytuje rychlost protokolu 100 MB/s, bez ohledu na zvolenou úroveň služby.
- Pokud jsou data načítána přechodná, například pracovní data v procesu ETL, je možné je načíst do databáze tempdb (která je popsána v minimálním protokolu).
- V případě analytických scénářů se načtěte do clusterované tabulky columnstore. Tím se sníží požadovaná rychlost protokolu z důvodu komprimace. Tato technika zvýší využití procesoru a vztahuje se pouze na datové sady, které využívají clusterované indexy columnstore.

### <a name="storage-space-governance"></a>Zásady správného řízení prostoru úložiště

V úrovních služeb Premium a Pro důležité obchodní informace jsou soubory dat a transakčního protokolu uloženy na místním svazku SSD počítače, který je hostitelem databáze nebo elastického fondu. To poskytuje vysoký počet vstupně-výstupních operací a propustnosti a nízkou vstupně-výstupní latenci. Velikost tohoto místního svazku závisí na hardwarových možnostech a je omezená. Na daném počítači se k místnímu svazku spotřebovávají v databázích zákazníka `tempdb` , včetně operačního systému, softwaru pro správu, dat monitorování, protokolů atd. Když se databáze vytváří, odstraňují a zvyšují nebo snižují jejich využití, místní prostor na počítači se v průběhu času pohybuje. 

Pokud systém zjistí, že dostupné volné místo na počítači je nízké a že databáze nebo elastický fond hrozí mít na paměti dostatek místa, přesune databázi nebo elastický fond na jiný počítač s dostatkem volného místa a umožní nárůst až do maximální velikosti nakonfigurovaných cílů služby. K tomuto přesunutí dochází v online režimu, podobně jako operace škálování databáze a má podobný [dopad](single-database-scale.md#impact), včetně krátkého převzetí služeb při selhání na konci operace. Toto převzetí služeb při selhání ukončí otevřené připojení a vrátí zpět transakce, které mohou ovlivnit aplikace s využitím databáze v daném čase.

Vzhledem k tomu, že jsou data fyzicky zkopírována do jiného počítače, může přesunutí větších databází vyžadovat značné množství času. Během této doby se zvyšuje riziko nedostatku místa v případě, že místní prostorová spotřeba na velkou uživatelskou databázi nebo elastický fond nebo `tempdb` databáze roste velmi rychle. Systém zahájí přesun databáze vyváženým způsobem, aby se předešlo chybám při nedostatku místa a zabránili se zbytečnému převzetí služeb při selhání.

## <a name="next-steps"></a>Další kroky

- Informace o obecných omezeních Azure najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- Informace o DTU a eDTU najdete v tématu [DTU a eDTU](purchasing-models.md#dtu-based-purchasing-model).
- Informace o omezení velikosti databáze tempdb najdete [v tématu databáze tempdb v Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
 