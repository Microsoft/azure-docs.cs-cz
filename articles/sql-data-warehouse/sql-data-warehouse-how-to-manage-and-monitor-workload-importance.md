---
title: Správa a monitorování důležitosti úloh
description: Naučte se spravovat a monitorovat důležitost na úrovni požadavků v Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: ee9acb873c5118733de142045457028c3f4d5f61
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692707"
---
# <a name="manage-and-monitor-workload-importance-in-azure-sql-data-warehouse"></a>Správa a monitorování důležitosti úloh v Azure SQL Data Warehouse

Správa a monitorování důležitosti na úrovni požadavku v Azure SQL Data Warehouse pomocí zobrazení zobrazení dynamické správy a katalogu

## <a name="monitor-importance"></a>Důležitost monitorování

Sledujte důležitost pomocí sloupce nový důležitost v zobrazení dynamické správy [Sys. DM _pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) .
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

Zobrazení katalogu sys. workload_management_workload_classifiers obsahuje informace o klasifikátorech ve vaší instanci Azure SQL Data Warehouse. Chcete-li vyloučit systémem definované klasifikátory, které jsou mapovány na třídy prostředků, spusťte následující kód:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

Zobrazení katalogu, [Sys. workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest), obsahuje informace o parametrech použitých při vytváření klasifikátoru.  Následující dotaz ukazuje, že ExecReportsClassifier byl vytvořen v parametru ```membername``` pro hodnoty s ExecutiveReports:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![Výsledky dotazu](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Při řešení potíží s chybnou klasifikací doporučujeme, abyste při vytváření klasifikátorů úloh odebrali mapování rolí tříd prostředků. Následující kód vrátí existující členství v roli třídy prostředku. Spusťte sp_droprolemember pro každý ```membername``` vrácenou z odpovídající třídy prostředků.
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
