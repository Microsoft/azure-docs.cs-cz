---
title: Přiřazení proměnných k synapse SQL
description: V tomto článku najdete tipy pro přiřazování proměnných T-SQL pomocí synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 91e251c8274ad85fcb0b3a65a3bdbcc660ceba00
ms.sourcegitcommit: 51718f41d36192b9722e278237617f01da1b9b4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101006"
---
# <a name="assigning-variables-with-synapse-sql"></a>Přiřazení proměnných k synapse SQL

V tomto článku najdete tipy pro přiřazování proměnných T-SQL pomocí synapse SQL.

## <a name="setting-variables-with-declare"></a>Nastavení proměnných pomocí deklarace

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

Další tipy pro vývoj najdete v článku [Přehled vývoje synapse SQL](develop-overview.md) .
