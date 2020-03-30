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
ms.openlocfilehash: 85fa6a989ae1a6214392914ffd46cd12af27a8d2
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350478"
---
# <a name="configure-workload-importance-in-azure-synapse-analytics"></a>Konfigurace důležitosti úlohy v Azure Synapse Analytics

Nastavení důležitosti v SQL Analytics pro Azure Synapse umožňuje ovlivnit plánování dotazů. Dotazy s vyšší důležitostí budou naplánovány ke spuštění před dotazy s nižší důležitostí. Chcete-li přiřadit důležitost dotazům, musíte vytvořit třídění úlohy.

## <a name="create-a-workload-classifier-with-importance"></a>Vytvoření třídění pracovního vytížení s důležitostí

Často ve scénáři datového skladu máte uživatele, kteří potřebují jejich dotazy ke spuštění rychle.  Uživatel může být vedoucí pracovníci společnosti, kteří potřebují spustit sestavy nebo uživatel může být analytik s adhoc dotaz. Vytvoření třídění pracovního vytížení přiřadit důležitost dotazu.  Níže uvedené příklady používají novou [syntaxi klasifikátoru úlohy](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) k vytvoření dvou třídění.  Název_člena může být jeden uživatel nebo skupina. Klasifikace jednotlivých uživatelů mají přednost před klasifikacemi rolí. Chcete-li najít stávající uživatele datového skladu, spusťte:

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
