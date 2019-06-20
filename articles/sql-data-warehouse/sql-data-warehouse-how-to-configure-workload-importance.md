---
title: Konfigurace úlohy význam ve službě Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Zjistěte, jak nastavit úroveň důležitosti požadavku.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload management
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 10a1fe2bff43b6799f47fc0245802ce578ef8f8f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165530"
---
# <a name="configure-workload-importance-in-azure-sql-data-warehouse"></a>Konfigurace úlohy význam ve službě Azure SQL Data Warehouse

Nastavení význam v SQL Data Warehouse umožňuje ovlivnit plánování dotazy. Dotazy s vyšším důležitostí bude naplánováno pro spuštění před dotazy s důležitostí nižší. Přiřadit důležitost na dotazy, je potřeba vytvořit úlohu třídění.

## <a name="create-a-workload-classifier-with-importance"></a>Vytvoření klasifikátoru úlohy s důležitostí

Často v scénář datového skladu, je nutné uživatele, kteří potřebují jejich dotazů pro rychlé spouštění.  Uživatel může být, že členové vedení společnosti, kteří potřebují spouštět sestavy nebo uživatel může být analytikovi spuštění dotazu ad hoc. Vytváření úlohy třídění přiřadit důležitost do dotazu.  Následující příklady používají nové [vytvořit úlohu třídění](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) syntaxe pro vytvoření dvěma Klasifikátory.  Jméno může být jenom jednoho konkrétního uživatele nebo skupinu. Klasifikace jednotlivých uživatelů mají přednost před role klasifikace. Najít existující uživatele datového skladu, spusťte:

```sql
Select name from sys.sysusers
```

Vytvoření úlohy třídění pro uživatele s větší význam, spusťte:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  above_normal);  

```

Vytvoření úlohy třídění pro spouštění dotazů ad hoc s nižší důležitostí spustit uživatele:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  below_normal);  
```

## <a name="next-steps"></a>Další kroky
- Další informace o správu úloh, naleznete v tématu [klasifikace pracovního vytížení](sql-data-warehouse-workload-classification.md)
- Další informace o důležitosti, naleznete v tématu [význam pracovního vytížení](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Přejděte na spravovat a monitorovat úlohy význam](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
