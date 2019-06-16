---
title: Používání popisků do nástroje dotazů v SQL Data Warehouse | Dokumentace Microsoftu
description: Tipy pro používání popisků do nástroje dotazů ve službě Azure SQL Data Warehouse pro vývoj řešení.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 5c53fc3594d02c92ea6a238f89417e31dad4818c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65861790"
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>Použití popisků k instrumentaci dotazů ve službě Azure SQL Data Warehouse
Tipy pro používání popisků do nástroje dotazů ve službě Azure SQL Data Warehouse pro vývoj řešení.


## <a name="what-are-labels"></a>Co jsou popisky?
SQL Data Warehouse podporuje koncept volá dotaz popisky. Před přechodem do libovolné hloubky, Pojďme se podívat na příklad:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Poslední řádek značky řetězec popisek složky dotazu, který má dotaz. Tato značka je zvláště užitečné, protože popisek je dotaz může prostřednictvím zobrazení dynamické správy. Dotazování na popisky poskytuje mechanismus pro vyhledání problémových dotazů a pomáhají identifikovat probíhá prostřednictvím ELT spustit.

Pomůžete dobré zásady vytváření názvů. Například spuštění popisek s PROJEKTEM, PROCEDURY, příkaz nebo KOMENTÁŘE pomáhají k jednoznačné identifikaci dotazu mezi všechny kódu ve správě zdrojového kódu.

Následující dotaz používá zobrazení dynamické správy Pokud chcete vyhledávat podle popisku.

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Je nutné umístit hranaté závorky nebo dvojité uvozovky kolem popisek word při dotazování. Popisek je vyhrazené slovo a způsobí chybu, pokud není s oddělovači. 
> 
> 

## <a name="next-steps"></a>Další postup
Další tipy pro vývoj najdete v části [přehled vývoje](sql-data-warehouse-overview-develop.md).


