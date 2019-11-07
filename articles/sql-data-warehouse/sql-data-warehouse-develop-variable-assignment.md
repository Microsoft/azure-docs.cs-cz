---
title: Přiřadit proměnné
description: Tipy pro přiřazování proměnných T-SQL v Azure SQL Data Warehouse pro vývoj řešení.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 1ae5285a8d1cf6fa391c082d0196b213e6b6a9c5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692757"
---
# <a name="assigning-variables-in-azure-sql-data-warehouse"></a>Přiřazování proměnných v Azure SQL Data Warehouse

Tipy pro přiřazování proměnných T-SQL v Azure SQL Data Warehouse pro vývoj řešení.

## <a name="setting-variables-with-declare"></a>Nastavení proměnných pomocí deklarace

Proměnné v SQL Data Warehouse jsou nastaveny pomocí příkazu `DECLARE` nebo příkazu `SET`. Inicializace proměnných pomocí příkazu DECLARE je jedním z nejpružnější způsobů, jak nastavit hodnotu proměnné v SQL Data Warehouse.

```sql
DECLARE @v  int = 0
;
```

Můžete také použít DEKLARaci k nastavení více než jedné proměnné v jednom okamžiku. PŘÍKAZ SELECT nebo UPDATE nelze použít k následujícím akcím:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Nelze inicializovat a používat proměnnou v rámci stejného příkazu DECLARE. Pro ilustraci tohoto bodu **není povolen následující** příklad, protože @p1 je inicializován a použit ve stejném příkazu Declare. Následující příklad obsahuje chybu.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Nastavení hodnot pomocí SET

SET je společná metoda pro nastavení jedné proměnné.

Následující příkazy jsou platné způsoby, jak nastavit proměnnou pomocí SET:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Současně lze nastavit pouze jednu proměnnou. Jsou však přípustné složené operátory.

## <a name="limitations"></a>Omezení

Nejde použít UPDATE pro přiřazení proměnné.

## <a name="next-steps"></a>Další kroky

Další tipy pro vývoj najdete v tématu [Přehled vývoje](sql-data-warehouse-overview-develop.md).
