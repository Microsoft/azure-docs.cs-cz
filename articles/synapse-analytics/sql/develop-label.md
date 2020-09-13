---
title: Použití popisků dotazů v synapse SQL
description: V tomto článku jsou uvedené základní tipy pro používání popisků dotazů v synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 596b18332fc490f72ad185699fc59bf4521eca69
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032176"
---
# <a name="use-query-labels-in-synapse-sql"></a>Použití popisků dotazů v synapse SQL
V tomto článku jsou uvedené základní tipy pro používání popisků dotazů v synapse SQL.

> [!NOTE]
> SQL na vyžádání (Preview) nepodporuje dotazy na označování.

## <a name="what-are-query-labels"></a>Co jsou popisky dotazů
Fond SQL podporuje koncept nazvaný popisky dotazů. Než se pustíte do jakékoli hloubky, Podívejme se na příklad:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Poslední řádek Taguje řetězec "jmenovka dotazu" do dotazu. Tato značka je zvláště užitečná, protože popisek je schopný dotazovat se prostřednictvím zobrazení dynamické správy. Dotazování na popisky poskytuje mechanismus pro vyhledání problematických dotazů a pomůže vám identifikovat průběh prostřednictvím ELT spuštění.

Dobrá konvence pojmenování je nejužitečnější. Například spuštění popisku pomocí projektu, procedury, příkazu nebo komentáře jednoznačně identifikuje dotaz ze všech kódů ve správě zdrojového kódu.

Následující dotaz používá zobrazení dynamické správy k hledání podle popisku:

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
Další tipy pro vývoj najdete v tématu [Přehled vývoje](develop-overview.md).


