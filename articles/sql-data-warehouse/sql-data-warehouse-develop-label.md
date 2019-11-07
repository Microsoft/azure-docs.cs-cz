---
title: Použití popisků k instrumentaci dotazů
description: Tipy pro použití popisků k instrumentaci dotazů v Azure SQL Data Warehouse pro vývoj řešení.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 4ca0b3564418aafe774158057bc3efd541f71f66
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692837"
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>Použití popisků k instrumentaci dotazů v Azure SQL Data Warehouse
Tipy pro použití popisků k instrumentaci dotazů v Azure SQL Data Warehouse pro vývoj řešení.


## <a name="what-are-labels"></a>Co jsou popisky?
SQL Data Warehouse podporuje koncept nazvaný popisky dotazů. Než se pustíte do jakékoli hloubky, Podívejme se na příklad:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Poslední řádek Taguje řetězec "jmenovka dotazu" do dotazu. Tato značka je zvláště užitečná, protože popisek je schopný dotazovat se prostřednictvím zobrazení dynamické správy. Dotazování na popisky poskytuje mechanismus pro vyhledání problematických dotazů a pomáhá identifikovat průběh prostřednictvím ELT spuštění.

Dobrá konvence vytváření názvů vám pomůže. Například spuštění popisku pomocí projektu, procedury, příkazu nebo komentáře pomáhá jednoznačně identifikovat dotaz ze všech kódů ve správě zdrojového kódu.

Následující dotaz používá zobrazení dynamické správy k hledání podle popisku.

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Při dotazování je nutné při dotazování kolem popisku slova vložit hranaté závorky nebo dvojité uvozovky. Popisek je rezervované slovo a při neomezeném důsledku dojde k chybě. 
> 
> 

## <a name="next-steps"></a>Další kroky
Další tipy pro vývoj najdete v tématu [Přehled vývoje](sql-data-warehouse-overview-develop.md).


