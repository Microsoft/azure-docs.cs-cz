---
title: Azure Data Factory mapování transformace filtr toku dat
description: Azure Data Factory mapování transformace filtr toku dat
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: e0b41850c149ff7095333cf77b780dec1f03b882
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66234416"
---
# <a name="azure-data-factory-filter-transformation"></a>Azure data factory filtr transformace

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
