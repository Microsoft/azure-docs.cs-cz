---
title: Správa zdrojů v hustých elastických fondech | Dokumenty společnosti Microsoft
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
ms.date: 03/13/2019
ms.openlocfilehash: 014f9edca1706c39930c6e48bb64cd8873bcace9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473723"
---
# <a name="resource-management-in-dense-elastic-pools"></a>Správa prostředků v hustých elastických fondech

[Elastické fondy](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool) Azure SQL Database jsou nákladově efektivní řešení pro správu mnoha databází s různým využitím prostředků. Všechny databáze v elastickém fondu sdílejí stejné přidělení prostředků, jako je například procesor, paměť, pracovní podprocesy, úložný prostor, tempdb, za předpokladu, že **pouze podmnožina databází ve fondu bude používat výpočetní prostředky v daném okamžiku**. Tento předpoklad umožňuje elastické fondy nákladově efektivní. Namísto placení za všechny prostředky, které by mohla každá jednotlivá databáze potenciálně potřebovat, platí zákazníci za mnohem menší sadu prostředků sdílenou mezi všemi databázemi ve fondu.

## <a name="resource-governance"></a>Zásady správného řízení prostředků

Sdílení prostředků vyžaduje, aby systém pečlivě řídil využití prostředků, aby minimalizoval efekt "hlučný soused", kde databáze s vysokou spotřebou prostředků ovlivňuje jiné databáze ve stejném elastickém fondu. Současně musí systém poskytovat dostatečné prostředky pro funkce, jako je vysoká dostupnost a zotavení po havárii (HADR), zálohování a obnovení, monitorování, úložiště dotazů, automatické ladění atd., aby fungovaly spolehlivě.

