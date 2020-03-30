---
title: Použití štítků k přístrojovým dotazům
description: Tipy pro použití popisků k instrumentaci dotazů v Azure SQL Data Warehouse pro vývoj řešení.
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
ms.openlocfilehash: 828e4a406cd0fb12877af44263ab1f338c20850c
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351680"
---
# <a name="using-labels-to-instrument-queries-in-azure-sql-data-warehouse"></a>Použití popisků k instrumentaci dotazů v Azure SQL Data Warehouse
Tipy pro použití popisků k instrumentaci dotazů v Azure SQL Data Warehouse pro vývoj řešení.


## <a name="what-are-labels"></a>Co jsou štítky?
SQL Data Warehouse podporuje koncept nazývaný popisky dotazů. Než se vydáte do jakékoli hloubky, podívejme se na příklad:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Poslední řádek označí řetězec Popisek mého dotazu na dotaz. Tato značka je zvláště užitečné, protože popisek je dotazovat prostřednictvím DMVs. Dotazování na popisky poskytuje mechanismus pro vyhledání problémových dotazů a pomáhá identifikovat průběh prostřednictvím spuštění ELT.

Dobrá konvence pojmenování opravdu pomáhá. Například spuštění popisku s PROJECT, PROCEDURE, STATEMENT nebo COMMENT pomáhá jednoznačně identifikovat dotaz mezi veškerým kódem ve zdrojovém kódu.

Následující dotaz používá dynamické zobrazení správy k vyhledávání podle popisku.

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
Další tipy pro vývoj najdete v [tématu přehled vývoje](sql-data-warehouse-overview-develop.md).


