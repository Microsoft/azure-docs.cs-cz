---
title: Použití popisků dotazů v synapse SQL
description: Součástí tohoto článku jsou základní tipy pro použití popisků dotazů v Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 47b476cbc6997ca5ec63968bdc269e2273662100
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430029"
---
# <a name="use-query-labels-in-synapse-sql"></a>Použití popisků dotazů v synapse SQL
Součástí tohoto článku jsou základní tipy pro použití popisků dotazů v Synapse SQL.

> [!NOTE]
> SQL na vyžádání (náhled) nepodporuje dotazy označování.

## <a name="what-are-query-labels"></a>Co jsou popisky dotazů
Fond SQL podporuje koncept nazývaný popisky dotazů. Než se vydáte do jakékoli hloubky, podívejme se na příklad:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Poslední řádek označí řetězec Popisek mého dotazu na dotaz. Tato značka je zvláště užitečné, protože popisek je dotazovat prostřednictvím DMVs. Dotazování na popisky poskytuje mechanismus pro vyhledání problémových dotazů a pomáhá identifikovat průběh prostřednictvím spuštění ELT.

Dobrá konvence pojmenování jsou velmi užitečné. Například spuštění popisku s PROJECT, PROCEDURE, STATEMENT nebo COMMENT jednoznačně identifikuje dotaz mezi veškerý kód ve zdrojovém kódu.

Následující dotaz používá dynamické zobrazení správy k vyhledávání podle popisku:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Při dotazování je nezbytné umístit čtvercové závorky nebo dvojité uvozovky kolem popisku slova. Popisek je vyhrazené slovo a způsobí chybu, pokud není oddělen. 
> 
> 

## <a name="next-steps"></a>Další kroky
Další tipy pro vývoj najdete v [tématu přehled vývoje](develop-overview.md).


