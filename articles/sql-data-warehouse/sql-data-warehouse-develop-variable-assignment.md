---
title: Přiřazení proměnné v Azure SQL Data Warehouse | Microsoft Docs
description: Tipy pro přiřazení proměnné T-SQL v Azure SQL Data Warehouse na vývoj řešení.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/12/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 012bc76950f212d69d26607c666e878b22015e70
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
---
# <a name="assigning-variables-in-azure-sql-data-warehouse"></a>Přiřazení proměnné v Azure SQL Data Warehouse
Tipy pro přiřazení proměnné T-SQL v Azure SQL Data Warehouse na vývoj řešení.

## <a name="setting-variables-with-declare"></a>Nastavení proměnné s DECLARE
Jsou nastaveny proměnné v SQL Data Warehouse pomocí `DECLARE` příkaz nebo `SET` příkaz. Inicializace proměnných s DECLARE je jedním z nejpružnější způsobů, jak nastavit hodnotu proměnné v SQL Data Warehouse.

```sql
DECLARE @v  int = 0
;
```

DECLARE můžete také nastavit více než jednu proměnnou najednou. Vyberte nebo aktualizaci nelze použít k následujícím:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Nelze inicializovat a použití proměnné v příkazu DECLARE stejné. Pro ilustraci bod, následující příklad je **není** povolené od @p1 jak inicializovat a použít v příkazu DECLARE stejné. Následující příklad vrátí chybu.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Nastavení hodnot sadou
Sada je běžnou metodu pro nastavení jednu proměnnou.

Následující příkazy jsou všechny platné způsoby, jak nastavit proměnnou sadou:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Najednou můžete sadou nastavit pouze jednu proměnnou. Složené operátory jsou však povolený.

## <a name="limitations"></a>Omezení
Vyberte nebo aktualizaci nelze použít pro přiřazení proměnné.

## <a name="next-steps"></a>Další postup
Další tipy pro vývoj, najdete v části [přehled vývoje](sql-data-warehouse-overview-develop.md).

