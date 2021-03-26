---
title: Monitorování nasazení SQL pomocí SQL Insights (Preview)
description: Přehled SQL Insights v Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: d0bb5c55d3f7ba0573dfe9b511f4d31dcc64ed85
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567827"
---
# <a name="monitor-your-sql-deployments-with-sql-insights-preview"></a>Monitorování nasazení SQL pomocí SQL Insights (Preview)
SQL Insights je komplexní řešení pro monitorování libovolného produktu v [řadě Azure SQL](../../azure-sql/index.yml). SQL Insights používá [zobrazení dynamické správy](../../azure-sql/database/monitoring-with-dmvs.md) k vystavování dat, která potřebujete k monitorování stavu, diagnostikování problémů a optimalizaci výkonu.  

SQL Insights provádí vzdálenou kontrolu vzdáleně. Agenti monitorování na vyhrazených virtuálních počítačích se připojují k prostředkům SQL a vzdáleně shromažďují data. Shromážděná data jsou uložená v [protokolech Azure monitor](../logs/data-platform-logs.md), což umožňuje snadnou agregaci, filtrování a analýzu trendů. Shromážděná data můžete zobrazit ze [šablony SEŠITU](../visualize/workbooks-overview.md)SQL Insights, nebo se můžete podívat přímo na data pomocí [dotazů protokolu](../logs/get-started-queries.md).

## <a name="pricing"></a>Ceny
Pro SQL Insights se neúčtují žádné přímé náklady. Všechny náklady vycházejí z virtuálních počítačů, které shromažďují data, Log Analytics pracovní prostory, které ukládají data, a všech pravidel upozornění nakonfigurovaných na data. 

**Virtual Machines**

