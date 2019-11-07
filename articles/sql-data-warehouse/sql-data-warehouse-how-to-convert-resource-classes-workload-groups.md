---
title: Převést třídu prostředků na skupinu úloh
description: Naučte se, jak vytvořit skupinu úloh, která je podobná třídě prostředků v Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: workload-management
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5708dccce5f7cdcc33fe6bfca980126cd8b5ee7f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685710"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Převod tříd prostředků na skupiny úloh
Skupiny úloh poskytují mechanismus pro izolaci a zahrnutí systémových prostředků.  Skupiny úloh navíc umožňují nastavit pravidla spouštění pro požadavky, které jsou v nich spuštěné.  Pravidlo pro spuštění dotazu s časovým limitem umožňuje, aby se dotazy navedly zrušit bez zásahu uživatele.  Tento článek vysvětluje, jak převzít existující třídu prostředků a vytvořit skupinu úloh s podobnou konfigurací.  Kromě toho je přidáno volitelné pravidlo časového limitu dotazu.

## <a name="understanding-the-existing-resource-class-configuration"></a>Porozumění existující konfiguraci třídy prostředků
Skupiny úloh vyžadují parametr s názvem `REQUEST_MIN_RESOURCE_GRANT_PERCENT`, který určuje procento celkových systémových prostředků přidělených na požadavek.  Přidělení prostředků se provádí pro [třídy prostředků](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#what-are-resource-classes) přidělením slotů souběžnosti.  Chcete-li určit hodnotu pro `REQUEST_MIN_RESOURCE_GRANT_PERCENT`, použijte sys. DM _workload_management_workload_groups_stats <link tbd> DMV.  Například dotaz uvedený níže vrátí hodnotu, která se dá použít pro `REQUEST_MIN_RESOURCE_GRANT_PERCENT` parametr pro vytvoření skupiny úloh, podobně jako staticrc40.   

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> Skupiny úloh fungují na základě procentuálních hodnot celkových systémových prostředků.  

Vzhledem k tomu, že skupiny úloh fungují na základě procenta celkových systémových prostředků, při horizontálním navýšení a snížení kapacity je procento prostředků přidělených ke statickým třídám prostředků relativních ke změnám celkových systémových prostředků.  Například staticrc40 na DW1000c přiděluje 9,6% celkových systémových prostředků.  V DW2000c se přidělí 19,2%.  Tento model je podobný, pokud chcete škálovat na souběžnost a přidělit více prostředků na požadavek.   

## <a name="create-workload-group"></a>Vytvořit skupinu úloh
Se známým `REQUEST_MIN_RESOURCE_GRANT_PERCENT`můžete k vytvoření skupiny úloh použít syntaxi vytvořit skupinu úloh <link>.  Volitelně můžete zadat `MIN_PERCENTAGE_RESOURCE`, která je větší než nula pro izolaci prostředků pro skupinu úloh.  Volitelně můžete také zadat `CAP_PERCENTAGE_RESOURCE` méně než 100 a omezit tak množství prostředků, které může skupina úloh spotřebovat.  

Následující příklad nastaví `MIN_PERCENTAGE_RESOURCE` k vyhradení 9,6% systémových prostředků, které se mají `wgDataLoads` a zaručuje, že jeden dotaz bude možné spustit všechny časy.  Kromě toho je `CAP_PERCENTAGE_RESOURCE` nastaveno na 38,4% a omezíte tuto skupinu úloh na čtyři souběžné požadavky.  Když nastavíte parametr `QUERY_EXECUTION_TIMEOUT_SEC` na 3600, všechny dotazy, které se spustí po dobu více než 1 hodiny, se automaticky zruší.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>Vytvoření klasifikátoru
Dříve bylo mapování dotazů na třídy prostředků provedeno pomocí [sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class).  Chcete-li dosáhnout stejných funkcí a požadavků na mapování na skupiny úloh, použijte syntaxi [vytvořit třídění úloh](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql) .  Pomocí sp_addrolemember jste povolili mapování prostředků na požadavek na základě přihlášení.  Klasifikátor nabízí další možnosti kromě přihlášení, například:
    - label
    - session
    - čas, kdy níže uvedený příklad přiřadí dotazy z `AdfLogin` přihlašovacích údajů, u kterých je také nastavená [možnost](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-label) `factloads` na skupinu úloh `wgDataLoads` vytvořena výše.

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```
## <a name="test-with-a-sample-query"></a>Testování s ukázkovým dotazem
Níže je ukázkový dotaz a dotaz DMV, aby bylo zajištěno, že je správně nakonfigurována skupina úloh a třídění.

```sql
SELECT SUSER_SNAME() --should be 'AdfLogin'

--change to a valid table AdfLogin has access to
SELECT TOP 10 * 
  FROM nation 
  OPTION (label='factloads')

SELECT request_id, [label], classifier_name, group_name, command 
  FROM sys.dm_pdw_exec_requests 
  WHERE [label] = 'factloads'
  ORDER BY submit_time DESC
```

## <a name="next-steps"></a>Další kroky

Izolaci úloh – odkaz stanoví, jak vytvořit skupinu úloh – propojení je zatbd.