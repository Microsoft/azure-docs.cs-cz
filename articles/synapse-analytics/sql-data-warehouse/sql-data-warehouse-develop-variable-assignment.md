---
title: Přiřazení proměnných
description: Tipy pro přiřazení proměnných T-SQL v Azure SQL Data Warehouse pro vývoj řešení.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 0adcd9bdf92b7ec649b7d91ca0e655fc006b3549
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351665"
---
# <a name="assigning-variables-in-azure-sql-data-warehouse"></a>Přiřazení proměnných v datovém skladu Azure SQL

Tipy pro přiřazení proměnných T-SQL v Azure SQL Data Warehouse pro vývoj řešení.

## <a name="setting-variables-with-declare"></a>Nastavení proměnných pomocí příkazu DECLARE

Proměnné v datovém skladu SQL `DECLARE` jsou `SET` nastaveny pomocí příkazu nebo příkazu. Inicializace proměnných pomocí funkce DECLARE je jedním z nejflexibilnějších způsobů nastavení hodnoty proměnné v datovém skladu SQL.

```sql
DECLARE @v  int = 0
;
```

Declare můžete také použít k nastavení více než jednu proměnnou najednou. Select nebo UPDATE nelze použít k následujícím akcím:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

Nelze inicializovat a použít proměnnou ve stejném příkazu DECLARE. Pro ilustraci bodu **následující** příklad @p1 není povolen, protože je inicializován a použit ve stejném příkazu DECLARE. Následující příklad uvádí chybu.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Nastavení hodnot pomocí sady

SET je běžná metoda pro nastavení jedné proměnné.

Následující příkazy jsou všechny platné způsoby nastavení proměnné s SET:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Pomocí sady lze nastavit pouze jednu proměnnou najednou. Obsluha sloučenin je však přípustná.

## <a name="limitations"></a>Omezení

Nelze použít update pro přiřazení proměnných.

## <a name="next-steps"></a>Další kroky

Další tipy pro vývoj najdete v [tématu přehled vývoje](sql-data-warehouse-overview-develop.md).
