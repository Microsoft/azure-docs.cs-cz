---
title: 'Rychlý Start: Konfigurace izolace úloh – T-SQL'
description: Pomocí T-SQL nakonfigurujte izolaci úloh.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/27/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: ae21368269a2c098b4e35bf83b8c9fdf2749a297
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460556"
---
# <a name="quickstart-configure-workload-isolation-in-a-dedicated-sql-pool-using-t-sql"></a>Rychlý Start: Konfigurace izolace úloh ve vyhrazeném fondu SQL pomocí T-SQL

V tomto rychlém startu budete rychle vytvořit skupinu úloh a třídění pro rezervaci prostředků pro načtení dat. Skupina úloh přidělí k načteným datům 20% systémových prostředků.  Klasifikátor úlohy přiřadí požadavky do skupiny úloh načíst data.  Díky 20% izolaci pro zatížení dat jsou zaručeny prostředky k Slaí.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

> [!NOTE]
> Vytvoření instance SQL synapse v Azure synapse Analytics může mít za následek novou fakturovatelnou službu.  Další informace najdete v tématu [ceny služby Azure synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Předpoklady

V tomto rychlém startu se předpokládá, že už máte v Azure synapse instanci SQL synapse a máte oprávnění k řízení databáze. Pokud ho potřebujete vytvořit, použijte [Vytvoření a připojení – portál](create-data-warehouse-portal.md) pro vytvoření vyhrazeného fondu SQL s názvem **mySampleDataWarehouse**.

## <a name="create-login-for-dataloads"></a>Vytvoření přihlašovacích údajů pro dataloades

Vytvořte přihlašovací jméno SQL Server ověřování v `master` databázi pomocí příkazu [Create Login](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) for ' ELTLogin'.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Vytvořit uživatele

[Vytvoření uživatele](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), "ELTLogin", v mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>Vytvoření skupiny úloh

Vytvořte [skupinu úloh](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) pro dataloades s izolací 20%.

```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5)
;
```

## <a name="create-a-workload-classifier"></a>Vytvoření klasifikátoru úloh

Vytvořte [klasifikátor úloh](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) pro mapování ELTLogin na skupinu úloh dataloads.

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'DataLoads'
      ,MEMBERNAME = 'ELTLogin')
;
```

## <a name="view-existing-workload-groups-and-classifiers-and-run-time-values"></a>Zobrazení existujících skupin úloh a klasifikátorů a hodnot runtime

```sql
--Workload groups
SELECT * FROM
sys.workload_management_workload_groups

--Workload classifiers
SELECT * FROM
sys.workload_management_workload_classifiers

--Run-time values
SELECT * FROM
sys.dm_workload_management_workload_groups_stats
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

```sql
DROP WORKLOAD CLASSIFIER [wgcELTLogin]
DROP WORKLOAD GROUP [DataLoads]
DROP USER [ELTLogin]
;
```

Účtují se vám poplatky za jednotky datového skladu a data uložená ve vašem vyhrazeném fondu SQL. Výpočetní prostředky a prostředky úložiště se účtují odděleně.

- Pokud chcete uchovávat data v úložišti, můžete pozastavit výpočetní prostředky, když nepoužíváte vyhrazený fond SQL. Když pozastavíte výpočetní prostředky, bude se vám účtovat jenom úložiště dat. Až budete připraveni pracovat s daty, obnovte výpočetní výkon.
- Pokud chcete odebrat budoucí poplatky, můžete odstranit vyhrazený fond SQL.

## <a name="next-steps"></a>Další kroky

- Nyní jste vytvořili skupinu úloh. Spusťte několik dotazů jako ELTLogin, abyste viděli, jak fungují. V tématu [Sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) můžete zobrazit dotazy a přiřazenou skupinu úloh.
- Další informace o správě úloh synapse SQL najdete v tématu věnovaném [správě úloh](sql-data-warehouse-workload-management.md) a [izolaci úloh](sql-data-warehouse-workload-isolation.md).
