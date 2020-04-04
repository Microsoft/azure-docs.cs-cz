---
title: Použití štítků k přístrojovým dotazům
description: Tipy pro použití popisků k instrumentaci dotazů v fondu Synapse SQL pro vývoj řešení.
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
ms.openlocfilehash: 5e2cd03ae878e80139a7f7a8ba67cef15b24d571
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633494"
---
# <a name="using-labels-to-instrument-queries-in-synapse-sql-pool"></a>Použití štítků k instrumentaci dotazů ve fondu Synapse SQL

Součástí tohoto článku jsou tipy pro vývoj řešení pomocí popisků instrumentovat dotazy ve fondu SQL.

Tipy pro použití popisků k instrumentaci dotazů v Azure SQL Data Warehouse pro vývoj řešení.

## <a name="what-are-labels"></a>Co jsou štítky?

Fond SQL podporuje koncept nazývaný popisky dotazů. Než se vydáte do jakékoli hloubky, podívejme se na příklad:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

Poslední řádek označí řetězec Popisek mého dotazu na dotaz. Tato značka je užitečná, protože popisek je možné dotazovat prostřednictvím dmvs.

Dotazování na popisky poskytuje mechanismus pro vyhledání problémových dotazů a pomáhá identifikovat průběh prostřednictvím spuštění ELT.

Dobrá konvence pojmenování opravdu pomáhá. Například spuštění popisku s PROJECT, PROCEDURE, STATEMENT nebo COMMENT jednoznačně identifikuje dotaz mezi veškerý kód ve zdrojovém kódu.

Následující dotaz používá dynamické zobrazení správy k vyhledávání podle popisku:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> Při dotazování je nezbytné umístit čtvercové závorky nebo dvojité uvozovky kolem popisku slova. Popisek je vyhrazené slovo a způsobí chybu, pokud není oddělen.

## <a name="next-steps"></a>Další kroky

Další tipy pro vývoj najdete v [tématu přehled vývoje](sql-data-warehouse-overview-develop.md).
