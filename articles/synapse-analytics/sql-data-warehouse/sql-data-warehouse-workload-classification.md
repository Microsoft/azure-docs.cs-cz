---
title: Klasifikace úloh
description: Pokyny pro použití klasifikace ke správě souběžnosti, důležitosti a výpočetních prostředků pro dotazy v Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: e7aa0c402878c994aabe4e12d811a99e300d7e67
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743648"
---
# <a name="azure-synapse-analytics-workload-classification"></a>Klasifikace pracovního vytížení Azure Synapse Analytics

Tento článek vysvětluje proces klasifikace úloh při přiřazování skupiny úloh a důležitost příchozích požadavků s fondy Synapse SQL v Azure Synapse.

## <a name="classification"></a>Classification

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

Klasifikace správy úloh umožňuje použití zásad pracovního vytížení u požadavků prostřednictvím přiřazení [tříd prostředků](resource-classes-for-workload-management.md#what-are-resource-classes) a [důležitosti](sql-data-warehouse-workload-importance.md).

I když existuje mnoho způsobů, jak klasifikovat úlohy ukládání dat, nejjednodušší a nejběžnější klasifikace je zatížení a dotaz. Data načítáte pomocí příkazů insert, update a delete.  Dotaz na data pomocí výběru. Řešení datového skladu bude mít často zásady zatížení pro aktivitu zatížení, jako je například přiřazení vyšší třídy prostředků s více prostředky. Různé zásady pracovního vytížení by se mohly vztahovat na dotazy, jako je například nižší důležitost ve srovnání s aktivitami zatížení.

Můžete také podřadit zatížení a dotaz úlohy. Podklasifikace poskytuje větší kontrolu nad vašimi úlohami. Úlohy dotazů se mohou například skládat z aktualizací krychle, dotazů řídicího panelu nebo dotazů ad hoc. Každý z těchto úloh dotazu můžete klasifikovat s různými třídami prostředků nebo nastavenídůležitosti. Zatížení může také těžit z podklasifikace. Velké transformace lze přiřadit k větší třídy prostředků. Vyšší důležitost lze použít k zajištění klíčových dat o prodeji je zavaděč před data počasí nebo sociální datové kanály.

Ne všechny příkazy jsou klasifikovány jako nevyžadují prostředky nebo potřebují význam ovlivnit provádění.  Příkazy DBCC, BEGIN, COMMIT a ROLLBACK TRANSACTION nejsou klasifikovány.

## <a name="classification-process"></a>Proces klasifikace

Klasifikace pro fond Synapse SQL v Azure Synapse je dosaženo dnes přiřazením uživatelů k roli, která má odpovídající třídu prostředků přiřazenou pomocí [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). Schopnost charakterizovat požadavky mimo přihlášení do třídy prostředků je omezena s touto možností. Bohatší metoda pro klasifikaci je nyní k dispozici se syntaxí [CREATE WORKLOAD CLASSIFIER.](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  Pomocí této syntaxe mohou uživatelé fondu SQL synapse přiřadit důležitost a `workload_group` kolik systémových prostředků je přiřazeno požadavku prostřednictvím parametru.

> [!NOTE]
> Klasifikace se vyhodnocuje na základě požadavku. Více požadavků v jedné relaci lze klasifikovat odlišně.

## <a name="classification-weighting"></a>Klasifikační vážení

V rámci procesu klasifikace je vážení na místě k určení, která skupina pracovního vytížení je přiřazena.  Váha probíhá takto:

|Parametr třídění |Hmotnost   |
|---------------------|---------|
|NÁZEV_ČLENA:UŽIVATEL      |64       |
|NÁZEV_ČLENA:ROLE      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

Parametr `membername` je povinný.  Pokud je však zadaný název člena uživatelem databáze namísto databázové role, je váha pro uživatele vyšší a proto je vybrán třídění.

Pokud je uživatel členem více rolí s různými třídami prostředků přiřazenými nebo odpovídajícími ve více klasifikátorech, je uživateli přiděleno přiřazení nejvyšší třídy prostředků.  Toto chování je konzistentní s existující chování přiřazení třídy prostředků.

## <a name="system-classifiers"></a>Klasifikátory systému

Klasifikace pracovního vytížení má klasifikátory zatížení systému. Klasifikátory systému mapují existující členství ve třídě prostředků na přidělení prostředků třídy prostředků s normální důležitostí. Klasifikátory systému nelze vynechat. Chcete-li zobrazit klasifikátory systému, můžete spustit následující dotaz:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>Míchání přiřazení tříd zdrojů s klasifikátory

Klasifikátory systému vytvořené vaším jménem poskytují snadnou cestu k migraci do klasifikace pracovního vytížení. Pomocí mapování rolí třídy prostředků s prioritou klasifikace může vést k chybné klasifikaci při zahájení vytváření nových klasifikátorů s důležitostí.

Představte si následující scénář:

- Existující datový sklad má databázového uživatele DBAUser přiřazeného k roli třídy prostředků větší. Přiřazení třídy zdrojů bylo provedeno s sp_addrolemember.
- Datový sklad je teď aktualizován pomocí správy úloh.
- Chcete-li otestovat novou syntaxi klasifikace, databázová role DBARole (jehož je DBAUser členem), má klasifikátor vytvořený pro ně mapování na mediumrc a vysokou důležitost.
- Když se DBAUser přihlásí a spustí dotaz, dotaz bude přiřazen k largerc. Vzhledem k tomu, že uživatel má přednost před členstvím v roli.

Chcete-li zjednodušit řešení potíží s chybnou klasifikací, doporučujeme odebrat mapování rolí třídy prostředků při vytváření klasifikátorů úloh.  Níže uvedený kód vrátí existující členství v roli třídy prostředků.  Spusťte [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) pro každý název člena vrácený z odpovídající třídy prostředků.

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember '[Resource Class]', membername
```

## <a name="next-steps"></a>Další kroky

- Další informace o vytvoření třídění naleznete [v tématu CREATE WORKLOAD CLASSIFIER (Transact-SQL).](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  
- Podívejte se na úvodní příručku o tom, jak vytvořit třídění [úlohy Vytvoření třídění pracovního vytížení](quickstart-create-a-workload-classifier-tsql.md).
- Podívejte se na články s postupy [pro konfiguraci důležitosti pracovního vytížení](sql-data-warehouse-how-to-configure-workload-importance.md) a jak [spravovat a monitorovat správu úloh](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Viz [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) pro zobrazení dotazů a přiřazený význam.
