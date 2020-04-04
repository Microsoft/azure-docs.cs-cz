---
title: Konfigurace důležitosti úloh
description: Přečtěte si, jak nastavit důležitost na úrovni požadavku v Azure Synapse Analytics.
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
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633353"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Konfigurace důležitosti úlohy v Azure Synapse Analytics

Nastavení důležitosti v Synapse SQL pro Azure Synapse umožňuje ovlivnit plánování dotazů. Dotazy s vyšší důležitostí budou naplánovány ke spuštění před dotazy s nižší důležitostí. Chcete-li přiřadit důležitost dotazům, musíte vytvořit třídění úlohy.

## <a name="create-a-workload-classifier-with-importance"></a>Vytvoření třídění pracovního vytížení s důležitostí

Často ve scénáři datového skladu máte uživatele, kteří potřebují jejich dotazy ke spuštění rychle.  Uživatel může být vedoucí pracovníci společnosti, kteří potřebují spustit sestavy nebo uživatel může být analytik s adhoc dotaz. Vytvoření třídění pracovního vytížení přiřadit důležitost dotazu.  Níže uvedené příklady používají novou [syntaxi klasifikátoru úlohy](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) k vytvoření dvou třídění. `Membername`může být jeden uživatel nebo skupina. Klasifikace jednotlivých uživatelů mají přednost před klasifikacemi rolí. Chcete-li najít stávající uživatele datového skladu, spusťte:

```sql
Select name from sys.sysusers
```

Chcete-li vytvořit třídění úlohy, pro uživatele s vyšší důležitostí spustit:

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  above_normal);  

```

Chcete-li vytvořit třídění úlohy pro uživatele s chod adhoc dotazy s nižší důležitostí spustit:  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier  
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name'  
         ,IMPORTANCE     =  below_normal);  
```

## <a name="next-steps"></a>Další kroky

- Další informace o správě pracovního vytížení najdete [v tématu Klasifikace pracovního vytížení](sql-data-warehouse-workload-classification.md)
- Další informace o důležitosti naleznete v [tématu Důležitost pracovního vytížení](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [Přejít na Spravovat a sledovat důležitost pracovního vytížení](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
