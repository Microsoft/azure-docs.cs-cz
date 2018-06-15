---
title: Pomocí popisků na nástrojích dotazy v SQL Data Warehouse | Microsoft Docs
description: Tipy pro používání popisky na nástrojích dotazy v Azure SQL Data Warehouse na vývoj řešení.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 22737faa146d83f1f31489125dee4146c7d11ac1
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31524240"
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>Použití popisků na nástrojích dotazy v Azure SQL Data Warehouse
Tipy pro používání popisky na nástrojích dotazy v Azure SQL Data Warehouse na vývoj řešení.


## <a name="what-are-labels"></a>Jaké jsou popisky?
SQL Data Warehouse podporuje koncept názvem popisky dotazu. Před přechodem do jakékoli hloubku, podíváme se na příklad:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Poslední řádek značky řetězec popisek Moje dotazu pro dotaz. Tato značka je zvláště užitečné, protože popisek dotaz může prostřednictvím zobrazení dynamické správy. Dotazování pro popisky poskytuje mechanismus pro vyhledání problém dotazy a pomáhá identifikovat průběh prostřednictvím ELT spustit.

Dobrý zásady vytváření názvů skutečně pomáhá. Například popisek počínaje projektu, postup, příkaz nebo komentář pomáhá k jednoznačné identifikaci dotaz mezi všechny kód ve správě zdrojového kódu.

Následující dotaz používá zobrazení dynamické správy Pokud chcete vyhledávat podle popisku.

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Je nutné uvést hranaté závorky a dvojité uvozovky kolem popisek word při dotazování. Popisek je vyhrazené slovo a způsobuje chybu, když není oddělené. 
> 
> 

## <a name="next-steps"></a>Další postup
Další tipy pro vývoj, najdete v části [přehled vývoje](sql-data-warehouse-overview-develop.md).


