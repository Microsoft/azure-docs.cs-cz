---
title: Transformace filtru toku dat Azure Data Factory mapování
description: Transformace filtru toku dat Azure Data Factory mapování
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: dd29753ff14d16081a46eebbc2ea02d94e5985f1
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029355"
---
# <a name="azure-data-factory-filter-transformation"></a>Transformace filtru Azure Data Factory



Transformace filtru poskytují filtrování řádků. Sestavte výraz definující podmínku filtru. Kliknutím do textového pole spusťte Tvůrce výrazů. V Tvůrci výrazů Sestavte výraz filtru, který určuje, které řádky z aktuálního datového proudu můžou předávat (filtrovat) k další transformaci. Transformaci filtru můžete představit jako klauzuli WHERE příkazu SQL.

## <a name="filter-on-loan_status-column"></a>Filtrovat podle sloupce loan_status:

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Filtrovat podle sloupce year v ukázce filmů:

```
year > 1980
```

## <a name="next-steps"></a>Další kroky

Zkuste transformaci filtrovat sloupce, [transformaci Select](data-flow-select.md)
