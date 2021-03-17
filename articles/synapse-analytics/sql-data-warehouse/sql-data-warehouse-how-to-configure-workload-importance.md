---
title: Konfigurace důležitosti úloh pro vyhrazený fond SQL
description: Naučte se nastavit důležitost na úrovni požadavků ve službě Azure synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 05/15/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 38fb842cf90c110266f53b79a9ab2ef6157025b4
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98681281"
---
# <a name="configure-workload-importance-in-dedicated-sql-pool-for-azure-synapse-analytics"></a>Konfigurace důležitosti úloh ve vyhrazeném fondu SQL pro Azure synapse Analytics

Nastavení důležitosti ve vyhrazeném fondu SQL pro Azure synapse umožňuje ovlivnit plánování dotazů. Dotazy s vyšší důležitostí budou naplánovány na spuštění před dotazy s nižší důležitostí. Pro přiřazení důležitosti dotazům je potřeba vytvořit klasifikátor úloh.

## <a name="create-a-workload-classifier-with-importance"></a>Vytvoření klasifikátoru úloh s důležitostí

V případě zaneprázdněného systému, který potřebují rychlé spouštění dotazů, často ve scénáři datového skladu máte uživatele.  Uživatel může být vedoucí pracovníky společnosti, kteří potřebují spouštět sestavy, nebo může být uživatel analytikem, který spouští dotaz ad hoc. Chcete-li přiřadit důležitost, je třeba vytvořit klasifikátor úlohy a důležitost je přiřazena k dotazu.  Níže uvedené příklady používají k vytvoření dvou klasifikátorů syntaxi  [Vytvoření třídění úloh](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) . `Membername` může se jednat o jednoho uživatele nebo skupinu.  Chcete-li najít stávající vyhrazené uživatele fondu SQL, spusťte příkaz:

```sql
Select name from sys.sysusers
```

Chcete-li vytvořit klasifikátor úlohy pro uživatele s vyšší důležitostí, proveďte následující:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = above_normal);
```

Chcete-li vytvořit klasifikátor úloh pro uživatele, který spouští dotazy ad hoc s nižším významem spuštění:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = below_normal);
```

## <a name="next-steps"></a>Další kroky

- Další informace o správě úloh najdete v tématu [klasifikace úloh](sql-data-warehouse-workload-classification.md) .
- Další informace o důležitosti najdete v tématu [důležitost úloh](sql-data-warehouse-workload-importance.md) .

> [!div class="nextstepaction"]
> [Přejít na správu a monitorování důležitosti úloh](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
