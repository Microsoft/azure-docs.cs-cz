---
title: Správa prostředků v hustých elastických fondech | Microsoft Docs
description: Spravujte výpočetní prostředky v elastických fondech Azure SQL s mnoha databázemi.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: carlrab
ms.date: 11/18/2019
ms.openlocfilehash: 4ce00743f6b77e6ac3e672e0ebce1e5eafc8235d
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74187186"
---
# <a name="resource-management-in-dense-elastic-pools"></a>Správa prostředků v hustých elastických fondech

Azure SQL Database [elastické fondy](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool) jsou cenově výhodné řešení pro správu mnoha databází s různými využitími prostředků. Všechny databáze v elastickém fondu sdílejí stejné přidělení prostředků, jako je procesor, paměť, pracovní vlákna, prostor úložiště, tempdb na předpokladu, že pouze podmnožina databází ve fondu bude v daném okamžiku používat výpočetní prostředky. Tento předpoklad umožňuje, aby elastické fondy byly nákladově efektivní. Místo placení všech prostředků, které by mohla každá databáze potenciálně potřebovat, můžou zákazníci platit mnohem menší sadu prostředků, která se sdílí mezi všemi databázemi ve fondu.

## <a name="resource-governance"></a>Zásady správného řízení prostředků

Sdílení prostředků vyžaduje, aby systém pečlivě prostudoval využití prostředků, aby se minimalizoval efekt "vysokého" souseda, kde databáze s vysokou spotřebou prostředků ovlivňuje další databáze ve stejném elastickém fondu. Systém zároveň musí poskytovat dostatek prostředků pro funkce, jako je vysoká dostupnost a zotavení po havárii (HADR), zálohování a obnovení, monitorování, úložiště dotazů a automatické ladění, aby fungovaly spolehlivě.

