---
title: Přiřazení proměnné ve službě Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Tipy pro přiřazení proměnné jazyka T-SQL ve službě Azure SQL Data Warehouse pro vývoj řešení.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: cf6d63c8cf67e42eed2ca52bfd0d0a3f9b0e10b1
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43302080"
---
# <a name="assigning-variables-in-azure-sql-data-warehouse"></a>Přiřazení proměnné ve službě Azure SQL Data Warehouse
Tipy pro přiřazení proměnné jazyka T-SQL ve službě Azure SQL Data Warehouse pro vývoj řešení.

## <a name="setting-variables-with-declare"></a>Nastavení proměnných s DECLARE
Proměnné ve službě SQL Data Warehouse se nastavují pomocí `DECLARE` příkazu nebo `SET` příkazu. Inicializace proměnných s DECLARE, je jedním z nejflexibilnější způsoby, jak nastavit hodnotu proměnné ve službě SQL Data Warehouse.

```sql
DECLARE @v  int = 0
;
```

Také vám pomůže DECLARE v každém okamžiku nastaven více než jednu proměnnou. Vyberte nebo aktualizaci nelze použít k postupujte takto:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Nelze inicializovat a použití proměnné v jednom příkazu DECLARE. Abychom tento bod ilustrovali v následujícím příkladu je **není** povolená, protože služba @p1 je inicializován i použít ve stejném příkazu DECLARE. Následující příklad vrátí chybu.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Nastavení hodnot SET
Sada je běžnou metodu pro nastavení jedné proměnné.

Následující příkazy jsou všechny platné způsoby, jak nastavit proměnnou pomocí sady:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Jednu proměnnou lze nastavit pouze současně se SADOU. Složené operátory jsou však přípustné.

## <a name="limitations"></a>Omezení
Vyberte nebo aktualizaci nelze použít pro přiřazení proměnné.

## <a name="next-steps"></a>Další postup
Další tipy pro vývoj najdete v části [přehled vývoje](sql-data-warehouse-overview-develop.md).

