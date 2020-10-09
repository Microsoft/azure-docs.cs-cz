---
title: Přiřadit proměnné
description: V tomto článku najdete základní tipy pro přiřazování proměnných T-SQL ve fondu SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 0cbadc10fb817c70bd259397c840aae68abc2d54
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85213325"
---
# <a name="assign-variables-in-synapse-sql-pool"></a>Přiřazení proměnných ve fondu SQL synapse

V tomto článku najdete základní tipy pro přiřazování proměnných T-SQL ve fondu SQL.

## <a name="set-variables-with-declare"></a>Nastavení proměnných pomocí příkazu DECLARE

Proměnné ve fondu SQL jsou nastaveny pomocí `DECLARE` příkazu nebo `SET` příkazu. Inicializace proměnných pomocí příkazu DECLARE je jedním z nejpružnější způsob, jak nastavit hodnotu proměnné ve fondu SQL.

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

Nelze inicializovat a používat proměnnou v rámci stejného příkazu DECLARE. Pro ilustraci tohoto bodu **není povolen následující** příklad, protože @p1 je inicializován i použit ve stejném příkazu Declare. Například následující příklad obsahuje chybu:

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

Další tipy pro vývoj najdete v tématu [Přehled vývoje](sql-data-warehouse-overview-develop.md).
