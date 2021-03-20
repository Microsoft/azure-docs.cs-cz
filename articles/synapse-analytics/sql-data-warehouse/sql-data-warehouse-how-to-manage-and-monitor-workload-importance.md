---
title: Správa a monitorování důležitosti úloh ve vyhrazeném fondu SQL
description: Přečtěte si, jak spravovat a monitorovat vyhrazený fond SQL pro důležitost požadavků na úrovni požadavku pro Azure synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 12b75ad3746cd0f54e27e474e0fd13bb0bba0e05
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98685238"
---
# <a name="manage-and-monitor-workload-importance-in-dedicated-sql-pool-for-azure-synapse-analytics"></a>Správa a monitorování důležitosti úloh ve vyhrazeném fondu SQL pro Azure synapse Analytics

Správa a monitorování vyhrazené úrovně požadavků fondu SQL ve službě Azure synapse pomocí zobrazení zobrazení dynamické správy a katalogu

## <a name="monitor-importance"></a>Důležitost monitorování

Sledujte důležitost pomocí sloupce nový důležitost v zobrazení dynamické správy [Sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .
Níže uvedený dotaz monitorování ukazuje dobu odeslání a čas spuštění dotazů. Přečtěte si čas odeslání a čas zahájení spolu s důležitostí, abyste viděli, jak důležitost ovlivnila plánování.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Pokud chcete zobrazit další informace o plánování dotazů, použijte zobrazení katalogu.

## <a name="manage-importance-with-catalog-views"></a>Správa důležitosti pomocí zobrazení katalogu

Zobrazení katalogu sys.workload_management_workload_classifiers obsahuje informace o klasifikátorech. Chcete-li vyloučit systémem definované klasifikátory, které jsou mapovány na třídy prostředků, spusťte následující kód:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

Zobrazení katalogu [Sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)obsahuje informace o parametrech použitých při vytváření klasifikátoru.  Níže uvedený dotaz ukazuje, že v ```membername``` parametru pro hodnoty s ExecutiveReports byl vytvořen ExecReportsClassifier:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![výsledky dotazu](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Při řešení potíží s chybnou klasifikací doporučujeme, abyste při vytváření klasifikátorů úloh odebrali mapování rolí tříd prostředků. Následující kód vrátí existující členství v roli třídy prostředku. Spusťte sp_droprolemember pro každou ```membername``` vrácenou z odpovídající třídy prostředku.
Níže je uveden příklad kontroly existence před vyřazením klasifikátoru úloh:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Další kroky

- Další informace o klasifikaci najdete v tématu [klasifikace úloh](sql-data-warehouse-workload-classification.md).
- Další informace o důležitosti najdete v tématu [důležitost úloh](sql-data-warehouse-workload-importance.md) .

> [!div class="nextstepaction"]
> [Přejít na konfigurace důležitosti úlohy](sql-data-warehouse-how-to-configure-workload-importance.md)
