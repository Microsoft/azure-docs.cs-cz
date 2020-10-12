---
title: Přiřazení proměnných k synapse SQL
description: V tomto článku najdete tipy pro přiřazování proměnných T-SQL pomocí synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: abc0a9f6fa6baefc7cc4b29c84ff179f0851dc30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90028912"
---
# <a name="assign-variables-with-synapse-sql"></a>Přiřazení proměnných k synapse SQL

V tomto článku najdete tipy pro přiřazování proměnných T-SQL pomocí synapse SQL.

## <a name="set-variables-with-declare"></a>Nastavení proměnných pomocí příkazu DECLARE

Proměnné v synapse SQL jsou nastaveny pomocí `DECLARE` příkazu nebo `SET` příkazu. Inicializace proměnných pomocí příkazu DECLARE je jedním z nejpružnější způsobů, jak nastavit hodnotu proměnné v synapse SQL.

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

Nelze inicializovat a používat proměnnou v rámci stejného příkazu DECLARE. Pro ilustraci není následující příklad povolen, protože * \@ P1* je inicializováno a použito ve stejném příkazu Declare. Následující příklad obsahuje chybu.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="set-values-with-set"></a>Nastavit hodnoty pomocí SET

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

Další tipy pro vývoj najdete v článku [Přehled vývoje synapse SQL](develop-overview.md) .
