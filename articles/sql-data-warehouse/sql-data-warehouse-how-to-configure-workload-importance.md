---
title: Konfigurace důležitosti úloh
description: Přečtěte si, jak nastavit důležitost úrovně požadavku.
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
ms.openlocfilehash: 59ba4b936f6098b0d0b3f5e571f107af088206e0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692692"
---
# <a name="configure-workload-importance-in-azure-sql-data-warehouse"></a>Konfigurace důležitosti úloh v Azure SQL Data Warehouse

Nastavení důležitosti v SQL Data Warehouse umožňuje ovlivnit plánování dotazů. Dotazy s vyšší důležitostí budou naplánovány na spuštění před dotazy s nižší důležitostí. Pro přiřazení důležitosti dotazům je potřeba vytvořit klasifikátor úloh.

## <a name="create-a-workload-classifier-with-importance"></a>Vytvoření klasifikátoru úloh s důležitostí

V případě scénáře datového skladu často existují uživatelé, kteří potřebují své dotazy pro rychlé spuštění.  Uživatel může být vedoucí pracovníky společnosti, kteří potřebují spouštět sestavy, nebo může být uživatel analytikem, který spouští dotaz ad hoc. Pro přiřazení důležitosti dotazu vytvoříte třídění úloh.  Níže uvedené příklady používají novou syntaxi pro [Vytvoření třídění úloh](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) k vytvoření dvou klasifikátorů.  Člen může být jeden uživatel nebo skupina. Klasifikace jednotlivých uživatelů mají přednost před klasifikací rolí. Chcete-li najít existující uživatele datového skladu, spusťte příkaz:

```sql
Select name from sys.sysusers
```

Chcete-li vytvořit klasifikátor úlohy pro uživatele s vyšší důležitostí, proveďte následující:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  above_normal);  

```

Chcete-li vytvořit klasifikátor úloh pro uživatele, který spouští dotazy ad hoc s nižším významem spuštění:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
                   ,MEMBERNAME        = 'name'  
                   ,IMPORTANCE        =  below_normal);  
```

## <a name="next-steps"></a>Další kroky
- Další informace o správě úloh najdete v tématu [klasifikace úloh](sql-data-warehouse-workload-classification.md) .
- Další informace o důležitosti najdete v tématu [důležitost úloh](sql-data-warehouse-workload-importance.md) .

> [!div class="nextstepaction"]
> [Přejít na správu a monitorování důležitosti úloh](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
