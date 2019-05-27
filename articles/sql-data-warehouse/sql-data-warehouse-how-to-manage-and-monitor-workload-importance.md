---
title: Správa a monitorování úloh význam ve službě Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Zjistěte, jak spravovat a sledovat žádosti o úroveň důležitosti
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 8d6cd80f569be58b520a53faa0d4bda8f86af104
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003488"
---
# <a name="manage-and-monitor-workload-importance-in-azure-sql-data-warehouse"></a>Správa a monitorování úloh význam ve službě Azure SQL Data Warehouse

Spravovat a sledovat žádosti o úroveň důležitosti ve službě Azure SQL Data Warehouse pomocí zobrazení dynamické správy a zobrazení katalogu.

## <a name="monitor-importance"></a>Důležitost monitorování

Monitorování význam pomocí nový sloupec význam v [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) zobrazení dynamické správy.
Níže monitorování dotazu ukazuje čas odeslání a počáteční čas pro dotazy. Zkontrolujte čas odeslání a počáteční čas spolu s význam naleznete v tématu jak k nim plánování význam.

```sql
SELECT s.login_name, r.status, r.importance, r.submit_time, r.start_time
  FROM sys.dm_pdw_exec_sessions s
  JOIN sys.dm_pdw_exec_requests r ON s.session_id = r.session_id
  WHERE r.resource_class is not null
ORDER BY r.start_time
```

Chcete-li prozkoumat další jak dotazy se plán, použijte zobrazení katalogu.

## <a name="manage-importance-with-catalog-views"></a>Správa význam pomocí zobrazení katalogu

Zobrazení katalogu sys.workload_management_workload_classifiers obsahuje informace o klasifikátorů ve vaší instanci Azure SQL Data Warehouse. Chcete-li vyloučit třídění, které se mapují na prostředek třídy definované v systému spusťte následující kód:

```sql
SELECT *
  FROM sys.workload_management_workload_classifiers
  WHERE classifier_id > 12
```

Zobrazení katalogu [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql?view=azure-sqldw-latest), obsahuje informace o parametrech vytvoření klasifikátoru použít.  Níže ukazuje dotaz, které ExecReportsClassifier byl vytvořen na ```membername``` parametr pro hodnoty s ExecutiveReports:

```sql
SELECT c.name,cd.classifier_type, classifier_value
  FROM sys.workload_management_workload_classifiers c
  JOIN sys.workload_management_workload_classifier_details cd
    ON cd.classifier_id = c.classifier_id
  WHERE c.name = 'ExecReportsClassifier'
```

![Výsledky dotazu](./media/sql-data-warehouse-how-to-manage-and-monitor-workload-importance/wlm-query-results.png)

Pokud chcete zjednodušit Poradce při potížích chybnou, doporučujeme že odebrat mapování role třídy prostředků při vytváření úlohy Klasifikátory. Následující kód vrátí existující prostředek třídy členství v rolích. Spusťte pro každou sp_droprolemember ```membername``` vrátilo odpovídajícího třídy prostředků.
Níže je příklad kontroly existence před vyřazením klasifikátor úlohy:

```sql
IF EXISTS (SELECT 1 FROM sys.workload_management_workload_classifiers WHERE name = 'ExecReportsClassifier')
  DROP WORKLOAD CLASSIFIER ExecReportsClassifier;
GO
```

## <a name="next-steps"></a>Další postup
- Další informace o klasifikaci, naleznete v tématu [úloh klasifikace](sql-data-warehouse-workload-classification.md).
- Další informace o důležitosti, naleznete v tématu [význam pracovního vytížení](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Přejít na důležitosti úlohy konfigurace ](sql-data-warehouse-how-to-configure-workload-importance.md)