Azure SQL Database dosahuje těchto cílů pomocí více mechanismů zásad správného řízení prostředků, včetně [objektů úloh](https://docs.microsoft.com/windows/win32/procthread/job-objects) y Windows pro řízení prostředků na úrovni procesu, Správce prostředků souborového serveru Windows [(FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) pro správu kvót úložiště a upravenou a rozšířenou verzi [správce prostředků](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) SQL Server k implementaci zásad správného řízení prostředků v rámci každé instance SQL Serveru spuštěné v azure sql database.

Primárním cílem návrhu elastických fondů je nákladově efektivní. Z tohoto důvodu systém záměrně umožňuje zákazníkům vytvářet _husté_ fondy, to je fondy s počtem databází blíží nebo na maximální povoleno, ale s mírným přidělení výpočetních prostředků. Ze stejného důvodu systém nerezervuje všechny potenciálně potřebné prostředky pro své interní procesy, ale umožňuje sdílení prostředků mezi interními procesy a uživatelskými úlohami.

Tento přístup umožňuje zákazníkům používat husté elastické fondy k dosažení odpovídajícího výkonu a velkých úspor nákladů. Pokud je však zatížení proti mnoha databázím v hustém fondu dostatečně intenzivní, bude významné tvrzení o prostředku. Konflikty prostředků snižuje výkon zatížení uživatele a může negativně ovlivnit interní procesy.

> [!IMPORTANT]
> V husté fondy s mnoha aktivních databází nemusí být možné zvýšit počet databází ve fondu až na maxima zdokumentované pro [dtu](sql-database-dtu-resource-limits-elastic-pools.md) a [virtuální jádra](sql-database-vcore-resource-limits-elastic-pools.md) elastické fondy.
> 
> Počet databází, které lze umístit do hustých fondů, aniž by došlo k konfliktům prostředků a problémům s výkonem, závisí na počtu souběžně aktivních databází a na spotřebě prostředků podle zatížení uživatelů v každé databázi. Toto číslo se může v průběhu času měnit s tím, jak se mění zatížení uživatelů.

Dojde-li k tvrzení o prostředku v hustě zabaleném fondu, zákazníci si mohou vybrat jednu nebo více z následujících akcí, které ji zmírní:
- Vylaďte úlohu dotazu, abyste snížili spotřebu prostředků, nebo rozšiřte spotřebu prostředků do více databází v průběhu času.
- Snižte hustotu fondu přesunutím některých databází do jiného fondu nebo jejich vytvořením samostatných databází.
- Vertikálně navýšit kapacitu fondu, abyste získali více prostředků.

Návrhy, jak implementovat poslední dvě akce, naleznete v [části Provozní doporučení](#operational-recommendations) dále v tomto článku. Snížení tvrzení o prostředcích přináší výhody zatížení uživatelů i interní procesy a umožňuje systému spolehlivě udržovat očekávanou úroveň služeb.

## <a name="monitoring-resource-consumption"></a>Sledování spotřeby zdrojů

Aby se zabránilo snížení výkonu z důvodu tvrzení o prostředku, zákazníci, kteří používají husté elastické fondy, by měli proaktivně sledovat spotřebu prostředků a provést včasnou akci, pokud rostoucí konflikty prostředků začnou ovlivňovat úlohy. Průběžné monitorování je důležité, protože využití prostředků ve fondu se v průběhu času mění kvůli změnám v zatížení uživatelů, změnám v objemech dat a distribuci, změnám hustoty fondu a změnám ve službě Azure SQL Database. 

Azure SQL Database poskytuje několik metrik, které jsou relevantní pro tento typ monitorování. Překročení doporučené průměrné hodnoty pro každou metriku označuje konflikty prostředků ve fondu a je třeba je řešit pomocí jedné z výše uvedených akcí.

|Název metriky|Popis|Doporučená průměrná hodnota| 
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|Využití procesoru procesu SQL Server spojené s elastického fondu, měřeno základní operační systém. K dispozici v zobrazení [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) v každé databázi a v `master` zobrazení [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) v databázi. Tato metrika se také vydává do Azure Monitoru, kde je [pojmenována](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `sqlserver_process_core_percent`a dá se zobrazit na webu Azure Portal. Tato hodnota je stejná pro každou databázi ve stejném elastickém fondu.|Pod 70%. Příležitostné krátké hroty až do 90% mohou být přijatelné.|
|`max_worker_percent`|Využití [pracovního vlákna.]( https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide) Za předpokladu, že pro každou databázi ve fondu, stejně jako pro samotný fond. Existují různá omezení počtu pracovních podprocesů na úrovni databáze a na úrovni fondu, proto se doporučuje sledování této metriky na obou úrovních. K dispozici v zobrazení [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) v každé databázi a v `master` zobrazení [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) v databázi. Tato metrika se také vydává do Azure Monitoru, kde je [pojmenována](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `workers_percent`a dá se zobrazit na webu Azure Portal.|Pod 80%. Špičky až 100 % způsobí selhání pokusů o připojení a dotazů.|
|`avg_data_io_percent`|Využití IOPS pro čtení a zápis fyzické vi. Za předpokladu, že pro každou databázi ve fondu, stejně jako pro samotný fond. Existují různá omezení počtu vstupně-upů na úrovni databáze a na úrovni fondu, proto se doporučuje sledování této metriky na obou úrovních. K dispozici v zobrazení [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) v každé databázi a v `master` zobrazení [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) v databázi. Tato metrika se také vydává do Azure Monitoru, kde je [pojmenována](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `physical_data_read_percent`a dá se zobrazit na webu Azure Portal.|Pod 80%. Příležitostné krátké hroty až do 100% mohou být přijatelné.|
|`avg_log_write_percent`|Využití propustnost pro vodicí protokol transakcí zápis VO. Za předpokladu, že pro každou databázi ve fondu, stejně jako pro samotný fond. Existují různá omezení propustnost protokolu na úrovni databáze a na úrovni fondu, proto se doporučuje sledování této metriky na obou úrovních. K dispozici v zobrazení [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) v každé databázi a v `master` zobrazení [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) v databázi. Tato metrika se také vydává do Azure Monitoru, kde je [pojmenována](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `log_write_percent`a dá se zobrazit na webu Azure Portal. Když se tato metrika blíží 100%, všechny změny databáze (INSERT, UPDATE, DELETE, MERGE příkazy, SELECT ... DO, Hromadné vložky, atd.) bude pomalejší.|Pod 90%. Příležitostné krátké hroty až do 100% mohou být přijatelné.|
|`oom_per_second`|Rychlost chyb y nedostatku paměti (OOM) v elastickém fondu, což je indikátor tlaku paměti. K dispozici v zobrazení [sys.dm_resource_governor_resource_pools_history_ex.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database?view=azuresqldb-current) Viz Příklady ukázkového dotazu pro výpočet této [metriky.](#examples)|0|
|`avg_storage_percent`|Využití úložného prostoru na úrovni elastického fondu. K dispozici v zobrazení [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) v databázi. `master` Tato metrika se také vydává do Azure Monitoru, kde je [pojmenována](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `storage_percent`a dá se zobrazit na webu Azure Portal.|Pod 80%. Může přistupovat 100 % pro fondy bez růstu dat.|
|`tempdb_log_used_percent`|Využití místa transakčního `tempdb` protokolu v databázi. Přestože dočasné objekty vytvořené v jedné databázi nejsou viditelné `tempdb` v jiných databázích ve stejném elastickém fondu, je sdílený prostředek pro všechny databáze ve stejném fondu. Dlouho běžící nebo nečinná transakce spuštěná `tempdb` z jedné databáze ve fondu může spotřebovat velkou část transakčního protokolu a způsobit selhání dotazů v jiných databázích ve stejném fondu. K dispozici v zobrazení [sys.dm_db_log_space_usage.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql) Tato metrika se taky vydává do Azure Monitoru a dá se zobrazit na webu Azure Portal. Viz [Příklady](#examples) ukázkového dotazu, který vrátí aktuální hodnotu této metriky.|Pod 50%. Příležitostné hroty až 80% jsou přijatelné.|
|||

Kromě těchto metrik Azure SQL Database poskytuje zobrazení, které vrací omezení zásad správného řízení skutečných prostředků, stejně jako další zobrazení, která vracejí statistiky využití prostředků na úrovni fondu prostředků a na úrovni skupiny úloh.

|Zobrazit název|Popis|  
|-----------------|--------------------------------|  
|[sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|Vrátí skutečné nastavení konfigurace a kapacity používané mechanismy zásad správného řízení prostředků v aktuální databázi nebo elastickém fondu.|
|[sys.dm_resource_governor_resource_pools](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|Vrátí informace o aktuálním stavu fondu zdrojů, aktuální konfiguraci fondů zdrojů a kumulativní chvějích statistik fondu zdrojů.|
|[sys.dm_resource_governor_workload_groups](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|Vrátí kumulativní statistiku skupiny pracovních vytížek a aktuální konfiguraci skupiny úloh. Toto zobrazení lze spojit se sys.dm_resource_governor_resource_pools ve sloupci `pool_id` získat informace fondu zdrojů.|
|[sys.dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)|Vrátí statistiku využití fondu zdrojů za posledních 32 minut. Každý řádek představuje interval 20 sekund. Sloupce `delta_` vrátí změnu v každé statistice během intervalu.|
|[sys.dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)|Vrátí statistiku využití skupiny pracovních vytížek za posledních 32 minut. Každý řádek představuje interval 20 sekund. Sloupce `delta_` vrátí změnu v každé statistice během intervalu.|
|||

Tato zobrazení lze použít ke sledování využití prostředků a řešení potíží s tvrzeními o prostředcích téměř v reálném čase. Zatížení uživatelů na primární a čitelné sekundární repliky, včetně geografické `SloSharedPool1` repliky, je klasifikován do fondu prostředků a `UserPrimaryGroup.DBId[N]` skupiny úloh, kde `N` stojí hodnotu ID databáze.

Kromě sledování aktuálního využití prostředků mohou zákazníci, kteří používají husté fondy, udržovat historická data o využití prostředků v samostatném úložišti dat. Tato data lze použít v prediktivní analýze pro aktivní správu využití zdrojů na základě historických a sezónních trendů.

## <a name="operational-recommendations"></a>Provozní doporučení

**Ponechte dostatečný prostor pro zdroje**. Pokud dojde k konfliktu prostředků a snížení výkonu, zmírnění může zahrnovat přesunutí některé databáze z ovlivněného elastického fondu nebo škálování fondu, jak je uvedeno dříve. Tyto akce však vyžadují další výpočetní prostředky k dokončení. Zejména pro fondy Premium a Důležité pro podnikání tyto akce vyžadují přenos všech dat pro přesunuté databáze nebo pro všechny databáze v elastickém fondu, pokud je fond navýšen. Přenos dat je dlouhotrvající operace náročná na prostředky. Pokud je fond již pod vysokým tlakem prostředků, samotná operace zmírnění sníží výkon ještě více. V extrémních případech nemusí být možné vyřešit konflikty prostředků prostřednictvím přesunutí databáze nebo navýšení fondu, protože požadované prostředky nejsou k dispozici. V tomto případě dočasně snížení zatížení dotazu na ovlivněné elastické fondu může být jediným řešením.

Zákazníci, kteří používají husté fondy, by měli pečlivě sledovat trendy využití prostředků, jak je popsáno výše, a přijmout zmírňující akce, zatímco metriky zůstávají v doporučených rozsahech a v elastickém fondu jsou stále dostatečné prostředky.

Využití prostředků závisí na více faktorech, které se v průběhu času mění pro každou databázi a každý elastický fond. Dosažení optimálního poměru cena/výkon v hustých fondech vyžaduje průběžné monitorování a vyvažování, které přesouvá databáze z více využívaných fondů do méně využívaných fondů a vytváří nové fondy podle potřeby pro zvýšení zatížení.

**Nepohybujte "horkými" databázemi**. Pokud konflikty prostředků na úrovni fondu je primárně způsobena malý počet vysoce využité databáze, může být lákavé přesunout tyto databáze do méně využitého fondu nebo je samostatné databáze. Však to, zatímco databáze zůstává vysoce využita se nedoporučuje, protože operace přesunutí bude dále snížit výkon, a to jak pro databázi přesunuta a pro celý fond. Místo toho počkejte, dokud vysoké využití odezní, nebo přesuňte méně využívaných databází, abyste zmírnili tlak prostředků na úrovni fondu. Přesunutí databází s velmi nízkým využitím však v tomto případě neposkytuje žádné výhody, protože podstatně nesnižuje využití prostředků na úrovni fondu.

**Vytvořte nové databáze ve fondu "karantény"**. Ve scénářích, kde jsou často vytvářeny nové databáze, jako jsou například aplikace používající model klienta na databázi, existuje riziko, že nová databáze umístěná do existujícího elastického fondu neočekávaně spotřebuje významné prostředky a ovlivní jiné databáze a interních procesů v bazénu. Chcete-li toto riziko zmírnit, vytvořte samostatný fond "karantény" s dostatečným přidělením prostředků. Tento fond použijte pro nové databáze s dosud neznámými vzory spotřeby prostředků. Jakmile databáze zůstane v tomto fondu pro obchodní cyklus, například týden nebo měsíc, a její spotřeba zdrojů je známa, lze ji přesunout do fondu s dostatečnou kapacitou pro použití tohoto dalšího zdroje.

**Vyhněte se příliš hustým logickým serverům**. Azure SQL Database [podporuje](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server) až 5000 databází na logický server. Zákazníci, kteří používají elastické fondy s tisíci databázemi, mohou zvážit umístění více elastických fondů na jeden server s celkovým počtem databází až do podporovaného limitu. Logické servery s mnoha tisíci databázemi však vytvářejí provozní výzvy. Operace, které vyžadují výčet všech databází na serveru, například zobrazení databází na portálu, budou pomalejší. Provozní chyby, jako je například nesprávná změna přihlášení na úrovni serveru nebo pravidla brány firewall, ovlivní větší počet databází. Náhodné odstranění logického serveru bude vyžadovat pomoc od podpory společnosti Microsoft k obnovení databází na odstraněném serveru a způsobí dlouhodobý výpadek pro všechny ohrožené databáze.

Doporučujeme omezit počet databází na logický server na nižší číslo, než je maximální podporované. V mnoha scénářích je optimální použití až 1000-2000 databází na server. Chcete-li snížit pravděpodobnost náhodného odstranění serveru, doporučujeme umístit [zámek odstranění](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) na logický server nebo jeho skupinu prostředků.

V minulosti byly některé scénáře zahrnující přesun databází dovnitř, ven nebo mezi elastickými fondy na stejném logickém serveru rychlejší než při přesouvání databází mezi logickými servery. V současné době všechny přesuny databáze se provádějí stejnou rychlostí bez ohledu na zdrojový a cílový logický server.

## <a name="examples"></a>Příklady

### <a name="monitoring-memory-utilization"></a>Sledování využití paměti

Tento dotaz vypočítá `oom_per_second` metriku pro každý fond zdrojů za posledních 32 minut. Tento dotaz lze spustit v libovolné databázi v elastickém fondu.

```
SELECT pool_id,
       name AS resource_pool_name,
       IIF(name LIKE 'SloSharedPool%' OR name LIKE 'UserPool%', 'user', 'system') AS resource_pool_type,
       SUM(CAST(delta_out_of_memory_count AS decimal))/(SUM(duration_ms)/1000.) AS oom_per_second
FROM sys.dm_resource_governor_resource_pools_history_ex
GROUP BY pool_id, name
ORDER BY pool_id;
```
### <a name="monitoring-tempdb-log-space-utilization"></a>Monitorování `tempdb` využití prostoru protokolu

Tento dotaz vrátí aktuální `tempdb_log_used_percent` hodnotu metriky. Tento dotaz lze spustit v libovolné databázi v elastickém fondu.

```
SELECT used_log_space_in_percent AS tempdb_log_used_percent
FROM tempdb.sys.dm_db_log_space_usage;
```

## <a name="next-steps"></a>Další kroky

* Úvod do elastických fondů najdete v [tématu Elastické fondy vám pomůžou spravovat a škálovat více databází Azure SQL](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool).
* Informace o optimalizaci úloh dotazů za účelem snížení využití prostředků naleznete v [tématu Monitorování a optimalizace]( https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-tuning-index)a [Monitorování a optimalizace výkonu](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview).
