---
title: Monitorování nasazení SQL pomocí SQL Insights (Preview)
description: Přehled SQL Insights v Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: d01f80a803c5b0f9da067dd23ab8cdb4cc591a79
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609388"
---
# <a name="monitor-your-sql-deployments-with-sql-insights-preview"></a>Monitorování nasazení SQL pomocí SQL Insights (Preview)
SQL Insights monitoruje výkon a stav nasazení SQL.  Může přispět k předvídatelnému výkonu a dostupnosti důležitých úloh, které jste vytvořili kolem back-endu SQL, a to tak, že identifikují problémová místa a problémy s výkonem. SQL Insights ukládá data do [protokolů Azure monitor](../logs/data-platform-logs.md), což umožňuje poskytovat výkonnou agregaci a filtrování a analyzovat trendy dat v čase. Tato data můžete zobrazit z Azure Monitor v zobrazeních, která jako součást této nabídky dodáváme, a můžete se podívat přímo na data protokolu, abyste mohli spouštět dotazy a analyzovat trendy.

SQL Insights neinstaluje cokoli do nasazení SQL IaaS. Místo toho využívá vyhrazené monitorování virtuálních počítačů ke vzdálenému shromažďování dat pro nasazení SQL PaaS i SQL IaaS.  Profil monitorování SQL Insights umožňuje spravovat sady dat, které se mají shromažďovat, podle typu SQL, včetně Azure SQL DB, spravované instance Azure SQL a SQL serveru běžícího na virtuálním počítači Azure.

## <a name="pricing"></a>Ceny

Pro SQL Insights se neúčtují žádné přímé náklady, ale v pracovním prostoru Log Analytics se vám účtuje jeho aktivita. Na základě cen, které jsou publikovány na [stránce s cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/), se SQL Insights účtuje takto:

- Data ingestovaná z agentů a uložená v pracovním prostoru.
- Pravidla výstrah na základě dat protokolu
- Oznámení se odesílají z pravidel výstrah.

Velikost protokolu se liší podle délky řetězců shromažďovaných dat a může se zvýšit s množstvím aktivity databáze. 

## <a name="supported-versions"></a>Podporované verze 
SQL Insights podporuje následující verze SQL Server:

- SQL Server 2012 a novější

SQL Insights podporuje SQL Server spuštěné v následujících prostředích:

- Azure SQL Database
- Spravovaná instance Azure SQL
- Virtuální počítače Azure SQL
- Virtuální počítače Azure

SQL Insights nepodporuje ani neomezenou podporu pro následující:

- SQL Server běžící na virtuálních počítačích mimo Azure se v tuto chvíli nepodporuje.
- Azure SQL Database elastické fondy: omezená podpora během Public Preview. Bude plně podporován při obecné dostupnosti.  
- Nasazení bez serveru SQL bez serveru: jako aktivní geografické prostředí DR, současná agenti monitorování zabrání nasazení bez serveru v přechodu do režimu spánku. To může způsobit vyšší než očekávané náklady z nasazení bez serveru.  
- Čitelné sekundární: v současné době se podporují jenom typy nasazení s jedním čitelným sekundárním koncovým bodem (Pro důležité obchodní informace nebo s měřítkem). Pokud nasazení v rámci škálování podporuje pojmenované repliky, budeme schopni podpořit pro jednu logickou databázi více čitelných sekundárních koncových bodů.  
- Azure Active Directory: v současné době podporujeme jenom přihlášení SQL pro agenta monitorování. Plánujeme podporovat Azure Active Directory v nadcházející verzi a bez aktuální podpory pro ověřování virtuálních počítačů SQL pomocí aktivních adresářů na Bespoke řadiči domény.  


## <a name="open-sql-insights"></a>Otevřete SQL Insights.
Otevřete SQL Insights výběrem **SQL (Preview)** v části **přehled** v nabídce **Azure monitor** v Azure Portal. Kliknutím na dlaždici načtěte prostředí pro typ SQL, který sledujete.

:::image type="content" source="media/sql-insights/portal.png" alt-text="SQL Insights v Azure Portal.":::


## <a name="enable-sql-insights"></a>Povolit SQL Insights 
Podrobný postup, jak povolit SQL Insights, najdete v tématu [Povolení SQL Insights](sql-insights-enable.md) .


## <a name="data-collected-by-sql-insights"></a>Data shromážděná službou SQL Insights
Ve verzi Public Preview podporuje SQL Insights jenom vzdálenou metodu monitorování. Agent telegraf není nainstalován v SQL Server. Používá modul plug-in SQL Server Input pro telegraf a používá tři skupiny dotazů pro různé typy monitorování SQL serveru: Azure SQL DB, Azure SQL Managed instance, SQL Server běžící na virtuálním počítači Azure. 

Následující tabulky shrnují následující:

- Název dotazu v modulu plug-in SQLServer telegraf
- Dynamická spravovaná zobrazení – volání dotazu
- Obor názvů – data se zobrazí pod tabulkou *InsighstMetrics* .
- Zda jsou data shromažďována ve výchozím nastavení
- Jak často se data shromažďují ve výchozím nastavení
 
Můžete upravit, které dotazy jsou spouštěny, a četnost shromažďování dat při vytváření profilu monitorování. 

