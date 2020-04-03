---
title: Správa a monitorování důležitosti úloh
description: Zjistěte, jak spravovat a sledovat důležitost úrovně požadavků v Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 114f8d637a927a899807a676fb3e1b45f5c7687c
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585752"
---
# <a name="manage-and-monitor-workload-importance-in-azure-synapse-analytics"></a>Správa a monitorování důležitosti pracovního vytížení v Azure Synapse Analytics

Spravujte a monitorujte důležitost úrovně požadavků synapse SQL v Azure Synapse pomocí dmv a zobrazení katalogu.

## <a name="monitor-importance"></a>Sledovat důležitost

Sledování důležitosti pomocí nového sloupce důležitosti v zobrazení dynamické správy [sys.dm_pdw_exec_requests.](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest)
Níže uvedený dotaz monitorování zobrazuje čas odeslání a čas zahájení dotazů. Zkontrolujte čas odeslání a čas zahájení spolu s důležitostí, abyste zjistili, jak důležitost ovlivnila plánování.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Chcete-li se podívat dále na plánování dotazů, použijte zobrazení katalogu.

## <a name="manage-importance-with-catalog-views"></a>Správa důležitosti pomocí zobrazení katalogu

Zobrazení katalogu sys.workload_management_workload_classifiers obsahuje informace o klasifikátorech. Chcete-li vyloučit systémem definované klasifikátory, které mapují na třídy prostředků, spusťte následující kód:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

Zobrazení katalogu [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest)obsahuje informace o parametrech použitých při vytváření třídění.  Níže uvedený dotaz ukazuje, že ExecReportsClassifier byl vytvořen na ```membername``` parametr pro hodnoty s ExecutiveReports:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![výsledky dotazu](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Chcete-li zjednodušit řešení potíží s chybnou klasifikací, doporučujeme odebrat mapování rolí třídy prostředků při vytváření klasifikátorů úloh. Níže uvedený kód vrátí existující členství v roli třídy prostředků. Spusťte ```membername``` sp_droprolemember pro každou vrácenou z odpovídající třídy prostředků.
Níže je uveden příklad kontroly existence před uvolněním třídění úlohy:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Další kroky
- Další informace o klasifikaci naleznete v [tématu Klasifikace pracovního vytížení](sql-data-warehouse-workload-classification.md).
- Další informace o důležitosti naleznete v [tématu Důležitost pracovního vytížení](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Přejít na Konfigurovat důležitost pracovního vytížení](sql-data-warehouse-how-to-configure-workload-importance.md)
