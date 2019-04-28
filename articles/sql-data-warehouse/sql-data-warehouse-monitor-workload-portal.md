---
title: Monitorování úloh – Azure portal | Dokumentace Microsoftu
description: Monitorování Azure SQL Data Warehouse pomocí webu Azure portal
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 6c8ce090039e3d5cc85c86d920710294de2165f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60748822"
---
# <a name="monitor-workload---azure-portal"></a>Monitorování úloh – Azure portal

Tento článek popisuje, jak pomocí webu Azure portal k monitorování vaší úlohy. Jedná se o nastavení monitorování protokolů Azure k prozkoumání dotazu spuštění a úlohu vývoj pomocí log analytics pro [Azure SQL Data Warehouse](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- Azure SQL Data Warehouse: Jsme bude pokračovat ve shromažďování protokolů pro SQL data warehouse. Pokud nemáte zřízené SQL data warehouse, přečtěte si pokyny v [vytvořit SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial).

## <a name="create-a-log-analytics-workspace"></a>Vytvoření pracovního prostoru Log Analytics

Přejděte do okna procházení pro pracovní prostory Log Analytics a vytvoření pracovního prostoru 

![Pracovní prostory Log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![Přidat pracovní prostor analýzy](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![Přidat pracovní prostor analýzy](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

Podrobné informace o pracovních prostorech naleznete na následujícím [dokumentaci](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#create-a-workspace).

## <a name="turn-on-diagnostic-logs"></a>Zapnout diagnostické protokoly 

Konfigurace nastavení diagnostiky a vygenerovat tento počet protokolů ze služby SQL data warehouse. Protokoly se skládají z zobrazení telemetrie ekvivalentní nejčastěji používané výkonu pro SQL Data Warehouse odstraňování potíží v zobrazení dynamické správy datového skladu. Aktuálně jsou podporovány následující zobrazení:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![Povolení diagnostických protokolů](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

Protokoly můžete se emitovat do služby Azure Storage, Stream Analytics a Log Analytics. Pro účely tohoto kurzu vyberte Log Analytics.

![Zadejte protokoly](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Spouštějte dotazy Log Analytics

Přejděte do pracovního prostoru Log Analytics, kde můžete provádět následující akce:

- Analýza protokolů pomocí dotazů na protokoly a ukládat dotazy pro opakované použití
- Ukládat dotazy pro opakované použití
- Vytvoření upozornění protokolu
- Výsledky dotazu připnout na řídicí panel

Podrobnosti o možnostech dotazů na protokoly, navštivte následující [dokumentaci](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

![Editor pracovního prostoru log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspace_editor.png)



![Dotazy na pracovní prostor analýzy protokolů](media/sql-data-warehouse-monitor/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Ukázkové dotazy log



```Kusto
//List all queries 
AzureDiagnostics
| where Category contains "ExecRequests"
| project TimeGenerated, StartTime_t, EndTime_t, Status_s, Command_s, ResourceClass_s, duration=datetime_diff('millisecond',EndTime_t, StartTime_t)
```
```Kusto
//Chart the most active resource classes
AzureDiagnostics
| where Category contains "ExecRequests"
| where Status_s == "Completed"
| summarize totalQueries = dcount(RequestId_s) by ResourceClass_s
| render barchart 
```
```Kusto
//Count of all queued queries
AzureDiagnostics
| where Category contains "waits" 
| where Type_s == "UserConcurrencyResourceType"
| summarize totalQueuedQueries = dcount(RequestId_s)
```
## <a name="next-steps"></a>Další postup

Teď, když jste nastavili a nakonfigurovali protokoly Azure monitoru [přizpůsobení řídicích panelů Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) sdílet napříč vaším týmem.
