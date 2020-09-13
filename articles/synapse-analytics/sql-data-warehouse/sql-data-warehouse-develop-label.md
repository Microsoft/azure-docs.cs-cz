---
title: Použití popisků k instrumentaci dotazů
description: Tipy pro použití popisků k instrumentaci dotazů v synapse fondu SQL pro vývoj řešení
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
ms.openlocfilehash: 91b6dac5fba4bb8dfd8cf4a3bb4e5952f8388bb5
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89459143"
---
# <a name="using-labels-to-instrument-queries-in-synapse-sql-pool"></a>Použití popisků k instrumentaci dotazů v synapse fondu SQL

V tomto článku najdete tipy pro vývoj řešení pomocí popisků pro instrumentaci dotazů ve fondu SQL.

Tipy pro použití popisků k instrumentaci dotazů v Azure synapse Analytics pro vývoj řešení.

## <a name="what-are-labels"></a>Co jsou popisky?

Fond SQL podporuje koncept nazvaný popisky dotazů. Než se pustíte do jakékoli hloubky, Podívejme se na příklad:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Poslední řádek Taguje řetězec "jmenovka dotazu" do dotazu. Tato značka je užitečná, protože popisek je schopný dotazovat se prostřednictvím zobrazení dynamické správy.

Dotazování na popisky poskytuje mechanismus pro vyhledání problematických dotazů a pomáhá identifikovat průběh prostřednictvím ELT spuštění.

Dobrá konvence vytváření názvů vám pomůže. Například spuštění popisku pomocí projektu, procedury, příkazu nebo komentáře jednoznačně identifikuje dotaz ze všech kódů ve správě zdrojového kódu.

Následující dotaz používá zobrazení dynamické správy k hledání podle popisku:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Při dotazování je nutné při dotazování kolem popisku slova vložit hranaté závorky nebo dvojité uvozovky. Popisek je rezervované slovo a při neomezeném důsledku dojde k chybě.

## <a name="next-steps"></a>Další kroky

Další tipy pro vývoj najdete v tématu [Přehled vývoje](sql-data-warehouse-overview-develop.md).
