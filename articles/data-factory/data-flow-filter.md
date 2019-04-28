---
title: Azure Data Factory mapování transformace filtr toku dat
description: Azure Data Factory mapování transformace filtr toku dat
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: d1751c47ad4507260d9f8d6ea44fcb32ed0e7338
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61347425"
---
# <a name="azure-data-factoryfilter-transformation"></a>FactoryFilter transformaci dat Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Filtr transformace poskytuje filtrování řádků. Sestavte výraz, který definuje podmínky filtru. Klikněte do textového pole pro spuštění Tvůrce výrazů. Uvnitř výrazu Tvůrce sestavení výraz filtru řídit, které řádky z aktuální datový proud můžou předávání (filtr) k další transformaci. Představte si filtr transformace jako klauzuli WHERE příkazu SQL.

## <a name="filter-on-loanstatus-column"></a>Vyfiltrujte sloupec loan_status:

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Vyfiltrujte sloupec pro rok v ukázce filmy:

```
year > 1980
```

## <a name="next-steps"></a>Další postup

Sloupec filtrování transformace, zkuste [vyberte transformace](data-flow-select.md)
