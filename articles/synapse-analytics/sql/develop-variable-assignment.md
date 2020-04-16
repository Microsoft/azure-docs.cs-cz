---
title: Přiřazení proměnných pomocí synapse SQL
description: V tomto článku najdete tipy pro přiřazení t-SQL proměnných s Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: b2a596b71ee7e5f58e01d5bc10b330f6f54a69d2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428664"
---
# <a name="assigning-variables-with-synapse-sql"></a>Přiřazení proměnných pomocí synapse SQL

V tomto článku najdete tipy pro přiřazení t-SQL proměnných s Synapse SQL.

## <a name="setting-variables-with-declare"></a>Nastavení proměnných pomocí příkazu DECLARE

Proměnné v Synapse SQL jsou `DECLARE` nastaveny `SET` pomocí příkazu nebo příkazu. Inicializace proměnných pomocí declare je jedním z nejflexibilnějších způsobů nastavení hodnoty proměnné v synapse SQL.

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

Nelze inicializovat a použít proměnnou ve stejném příkazu DECLARE. Pro ilustraci následující příklad *@p1* není povolen, protože je inicializován a použit ve stejném příkazu DECLARE. Následující příklad uvádí chybu.

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

Další tipy pro vývoj najdete v článku [přehled vývoje Synapse SQL.](develop-overview.md)