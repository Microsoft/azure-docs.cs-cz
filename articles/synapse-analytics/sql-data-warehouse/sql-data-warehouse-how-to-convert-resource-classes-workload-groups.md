---
title: Převést třídu prostředků na skupinu pracovního vytížení
description: Zjistěte, jak vytvořit skupinu úloh, která je podobná třídě prostředků v Azure SQL Data Warehouse.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d20a811270b89772ab75bc298544a549caa01041
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350449"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Převést třídy prostředků na skupiny úloh
Skupiny úloh poskytují mechanismus pro izolaci a obsahují systémové prostředky.  Kromě toho skupiny úloh umožňují nastavit pravidla spuštění pro požadavky spuštěné v nich.  Pravidlo časového limitu dotazu umožňuje zrušit dotazy na útěk bez zásahu uživatele.  Tento článek vysvětluje, jak přijmout existující třídu prostředků a vytvořit skupinu úloh s podobnou konfigurací.  Kromě toho je přidáno volitelné pravidlo časového limitu dotazu.

## <a name="understanding-the-existing-resource-class-configuration"></a>Principy konfigurace existující třídy prostředků
Skupiny úloh vyžadují `REQUEST_MIN_RESOURCE_GRANT_PERCENT` nazvaný parametr, který určuje procento celkových systémových prostředků přidělených na požadavek.  Přidělení prostředků se provádí pro [třídy prostředků](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#what-are-resource-classes) přidělením slotů souběžnosti.  Chcete-li určit hodnotu, která má být specifikována pro `REQUEST_MIN_RESOURCE_GRANT_PERCENT`, použijte sys.dm_workload_management_workload_groups_stats <link tbd> DMV.  Například níže dotaz dotaz vrátí hodnotu, která `REQUEST_MIN_RESOURCE_GRANT_PERCENT` lze použít pro parametr k vytvoření skupiny pracovního vytížení podobné staticrc40.   

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> Skupiny úloh pracují na základě procenta celkových systémových prostředků.  

Vzhledem k tomu, že skupiny pracovních vytížek pracují na základě procenta celkových systémových prostředků, při škálování nahoru a dolů se procento zdrojů přidělených statickým třídám prostředků vzhledem k celkovým systémovým prostředkům změní.  Například staticrc40 na DW1000c přiděluje 9,6 % celkových systémových prostředků.  Na DW2000c je přiděleno 19,2 %.  Tento model je podobný, pokud chcete vertikálně navýšit kapacitu pro souběžnost versus přidělení více prostředků na požadavek.   

## <a name="create-workload-group"></a>Vytvořit skupinu pracovních vytížek
Se známým `REQUEST_MIN_RESOURCE_GRANT_PERCENT`můžete použít syntaxi <link> CREATE WORKLOAD GROUP k vytvoření skupiny pracovního vytížení.  Volitelně můžete zadat, `MIN_PERCENTAGE_RESOURCE` která je větší než nula izolovat prostředky pro skupinu pracovního vytížení.  Můžete také volitelně `CAP_PERCENTAGE_RESOURCE` zadat méně než 100 omezit množství prostředků, které skupina pracovního vytížení může spotřebovat.  

Níže uvedený příklad `MIN_PERCENTAGE_RESOURCE` nastaví vyhradit 9,6 % `wgDataLoads` systémových prostředků a zaručuje, že jeden dotaz bude moci spustit po celou dobu.  Navíc `CAP_PERCENTAGE_RESOURCE` je nastavena na 38,4 % a omezuje tuto skupinu úloh na čtyři souběžné požadavky.  Nastavením `QUERY_EXECUTION_TIMEOUT_SEC` parametru 3600 bude automaticky zrušen jakýkoli dotaz, který běží déle než 1 hodinu.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>Vytvoření třídění
Dříve mapování dotazů na třídy prostředků bylo provedeno s [sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class).  Chcete-li dosáhnout stejné funkce a mapovat požadavky na skupiny úloh, použijte [syntaxi CREATE WORKLOAD CLASSIFIER.](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql)  Použití sp_addrolemember povoleno pouze mapování prostředků na požadavek na základě přihlášení.  Třídění poskytuje další možnosti kromě přihlášení, například:
    - label
    - relace
    - Čas Níže uvedený příklad přiřadí `AdfLogin` dotazy z přihlášení, které `factloads` mají také `wgDataLoads` [OPTION LABEL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-develop-label) nastavena na skupinu pracovního vytížení vytvořené výše.

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```
## <a name="test-with-a-sample-query"></a>Testování pomocí ukázkového dotazu
Níže je ukázkový dotaz a dotaz DMV, který zajišťuje správné nakonfigurování skupiny úloh a třídění.

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

Připojení tbd propojení úloh tbd Jak vytvořit propojení skupiny úloh tbd