U virtuálních počítačů se účtují na základě cen publikovaných na stránce s [cenami virtuálních počítačů](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/linux/). Požadovaný počet virtuálních počítačů se bude lišit podle počtu připojovacích řetězců, které chcete monitorovat. Pro každé 100 připojovacích řetězců doporučujeme přidělit 1 virtuální počítač velikosti Standard_B2s. Další podrobnosti najdete v tématu [požadavky na virtuální počítače Azure](sql-insights-enable.md#azure-virtual-machine-requirements) .

**Pracovní prostory služby Log Analytics**

U Log Analyticsch pracovních prostorů se účtují na základě cen publikovaných na [stránce s cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/). Pracovní prostory Log Analytics používané službou SQL Insights budou účtovat náklady na příjem dat, uchovávání dat a (volitelně) Export dat. Přesné poplatky se budou lišit v závislosti na množství dat, která jsou ingestovaná, zachovaná a exportovaná. Množství těchto dat se následně bude lišit v závislosti na vaší databázové aktivitě a nastaveních kolekce definovaných v [profilech monitorování](sql-insights-enable.md#create-sql-monitoring-profile).

**Pravidla upozornění**

Pravidla výstrah v Azure Monitor se účtují na základě cen publikovaných na [stránce s cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/). Pokud se rozhodnete [vytvořit výstrahy s SQL Insights](sql-insights-alerts.md), budou se vám účtovat všechna vytvořená pravidla upozornění a všechna oznámení.

## <a name="supported-versions"></a>Podporované verze 
SQL Insights podporuje následující verze SQL Server:
- SQL Server 2012 a novější

SQL Insights podporuje SQL Server spuštěné v následujících prostředích:
- Azure SQL Database
- Spravovaná instance Azure SQL
- SQL Server ve službě Azure Virtual Machines (SQL Server běží na virtuálních počítačích zaregistrovaných u poskytovatele [virtuálních počítačů SQL](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) )
- Virtuální počítače Azure (SQL Server spuštěné na virtuálních počítačích, které nejsou registrované u poskytovatele [virtuálních počítačů SQL](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) )

SQL Insights nepodporuje ani neomezenou podporu pro následující:
- **Instance mimo Azure**: SQL Server běžící na virtuálních počítačích mimo Azure se nepodporují.
- **Azure SQL Database elastické fondy**: pro elastické fondy nejde shromáždit metriky. Pro databáze v rámci elastických fondů nelze shromáždit metriky.
- **Azure SQL Database nízké úrovně služeb**: pro databáze na [úrovni služeb](../../azure-sql/database/resource-limits-dtu-single-databases.md) Basic, S0, S1 a S2 nelze shromáždit metriky.
- **Azure SQL Database úroveň bez serveru**: metriky je možné shromažďovat pro databáze pomocí výpočetní vrstvy bez serveru. Proces shromažďování metrik ale resetuje časovač zpoždění automatického pozastavení, takže databáze brání v vstupu do stavu automatického pozastavení.
- **Sekundární repliky**: metriky se dají shromažďovat jenom pro jednu sekundární repliku na databázi. Pokud má databáze více než jednu sekundární repliku, je možné monitorovat pouze 1.
- **Ověřování pomocí Azure Active Directory**: jedinou podporovanou metodou [ověřování](../../azure-sql/database/logins-create-manage.md#authentication-and-authorization) pro monitorování je ověřování SQL. Pro SQL Server na virtuálním počítači Azure se nepodporuje ověřování pomocí služby Active Directory na vlastním řadiči domény.  

## <a name="open-sql-insights"></a>Otevřete SQL Insights.
Otevřete SQL Insights výběrem **SQL (Preview)** v části **přehled** v nabídce **Azure monitor** v Azure Portal. Kliknutím na dlaždici načtěte prostředí pro typ SQL, který sledujete.

:::image type="content" source="media/sql-insights/portal.png" alt-text="SQL Insights v Azure Portal.":::

## <a name="enable-sql-insights"></a>Povolit SQL Insights 
Pokyny k povolení SQL Insights najdete v tématu [Povolení SQL Insights](sql-insights-enable.md) .

## <a name="troubleshoot-sql-insights"></a>Řešení potíží s SQL Insights 
Pokyny k řešení potíží s SQL Insights najdete v tématu [řešení potíží s SQL Insights](sql-insights-troubleshoot.md) .

## <a name="data-collected-by-sql-insights"></a>Data shromážděná službou SQL Insights
SQL Insights provádí vzdálenou kontrolu vzdáleně. Do virtuálních počítačů, na kterých běží SQL Server, nenainstalujeme žádné agenty. 

SQL Insights používá k vzdálenému shromažďování dat z vašich prostředků SQL vyhrazené monitorování virtuálních počítačů. Každý virtuální počítač pro monitorování bude mít nainstalovaného [agenta Azure monitor](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview) a rozšíření WLI (úlohy Insights). Rozšíření WLI zahrnuje Open sourceho [agenta telegraf](https://www.influxdata.com/time-series-platform/telegraf/). SQL Insights používá [pravidla shromažďování dat](https://docs.microsoft.com/azure/azure-monitor/agents/data-collection-rule-overview) k určení nastavení shromažďování dat pro [modul plug-in telegraf SQL Server](https://www.influxdata.com/integration/microsoft-sql-server/).

K dispozici jsou různé sady dat Azure SQL Database, spravované instance Azure SQL a SQL Server. Dostupná data jsou popsána v následujících tabulkách. Můžete upravit, které datové sady se mají shromažďovat, a frekvenci shromažďování při [vytváření profilu monitorování](sql-insights-enable.md#create-sql-monitoring-profile).

Následující tabulky obsahují následující sloupce:
- **Popisný název**: název dotazu, který je zobrazený na Azure Portal při vytváření profilu monitorování
- **Název konfigurace**: název dotazu, který je zobrazený na Azure Portal při úpravách profilu monitorování.
- **Obor názvů**: název dotazu, který se nachází v pracovním prostoru Log Analytics. Tento identifikátor se zobrazí v tabulce **InsighstMetrics** pro `Namespace` vlastnost ve sloupci. `Tags`
- **Zobrazení dynamické správy**: Dynamická spravovaná zobrazení používaná k vytvoření datové sady
- **Povoleno ve výchozím nastavení**: Určuje, zda jsou data shromažďována ve výchozím nastavení.
- **Výchozí četnost shromažďování** dat: jak často se data shromažďují ve výchozím nastavení.

### <a name="data-for-azure-sql-database"></a>Data pro Azure SQL Database 
| Popisný název | Název konfigurace | Obor názvů | Zobrazení dynamické správy | Ve výchozím nastavení povoleno | Výchozí frekvence shromažďování |
|:---|:---|:---|:---|:---|:---|
| Statistika čekání databáze | AzureSQLDBWaitStats | sqlserver_azuredb_waitstats | sys.dm_db_wait_stats | No | NA |
| Statistika čekání DBO | AzureSQLDBOsWaitstats | sqlserver_waitstats |sys.dm_os_wait_stats | Yes | 60 sekund |
| Správci paměti | AzureSQLDBMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Yes | 60 sekund |
| V/V databází | AzureSQLDBDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.database_files<br>tempdb.sys .database_files | Yes | 60 sekund |
| Vlastnosti serveru | AzureSQLDBServerProperties | sqlserver_server_properties | sys.dm_os_job_object<br>sys.database_files<br>tabulce. databáze<br>tabulce. [database_service_objectives] | Yes | 60 sekund |
| Čítače výkonu | AzureSQLDBPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases | Yes | 60 sekund |
| Statistiky prostředků | AzureSQLDBResourceStats | sqlserver_azure_db_resource_stats | sys.dm_db_resource_stats | Yes | 60 sekund |
| Zásady správného řízení prostředků | AzureSQLDBResourceGovernance | sqlserver_db_resource_governance | sys.dm_user_db_resource_governance | Yes | 60 sekund |
| Žádosti | AzureSQLDBRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | No | NA |
| Plánovače| AzureSQLDBSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | No | NA  |

### <a name="data-for-azure-sql-managed-instance"></a>Data pro spravovanou instanci Azure SQL 

| Popisný název | Název konfigurace | Obor názvů | Zobrazení dynamické správy | Ve výchozím nastavení povoleno | Výchozí frekvence shromažďování |
|:---|:---|:---|:---|:---|:---|
| Statistiky čekání | AzureSQLMIOsWaitstats | sqlserver_waitstats | sys.dm_os_wait_stats | Yes | 60 sekund |
| Správci paměti | AzureSQLMIMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Yes | 60 sekund |
| V/V databází | AzureSQLMIDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | Yes | 60 sekund |
| Vlastnosti serveru | AzureSQLMIServerProperties | sqlserver_server_properties | sys.server_resource_stats | Yes | 60 sekund |
| Čítače výkonu | AzureSQLMIPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters<br>sys.databases| Yes | 60 sekund |
| Statistiky prostředků | AzureSQLMIResourceStats | sqlserver_azure_db_resource_stats | sys.server_resource_stats | Yes | 60 sekund |
| Zásady správného řízení prostředků | AzureSQLMIResourceGovernance | sqlserver_instance_resource_governance | sys.dm_instance_resource_governance | Yes | 60 sekund |
| Žádosti | AzureSQLMIRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | No | NA |
| Plánovače | AzureSQLMISchedulers | sqlserver_schedulers | sys.dm_os_schedulers | No | NA |

### <a name="data-for-sql-server"></a>Data pro SQL Server

| Popisný název | Název konfigurace | Obor názvů | Zobrazení dynamické správy | Ve výchozím nastavení povoleno | Výchozí frekvence shromažďování |
|:---|:---|:---|:---|:---|:---|
| Statistiky čekání | SQLServerWaitStatsCategorized | sqlserver_waitstats | sys.dm_os_wait_stats | Yes | 60 sekund | 
| Správci paměti | SQLServerMemoryClerks | sqlserver_memory_clerks | sys.dm_os_memory_clerks | Yes | 60 sekund |
| V/V databází | SQLServerDatabaseIO | sqlserver_database_io | sys.dm_io_virtual_file_stats<br>sys.master_files | Yes | 60 sekund |
| Vlastnosti serveru | SQLServerProperties | sqlserver_server_properties | sys.dm_os_sys_info | Yes | 60 sekund |
| Čítače výkonu | SQLServerPerformanceCounters | sqlserver_performance | sys.dm_os_performance_counters | Yes | 60 sekund |
| Místo na svazku | SQLServerVolumeSpace | sqlserver_volume_space | sys.master_files | Yes | 60 sekund |
| SQL Server procesor | SQLServerCpu | sqlserver_cpu | sys.dm_os_ring_buffers | Yes | 60 sekund |
| Plánovače | SQLServerSchedulers | sqlserver_schedulers | sys.dm_os_schedulers | No | NA |
| Žádosti | SQLServerRequests | sqlserver_requests | sys.dm_exec_sessions<br>sys.dm_exec_requests<br>sys.dm_exec_sql_text | No | NA |
| Stavy repliky dostupnosti | SQLServerAvailabilityReplicaStates | sqlserver_hadr_replica_states | sys.dm_hadr_availability_replica_states<br>sys.availability_replicas<br>sys.availability_groups<br>sys.dm_hadr_availability_group_states | No | 60 sekund |
| Repliky databáze dostupnosti | SQLServerDatabaseReplicaStates | sqlserver_hadr_dbreplica_states | sys.dm_hadr_database_replica_states<br>sys.availability_replicas | No | 60 sekund |

## <a name="next-steps"></a>Další kroky

- Pokyny k povolení SQL Insights najdete v tématu [Povolení SQL Insights](sql-insights-enable.md) .
- Přečtěte si nejčastější [dotazy](../faq.md#sql-insights-preview) k nejčastějším dotazům o SQL Insights.
