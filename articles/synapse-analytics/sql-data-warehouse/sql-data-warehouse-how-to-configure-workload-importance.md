---
title: Konfigurace důležitosti úloh
description: Naučte se nastavit důležitost na úrovni požadavků ve službě Azure synapse Analytics.
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
ms.openlocfilehash: 0ab7b8be8780f7edb2734d99587bc7709ced9436
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633353"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Konfigurace důležitosti úloh ve službě Azure synapse Analytics

Nastavení důležitosti v synapse SQL pro Azure synapse umožňuje ovlivnit plánování dotazů. Dotazy s vyšší důležitostí budou naplánovány na spuštění před dotazy s nižší důležitostí. Pro přiřazení důležitosti dotazům je potřeba vytvořit klasifikátor úloh.

## <a name="create-a-workload-classifier-with-importance"></a>Vytvoření klasifikátoru úloh s důležitostí

V případě scénáře datového skladu často existují uživatelé, kteří potřebují své dotazy pro rychlé spuštění.  Uživatel může být vedoucí pracovníky společnosti, kteří potřebují spouštět sestavy, nebo může být uživatel analytikem, který spouští dotaz ad hoc. Pro přiřazení důležitosti dotazu vytvoříte třídění úloh.  Níže uvedené příklady používají novou syntaxi pro [Vytvoření třídění úloh](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) k vytvoření dvou klasifikátorů. `Membername`může se jednat o jednoho uživatele nebo skupinu. Klasifikace jednotlivých uživatelů mají přednost před klasifikací rolí. Chcete-li najít existující uživatele datového skladu, spusťte příkaz:

```sql
Select name from sys.sysusers
```

Chcete-li vytvořit klasifikátor úlohy pro uživatele s vyšší důležitostí, proveďte následující:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  above_normal);  

```

Chcete-li vytvořit klasifikátor úloh pro uživatele, který spouští dotazy ad hoc s nižším významem spuštění:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  below_normal);  
```

## <a name="next-steps"></a>Další kroky

- Další informace o správě úloh najdete v tématu [klasifikace úloh](sql-data-warehouse-workload-classification.md) .
- Další informace o důležitosti najdete v tématu [důležitost úloh](sql-data-warehouse-workload-importance.md) .

> [!div class="nextstepaction"]
> [Přejít na správu a monitorování důležitosti úloh](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