Azure SQL Database dosahuje těchto cílů pomocí více mechanismů zásad správného řízení prostředků, včetně [objektů úloh](https://docs.microsoft.com/windows/win32/procthread/job-objects) Windows pro řízení prostředků na úrovni procesu, systému Windows [File Server Správce prostředků (Správce prostředků)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) pro správu kvót úložiště a upravené a rozšířené verze SQL Server [Správce zdrojů](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) pro implementaci zásad správného řízení prostředků v rámci každé instance SQL Server spuštěné v Azure SQL Database.

Cílem přenesení návrhu elastických fondů je nákladově efektivní. Z tohoto důvodu systém úmyslně umožňuje zákazníkům vytvářet _husté_ fondy, což je fondy s počtem přístupů k databázím nebo s povoleným maximálním počtem, ale s mírným přidělením výpočetních prostředků. Ze stejného důvodu systém nerezervuje všechny potenciálně potřebné prostředky pro své interní procesy, ale umožňuje sdílení prostředků mezi interními procesy a úlohami uživatelů.

Tento přístup zákazníkům umožňuje využít husté elastické fondy k zajištění dostatečného výkonu a nákladů na velké náklady. Pokud je ale zatížení proti databázím v hustém fondu dostatečně náročné, může se spor na prostředky významně zvýšit. Kolizí prostředků snižuje výkon uživatelských úloh a může negativně ovlivnit interní procesy.

Pokud se spory prostředků vyskytují v hustě zkomprimovaném fondu, můžou si zákazníci vybrat jednu nebo více následujících akcí, které ji budou zmírnit:
- Vyladěním úlohy dotazů snížíte spotřebu prostředků.
- Zmenšení hustoty fondu přesunutím některých databází do jiného fondu nebo jejich samostatnými databázemi.
- Navýšení kapacity fondu za účelem získání dalších prostředků.

Návrhy, jak implementovat poslední dvě akce, najdete v části [provozní doporučení](#operational-recommendations) dále v tomto článku. Snížení kolizí prostředků přináší úlohy uživatelů i interní procesy a umožňuje systému spolehlivě udržovat očekávanou úroveň služby.

## <a name="monitoring-resource-consumption"></a>Monitorování spotřeby prostředků

Aby se zabránilo snížení výkonu z důvodu kolize prostředků, Zákazníci využívající husté elastické fondy by měli proaktivně monitorovat spotřebu prostředků a provádět včasné akce, pokud se začne zvyšovat dopad prostředků na úlohy. Průběžné monitorování je důležité, protože využití prostředků ve fondu se v průběhu času mění podle změn v uživatelských úlohách, změnách v datových svazcích a distribuci, změn v hustotě fondu a změnách v databázovém stroji SQL Server. 

Azure SQL Database poskytuje několik metrik, které jsou relevantní pro tento typ monitorování. Překročení doporučené průměrné hodnoty pro každou metriku označuje kolize prostředků ve fondu a mělo by se řešit pomocí jedné z výše zmíněných akcí.

|Název metriky|Popis|Doporučená průměrná hodnota| 
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|Využití CPU v procesu SQL Server přidruženého k elastickému fondu, jak je měřené v podkladovém operačním systému. K dispozici v zobrazení [Sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) v každé databázi a v zobrazení [Sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) v databázi `master`. Tato metrika se také generuje do Azure Monitor, kde je [pojmenována](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `sqlserver_process_core_percent`a lze ji zobrazit v Azure Portal. Tato hodnota je stejná pro každou databázi ve stejném elastickém fondu.|Pod 70%. Může být přijatelné příležitostné krátké špičky až do 90%.|
|`max_worker_percent`|Využití [pracovního vlákna]( https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide) . K dispozici pro každou databázi ve fondu a také pro samotný fond. Existují různá omezení počtu pracovních vláken na úrovni databáze a na úrovni fondu, proto je vhodné monitorovat tuto metriku na obou úrovních. K dispozici v zobrazení [Sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) v každé databázi a v zobrazení [Sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) v databázi `master`. Tato metrika se také generuje do Azure Monitor, kde je [pojmenována](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `workers_percent`a lze ji zobrazit v Azure Portal.|Pod 80%. Špičky až do 100% způsobí, že se pokusy o připojení a dotazy selžou.|
|`avg_data_io_percent`|Využití IOPS pro čtení a zápis fyzické v/v. K dispozici pro každou databázi ve fondu a také pro samotný fond. Existují různá omezení počtu IOPS na úrovni databáze a na úrovni fondu, proto je vhodné monitorovat tuto metriku na obou úrovních. K dispozici v zobrazení [Sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) v každé databázi a v zobrazení [Sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) v databázi `master`. Tato metrika se také generuje do Azure Monitor, kde je [pojmenována](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `physical_data_read_percent`a lze ji zobrazit v Azure Portal.|Pod 80%. Může být přijatelné příležitostné krátké špičky až do 100%.|
|`avg_log_write_percent`|Využití propustnosti pro zápis protokolu transakcí v/v. K dispozici pro každou databázi ve fondu a také pro samotný fond. Propustnost protokolu na úrovni databáze a na úrovni fondu jsou různá omezení, proto se doporučuje monitorování této metriky na obou úrovních. K dispozici v zobrazení [Sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) v každé databázi a v zobrazení [Sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) v databázi `master`. Tato metrika se také generuje do Azure Monitor, kde je [pojmenována](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `log_write_percent`a lze ji zobrazit v Azure Portal. Pokud je tato metrika blízko až 100%, všechny úpravy databáze (příkazy INSERT, UPDATE, DELETE, MERGE, SELECT... DO, BULK INSERT atd.) bude pomalejší.|Pod 90%. Může být přijatelné příležitostné krátké špičky až do 100%.|
|`oom_per_second`|Frekvence chyb v nedostatku paměti (OOM) v elastickém fondu, což je indikátor přetížení paměti. K dispozici v zobrazení [Sys. dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database?view=azuresqldb-current) . Podívejte se na [Příklady](#examples) pro výpočet této metriky pomocí ukázkového dotazu.|0|
|`avg_storage_percent`|Využití prostoru úložiště na úrovni elastického fondu. K dispozici v zobrazení [Sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) v databázi `master`. Tato metrika se také generuje do Azure Monitor, kde je [pojmenována](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `storage_percent`a lze ji zobrazit v Azure Portal.|Pod 80%. Může pro fondy bez nárůstu dat přistupovat 100%.|
|`tempdb_log_used_percent`|Využití místa protokolu transakcí v databázi `tempdb`. I když dočasné objekty vytvořené v jedné databázi nejsou viditelné v jiných databázích ve stejném elastickém fondu, `tempdb` je sdílený prostředek pro všechny databáze ve stejném fondu. Dlouhodobá nebo nečinná transakce v `tempdb` spuštěná z jedné databáze ve fondu může spotřebovávat velkou část transakčního protokolu a způsobit chyby dotazů v jiných databázích ve stejném fondu. K dispozici v zobrazení [Sys. dm_db_log_space_usage](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql) . Tato metrika se také generuje do Azure Monitor a je možné ji zobrazit v Azure Portal. Podívejte se na [Příklady](#examples) ukázkový dotaz, který vrátí aktuální hodnotu této metriky.|Pod 50%. Příležitostné špičky až do 80% jsou přijatelné.|
|||

Kromě těchto metrik Azure SQL Database poskytuje zobrazení, které vrací skutečná omezení zásad správného řízení prostředků, a zobrazení, která vrací statistiky využití prostředků na úrovni fondu zdrojů a na úrovni skupiny úloh.

|Název zobrazení|Popis|  
|-----------------|--------------------------------|  
|[sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|Vrátí vlastní nastavení konfigurace a kapacity používané mechanismy zásad správného řízení prostředků v aktuální databázi nebo elastickém fondu.|
|[sys.dm_resource_governor_resource_pools](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|Vrátí informace o aktuálním stavu fondu zdrojů, aktuální konfiguraci fondů zdrojů a statistikách kumulativních fondů prostředků.|
|[sys.dm_resource_governor_workload_groups](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|Vrátí souhrnné statistické údaje o skupině úloh a aktuální konfiguraci skupiny úloh. Toto zobrazení se dá připojit k sys. dm_resource_governor_resource_pools ve sloupci `pool_id`, aby se získaly informace o fondu zdrojů.|
|[sys. dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)|Vrátí statistiku využití fondu zdrojů za posledních 32 minut. Každý řádek představuje interval 20 sekund. Sloupce `delta_` v průběhu intervalu vrátí změnu v každé statistice.|
|[sys. dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)|Vrátí statistiku využití skupiny úloh za posledních 32 minut. Každý řádek představuje interval 20 sekund. Sloupce `delta_` v průběhu intervalu vrátí změnu v každé statistice.|
|||

Tato zobrazení je možné využít k monitorování využití prostředků a odstraňování sporů prostředků téměř v reálném čase. Uživatelské zatížení primárních a čitelných sekundárních replik, včetně geografických replik, je klasifikované do fondu prostředků `SloSharedPool1` a `UserPrimaryGroup.DBId[N]` skupiny úloh, kde `N` představuje hodnotu ID databáze.

Kromě monitorování aktuálního využití prostředků můžou zákazníci, kteří používají husté fondy, uchovávat historická data o využití prostředků v samostatném úložišti dat. Tato data je možné využít při prediktivní analýze k proaktivní správě využití prostředků na základě historických a sezónních trendů.

## <a name="operational-recommendations"></a>Provozní doporučení

**Nechte dostatečný prostor pro prostředky**. Pokud dojde k kolize prostředků a snížení výkonu, může to znamenat přesunutí některých databází z ovlivněného elastického fondu nebo navýšení kapacity fondu, jak bylo popsáno dříve. Tyto akce však vyžadují dokončení dalších výpočetních prostředků. Konkrétně pro fondy Premium a Pro důležité obchodní informace vyžadují tyto akce přenos všech dat pro přesunuté databáze, nebo pro všechny databáze v elastickém fondu, pokud je fond škálovatelný. Přenos dat je dlouhodobá operace náročná na provoz a prostředky. Pokud je už fond pod vysokým tlakem, neomezená operace bude mít ještě víc výkon. V extrémních případech nemusí být možné vyřešit kolize prostředků přes přesun databáze nebo škálování fondu, protože požadované prostředky nejsou k dispozici. V takovém případě může být jediným řešením dočasné zmenšování úlohy dotazů v ovlivněném elastickém fondu.

Zákazníci, kteří používají husté fondy, by měli pečlivě sledovat trendy využití prostředků, jak je popsáno výše, a vzít v úvahu zmírňující opatření, zatímco metriky zůstanou v doporučených oblastech a v elastickém fondu jsou stále dostatečné prostředky.

Využití prostředků závisí na několika faktorech, které se v průběhu času mění pro každou databázi a každý elastický fond. Dosažení optimálního poměru cen a výkonu v hustých fondech vyžaduje nepřetržité monitorování a nové vyrovnávání, které přesouvá databáze z více využívaných fondů do méně využitých fondů a vytváří nové fondy podle potřeby pro zvýšení zatížení.

**Nepřesouvat "horké" databáze**. Pokud se spory prostředků na úrovni fondu primárně způsobují malým počtem vysoce využitých databází, může se stát, že tyto databáze přesunete do méně využitého fondu, nebo je vytvoříte jako samostatné databáze. Nicméně to se nedoporučuje, zatímco databáze zůstane vysoce využívána, protože operace přesunu bude dále snižovat výkon, jak probíhá přesouvání databáze, tak i pro celý fond. Místo toho buď počkejte, než se dokončí vysoké využití, nebo přesuňte méně využité databáze místo toho, aby se na úrovni fondu využilo zatížení prostředků. Přesun databází s velmi nízkým využitím ale v tomto případě neposkytuje žádnou výhodu, protože nevýznamně snižuje využití prostředků na úrovni fondu.

**Vytvořte nové databáze ve fondu "karanténa"** . Ve scénářích, kdy se vytvářejí nové databáze často, například aplikací využívajících model klienta pro databáze, je riziko, že nová databáze umístěná do existujícího elastického fondu neočekávaně spotřebuje významné prostředky a ovlivní jiné databáze. a interní procesy ve fondu. Chcete-li toto riziko zmírnit, vytvořte samostatný fond "karanténa" s dostačujícím přidělením prostředků. Tento fond použijte pro nové databáze, které mají ještě neznámé vzory využití prostředků. Jakmile se databáze nechali v tomto fondu pro obchodní cyklus, jako je například týden nebo měsíc, a spotřeba prostředků je známá, lze ji přesunout do fondu s dostatečnou kapacitou, aby bylo možné tyto dodatečné využití prostředků přizpůsobit.

**Vyhněte se nepříliš hustým logickým serverům**. Azure SQL Database [podporuje](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server) až 5000 databází na logický Server. Zákazníci, kteří používají elastické fondy s tisíci databází, můžou zvážit umístění více elastických fondů na jeden server s celkovým počtem databází až do podporovaného limitu. Logické servery s mnoha tisíci databází ale vytvářejí provozní problémy. Operace, které vyžadují výčet všech databází na serveru, například zobrazení databází na portálu, budou pomalejší. Provozní chyby, jako je například nesprávná změna přihlášení na úrovni serveru nebo pravidla brány firewall, budou mít vliv na větší počet databází. Náhodné odstranění logického serveru bude vyžadovat pomoc od podpora Microsoftu k obnovení databází na odstraněném serveru a způsobí dlouhotrvající výpadky všech ovlivněných databází.

Doporučujeme omezit počet databází na logický Server na nižší číslo, než je podporované maximum. V mnoha scénářích je optimální použití až 1000-2000 databází na server. Aby se snížila pravděpodobnost nechtěného odstranění serveru, doporučujeme umístit zámek proti [odstranění](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) na logický Server nebo na jeho skupinu prostředků.

V minulosti byly některé scénáře, které zahrnují přesun databází v, nebo mezi elastickými fondy na stejném logickém serveru rychlejší než při přesunu databází mezi logickými servery. V současné době se všechny přesunuté databáze spustí se stejnou rychlostí bez ohledu na zdrojový a cílový logický Server.

## <a name="examples"></a>Příklady

### <a name="monitoring-memory-utilization"></a>Monitorování využití paměti

Tento dotaz vypočítá metriku `oom_per_second` pro každý fond zdrojů za posledních 32 minut. Tento dotaz se dá provést v jakékoli databázi v elastickém fondu.

```
SELECT pool_id,
       name AS resource_pool_name,
       IIF(name LIKE 'SloSharedPool%' OR name LIKE 'UserPool%', 'user', 'system') AS resource_pool_type,
       SUM(CAST(delta_out_of_memory_count AS decimal))/(SUM(duration_ms)/1000.) AS oom_per_second
FROM sys.dm_resource_governor_resource_pools_history_ex
GROUP BY pool_id, name
ORDER BY pool_id;
```
### <a name="monitoring-tempdb-log-space-utilization"></a>Monitorování využití prostoru protokolu `tempdb`

Tento dotaz vrátí aktuální hodnotu metriky `tempdb_log_used_percent`. Tento dotaz se dá provést v jakékoli databázi v elastickém fondu.

```
SELECT used_log_space_in_percent AS tempdb_log_used_percent
FROM tempdb.sys.dm_db_log_space_usage;
```

## <a name="next-steps"></a>Další kroky

* Úvod do elastických fondů najdete v tématu [elastické fondy vám pomůžou se správou a škálováním více databází Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool).
* Informace o ladění úloh dotazů za účelem snížení využití prostředků najdete v tématu [monitorování a optimalizace]( https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-tuning-index)a [ladění a optimalizace výkonu](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview).
