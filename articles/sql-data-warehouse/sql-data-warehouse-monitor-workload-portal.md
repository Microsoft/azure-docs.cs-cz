---
title: Monitorování úlohy – Azure Portal | Microsoft Docs
description: Monitorování Azure SQL Data Warehouse pomocí Azure Portal
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 73e7312eacd11fbec052d2b0b7781528c3b3d50f
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575385"
---
# <a name="monitor-workload---azure-portal"></a>Monitorování úloh – Azure Portal

Tento článek popisuje, jak pomocí Azure Portal monitorovat vaše úlohy. Zahrnuje nastavení protokolů Azure Monitor pro zkoumání provádění dotazů a trendy úloh pomocí Log Analytics pro [Azure SQL Data Warehouse](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- Azure SQL Data Warehouse: Budeme shromažďovat protokoly pro SQL Data Warehouse. Pokud nemáte zřízenou SQL Data Warehouse, přečtěte si pokyny v tématu [vytvoření SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial).

## <a name="create-a-log-analytics-workspace"></a>Vytvoření pracovního prostoru Log Analytics

Přejděte do okna procházení pro Log Analytics pracovní prostory a vytvořte pracovní prostor. 

![Pracovní prostory služby Log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![Přidat pracovní prostor analýzy](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![Přidat pracovní prostor analýzy](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

Další informace o pracovních prostorech najdete v následující [dokumentaci](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#create-a-workspace).

## <a name="turn-on-diagnostic-logs"></a>Zapnout diagnostické protokoly 

Nakonfigurujte nastavení diagnostiky k vygenerování protokolů z SQL Data Warehouse. Protokoly se skládají z zobrazení telemetrie datového skladu ekvivalentních k nejčastěji používaným řešením potíží s výkonem zobrazení dynamické správy pro SQL Data Warehouse. V současné době jsou podporována následující zobrazení:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![Povolení diagnostických protokolů](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

Protokoly je možné vysílat Azure Storage, Stream Analytics nebo Log Analytics. Pro tento kurz vyberte Log Analytics.

![Zadat protokoly](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Spouštění dotazů proti Log Analytics

Přejděte do pracovního prostoru Log Analytics, kde můžete provádět následující akce:

- Analýza protokolů pomocí dotazů protokolu a ukládání dotazů pro opakované použití
- Ukládat dotazy pro opakované použití
- Vytváření výstrah protokolu
- Připnutí výsledků dotazu na řídicí panel

Podrobnosti o možnostech dotazů protokolu najdete v následující [dokumentaci](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

![Editor pracovního prostoru Log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspace_editor.png)



![Log Analytics dotazy na pracovní prostor](media/sql-data-warehouse-monitor/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Ukázky dotazů protokolu



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
## <a name="next-steps"></a>Další kroky

Teď, když jste nastavili a nakonfigurovali protokoly služby Azure monitor, můžete [přizpůsobit řídicí panely Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) pro sdílení v rámci svého týmu.
