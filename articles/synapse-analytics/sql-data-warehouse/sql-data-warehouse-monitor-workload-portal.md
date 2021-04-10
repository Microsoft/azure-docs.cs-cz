---
title: Monitorování úloh – Azure Portal
description: Monitorování synapse SQL pomocí Azure Portal
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: gaursa
ms.reviewer: jrasnick
ms.openlocfilehash: 3fb81ffc24ed6073e5398b14b7f490a0f63df84f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585583"
---
# <a name="monitor-workload---azure-portal"></a>Monitorování úloh – Azure Portal

Tento článek popisuje, jak pomocí Azure Portal monitorovat vaše úlohy. Zahrnuje nastavení protokolů Azure Monitor pro zkoumání provádění dotazů a trendy úloh pomocí Log Analytics pro [synapse SQL](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- Fond SQL: bude shromažďovat protokoly pro fond SQL. Pokud nemáte zřízen Fond SQL, přečtěte si pokyny v tématu [Vytvoření fondu SQL](./load-data-from-azure-blob-storage-using-copy.md).

## <a name="create-a-log-analytics-workspace"></a>Vytvoření pracovního prostoru služby Log Analytics

Přejděte do okna procházení pro Log Analytics pracovní prostory a vytvořte pracovní prostor.

![Pracovní prostory služby Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![Snímek obrazovky zobrazuje Log Analytics pracovní prostory, kde můžete vybrat přidat.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![Snímek obrazovky ukazuje Log Analytics pracovní prostor, do kterého můžete zadat hodnoty.](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

Další informace o pracovních prostorech najdete v následující [dokumentaci](../../azure-monitor/logs/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace).

## <a name="turn-on-resource-logs"></a>Zapnout protokoly prostředků

Nakonfigurujte nastavení diagnostiky k vygenerování protokolů z vašeho fondu SQL. Protokoly se skládají z zobrazení telemetrie, která odpovídají nejčastěji používaným řešením potíží s výkonem zobrazení dynamické správy. V současné době jsou podporována následující zobrazení:

- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

![Povolení protokolů prostředků](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

Protokoly je možné vysílat Azure Storage, Stream Analytics nebo Log Analytics. Pro tento kurz vyberte Log Analytics.

![Zadat protokoly](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Spouštění dotazů proti Log Analytics

Přejděte do pracovního prostoru Log Analytics, kde můžete provádět následující akce:

- Analýza protokolů pomocí dotazů protokolu a ukládání dotazů pro opakované použití
- Ukládat dotazy pro opakované použití
- Vytvoření upozornění protokolu
- Připnutí výsledků dotazu na řídicí panel

Podrobnosti o možnostech dotazů protokolu najdete v následující [dokumentaci](/azure/data-explorer/kusto/query/?bc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2fsql-data-warehouse%2ftoc.json).

![Editor pracovního prostoru Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![Log Analytics dotazy na pracovní prostor](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

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

Teď, když jste nastavili a nakonfigurovali protokoly služby Azure monitor, můžete [přizpůsobit řídicí panely Azure](../../azure-portal/azure-portal-dashboards.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) pro sdílení v rámci svého týmu.