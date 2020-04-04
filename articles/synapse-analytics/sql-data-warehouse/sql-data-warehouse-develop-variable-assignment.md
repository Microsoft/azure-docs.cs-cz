---
title: Přiřazení proměnných
description: V tomto článku najdete základní tipy pro přiřazení proměnných T-SQL ve fondu SQL.
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
ms.openlocfilehash: 2dcf706ea59657abc2718a69e59191604dc2849d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633410"
---
# <a name="assign-variables-in-synapse-sql-pool"></a>Přiřazení proměnných ve fondu Synapse SQL

V tomto článku najdete základní tipy pro přiřazení proměnných T-SQL ve fondu SQL.

## <a name="set-variables-with-declare"></a>Nastavení proměnných pomocí příkazu DECLARE

Proměnné ve fondu SQL jsou `DECLARE` nastaveny `SET` pomocí příkazu nebo příkazu. Inicializace proměnných pomocí funkce DECLARE je jedním z nejflexibilnějších způsobů nastavení hodnoty proměnné ve fondu SQL.

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

Nelze inicializovat a použít proměnnou ve stejném příkazu DECLARE. Pro ilustraci bodu **následující** příklad @p1 není povolen, protože je inicializován a použit ve stejném příkazu DECLARE. V následujícím příkladu je uvedena chyba:

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="set-values-with-set"></a>Nastavení hodnot pomocí sady

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
