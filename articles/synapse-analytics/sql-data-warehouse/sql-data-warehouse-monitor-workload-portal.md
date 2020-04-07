---
title: Monitorování úloh – portál Azure
description: Monitorování Synapse SQL pomocí portálu Azure
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 0658a775e40c1fc433c7c2e1d853493544e74ee4
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743213"
---
# <a name="monitor-workload---azure-portal"></a>Monitorování úloh – portál Azure

Tento článek popisuje, jak pomocí portálu Azure ke sledování úlohy. To zahrnuje nastavení protokolů monitorování Azure pro zkoumání trendů provádění dotazů a úloh pomocí analýzy protokolů pro [Synapse SQL](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure: Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.
- Fond SQL: Budeme shromažďovat protokoly pro fond SQL. Pokud nemáte zřízenfond SQL zřízena, naleznete v pokynech v [vytvoření fondu SQL](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="create-a-log-analytics-workspace"></a>Vytvoření pracovního prostoru služby Log Analytics

Přejděte do okna procházení pracovních prostorů Log Analytics a vytvořte pracovní prostor

![Pracovní prostory služby Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![Přidání pracovního prostoru Analytics](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![Přidání pracovního prostoru Analytics](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

Další podrobnosti o pracovních prostorech naleznete v následující [dokumentaci](../../azure-monitor/learn/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace).

## <a name="turn-on-diagnostic-logs"></a>Zapnutí diagnostických protokolů

Nakonfigurujte nastavení diagnostiky pro vyzařování protokolů z fondu SQL. Protokoly se skládají z telemetrických zobrazení ekvivalentní chod nejčastěji používaným řešením potíží s výkonem DMV. V současné době jsou podporována následující zobrazení:

- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

![Povolení diagnostických protokolů](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

Protokoly lze emitovat do Azure Storage, Stream Analytics nebo Log Analytics. V tomto kurzu vyberte Log Analytics.

![Určení protokolů](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Spouštění dotazů v log analytics

Přejděte do pracovního prostoru Log Analytics, kde můžete provést následující akce:

- Analýza protokolů pomocí dotazů protokolu a uložení dotazů pro opakované použití
- Uložit dotazy k opakovanému použití
- Vytvoření upozornění protokolu
- Připnutí výsledků dotazu na řídicí panel

Podrobnosti o možnostech dotazů protokolu naleznete v následující [dokumentaci](../../azure-monitor/log-query/query-language.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

![Editor pracovních prostorů Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![Dotazy na pracovní prostor Analýzy protokolů](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>Ukázkové dotazy protokolu

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

Teď, když jste nastavili a nakonfigurovali protokoly monitorování Azure, [přizpůsobte řídicí panely Azure](../../azure-portal/azure-portal-dashboards.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) tak, aby je sdílely v rámci celého týmu.
