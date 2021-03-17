---
title: Převést třídu prostředků na skupinu úloh
description: Naučte se, jak vytvořit skupinu úloh, která je podobná třídě prostředků ve vyhrazeném fondu SQL.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 1207f4856882d8aa0e6d1e41712071536bfecf29
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98728552"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Převod tříd prostředků na skupiny úloh

Skupiny úloh poskytují mechanismus pro izolaci a zahrnutí systémových prostředků.  Skupiny úloh navíc umožňují nastavit pravidla spouštění pro požadavky, které jsou v nich spuštěné.  Pravidlo pro spuštění dotazu s časovým limitem umožňuje, aby se dotazy navedly zrušit bez zásahu uživatele.  Tento článek vysvětluje, jak převzít existující třídu prostředků a vytvořit skupinu úloh s podobnou konfigurací.  Kromě toho je přidáno volitelné pravidlo časového limitu dotazu.

> [!NOTE]
> Pokyny k používání skupin úloh a tříd prostředků najdete v části [kombinování přiřazení tříd prostředků s klasifikátory](sql-data-warehouse-workload-classification.md#mixing-resource-class-assignments-with-classifiers) v dokumentu konceptu [klasifikace úloh](sql-data-warehouse-workload-classification.md) .

## <a name="understanding-the-existing-resource-class-configuration"></a>Porozumění existující konfiguraci třídy prostředků

Skupiny úloh vyžadují parametr s názvem `REQUEST_MIN_RESOURCE_GRANT_PERCENT` , který určuje procentuální hodnotu celkových systémových prostředků přidělených na požadavek.  Přidělení prostředků se provádí pro [třídy prostředků](resource-classes-for-workload-management.md#what-are-resource-classes) přidělením slotů souběžnosti.  K určení hodnoty, kterou chcete zadat `REQUEST_MIN_RESOURCE_GRANT_PERCENT` , použijte sys.dm_workload_management_workload_groups_stats <link tbd> DMV.  Například následující dotaz dotazu vrátí hodnotu, kterou lze použít pro `REQUEST_MIN_RESOURCE_GRANT_PERCENT` parametr k vytvoření skupiny úloh, podobně jako staticrc40.

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> Skupiny úloh fungují na základě procentuálních hodnot celkových systémových prostředků.  

Vzhledem k tomu, že skupiny úloh fungují na základě procenta celkových systémových prostředků, při horizontálním navýšení a snížení kapacity je procento prostředků přidělených ke statickým třídám prostředků relativních ke změnám celkových systémových prostředků.  Například staticrc40 na DW1000c přiděluje 19,2% celkových systémových prostředků.  V DW2000c se přidělí 9,6%.  Tento model je podobný, pokud chcete škálovat na souběžnost a přidělit více prostředků na požadavek.

## <a name="create-workload-group"></a>Vytvořit skupinu úloh

Známou je `REQUEST_MIN_RESOURCE_GRANT_PERCENT` , že <link> k vytvoření skupiny úloh můžete použít SYNTAXI vytvořit skupinu úloh.  Volitelně můžete zadat hodnotu `MIN_PERCENTAGE_RESOURCE` , která je větší než nula pro izolaci prostředků pro skupinu úloh.  Volitelně můžete také zadat `CAP_PERCENTAGE_RESOURCE` méně než 100 a omezit tak množství prostředků, které může skupina zatížení spotřebovat.  

Použití mediumrc jako základu pro příklad, následující kód nastaví, `MIN_PERCENTAGE_RESOURCE` aby vyhradí 10% systémových prostředků `wgDataLoads` a zaručila tak, že jeden dotaz bude možné spustit všechny časy.  Navíc `CAP_PERCENTAGE_RESOURCE` je nastaveno na 40% a omezí tuto skupinu úloh na čtyři souběžné požadavky.  Když nastavíte `QUERY_EXECUTION_TIMEOUT_SEC` parametr na 3600, všechny dotazy, které se spustí po dobu více než 1 hodiny, se automaticky zruší.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 10
 ,MIN_PERCENTAGE_RESOURCE = 10
 ,CAP_PERCENTAGE_RESOURCE = 40
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>Vytvoření klasifikátoru

Dříve bylo mapování dotazů na třídy prostředků provedeno pomocí [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  Chcete-li dosáhnout stejných funkcí a požadavků na mapování na skupiny úloh, použijte syntaxi [vytvořit třídění úloh](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  Použití sp_addrolemember povoluje mapování prostředků na požadavek na základě přihlášení.  Klasifikátor nabízí další možnosti kromě přihlášení, například:
    - label
    - relace
    - čas, kdy níže uvedený příklad přiřadí dotazy z `AdfLogin` přihlášení, které mají také [popisek možnost](sql-data-warehouse-develop-label.md)  nastavený na `factloads` skupinu úloh `wgDataLoads` vytvořenou výše.

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

- [Izolace úloh](sql-data-warehouse-workload-isolation.md)
- [Postup vytvoření skupiny úloh](quickstart-configure-workload-isolation-tsql.md)
- [Vytvoření KLASIFIKÁTORu úloh (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql??view=azure-sqldw-latest&preserve-view=true)
- [Vytvoření skupiny úloh (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest&preserve-view=true)