### <a name="azure-sql-db-data"></a>Data služby Azure SQL DB 

| Název dotazu | Zobrazení dynamické správy | Obor názvů | Ve výchozím nastavení povoleno | Výchozí frekvence shromažďování |
|:---|:---|:---|:---|:---|
| AzureSQLDBWaitStats |  sys.dm_db_wait_stats | sqlserver_azuredb_waitstats | No | NA |
| AzureSQLDBResourceStats | sys.dm_db_resource_stats | sqlserver_azure_db_resource_stats | Yes | 60 sekund |
| AzureSQLDBResourceGovernance | sys.dm_user_db_resource_governance | sqlserver_db_resource_governance | Yes | 60 sekund |
| AzureSQLDBDatabaseIO | sys.dm_io_virtual_file_stats<br>sys.database_files<br>tempdb.sys .database_files | sqlserver_database_io | Yes | 60 sekund |
| AzureSQLDBServerProperties | sys.dm_os_job_object<br>sys.database_files<br>tabulce. databáze<br>tabulce. [database_service_objectives] | sqlserver_server_properties | Yes | 60 sekund |
| AzureSQLDBOsWaitstats | sys.dm_os_wait_stats | sqlserver_waitstats | Yes | 60 sekund |
| AzureSQLDBMemoryClerks | sys.dm_os_memory_clerks | sqlserver_memory_clerks | Yes | 60 sekund |
| AzureSQLDBPerformanceCounters | sys.dm_os_performance_counters<br>sys.databases | sqlserver_performance | Yes | 60 sekund |
| AzureSQLDBRequests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | sqlserver_requests | No | NA |
| AzureSQLDBSchedulers | sys.dm_os_schedulers | sqlserver_schedulers | No | NA  |

### <a name="azure-sql-managed-instance-data"></a>Data spravované instance Azure SQL 

| Název dotazu | Zobrazení dynamické správy | Obor názvů | Ve výchozím nastavení povoleno | Výchozí frekvence shromažďování |
|:---|:---|:---|:---|:---|
| AzureSQLMIResourceStats | sys.server_resource_stats | sqlserver_azure_db_resource_stats | Yes | 60 sekund |
| AzureSQLMIResourceGovernance | sys.dm_instance_resource_governance | sqlserver_instance_resource_governance | Yes | 60 sekund |
| AzureSQLMIDatabaseIO | sys.dm_io_virtual_file_stats<br>sys.master_files | sqlserver_database_io | Yes | 60 sekund |
| AzureSQLMIServerProperties | sys.server_resource_stats | sqlserver_server_properties | Yes | 60 sekund |
| AzureSQLMIOsWaitstats | sys.dm_os_wait_stats | sqlserver_waitstats | Yes | 60 sekund |
| AzureSQLMIMemoryClerks | sys.dm_os_memory_clerks | sqlserver_memory_clerks | Yes | 60 sekund |
| AzureSQLMIPerformanceCounters | sys.dm_os_performance_counters<br>sys.databases | sqlserver_performance | Yes | 60 sekund |
| AzureSQLMIRequests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | sqlserver_requests | No | NA |
| AzureSQLMISchedulers | sys.dm_os_schedulers | sqlserver_schedulers | No | NA |

### <a name="sql-server-data"></a>Data SQL Server

| Název dotazu | Zobrazení dynamické správy | Obor názvů | Ve výchozím nastavení povoleno | Výchozí frekvence shromažďování |
|:---|:---|:---|:---|:---|
| SQLServerPerformanceCounters | sys.dm_os_performance_counters | sqlserver_performance | Yes | 60 sekund |
| SQLServerWaitStatsCategorized | sys.dm_os_wait_stats | sqlserver_waitstats | Yes | 60 sekund | 
| SQLServerDatabaseIO | sys.dm_io_virtual_file_stats<br>sys.master_files | sqlserver_database_io | Yes | 60 sekund |
| SQLServerProperties | sys.dm_os_sys_info | sqlserver_server_properties | Yes | 60 sekund |
| SQLServerMemoryClerks | sys.dm_os_memory_clerks | sqlserver_memory_clerks | Yes | 60 sekund |
| SQLServerSchedulers | sys.dm_os_schedulers | sqlserver_schedulers | No | NA |
| SQLServerRequests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | sqlserver_requests | No | NA |
| SQLServerVolumeSpace | sys.master_files | sqlserver_volume_space | Yes | 60 sekund |
| SQLServerCpu | sys.dm_os_ring_buffers | sqlserver_cpu | Yes | 60 sekund |
| SQLServerAvailabilityReplicaStates | sys.dm_hadr_availability_replica_states<br>sys.availability_replicas<br>sys.availability_groups<br>sys.dm_hadr_availability_group_states | sqlserver_hadr_replica_states | | 60 sekund |
| SQLServerDatabaseReplicaStates | sys.dm_hadr_database_replica_states<br>sys.availability_replicas | sqlserver_hadr_dbreplica_states | | 60 sekund |




## <a name="next-steps"></a>Další kroky

Podrobný postup povolení SQL Insights najdete v tématu [Povolení SQL Insights](sql-insights-enable.md) .
Přečtěte si [Nejčastější dotazy](../faq.md#sql-insights-preview) k nejčastějším dotazům o SQL Insights.
