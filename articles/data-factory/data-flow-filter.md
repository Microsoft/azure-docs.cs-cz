---
title: Azure Data Factory mapování transformace filtr toku dat
description: Azure Data Factory mapování transformace filtr toku dat
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 1930ca7406b6ef1b9fd20e1651bc031150dd952e
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729539"
---
# <a name="azure-data-factory-mapping-data-flow-filter-transformation"></a>Azure Data Factory mapování transformace filtr toku dat

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Filtr transformace poskytuje filtrování řádků. Sestavte výraz, který definuje podmínky filtru. Klikněte do textového pole pro spuštění Tvůrce výrazů. Uvnitř výrazu Tvůrce sestavení výraz filtru řídit, které řádky z aktuální datový proud můžou předávání (filtr) k další transformaci.

Například filtr na sloupec loan_status:

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Vyfiltrujte sloupec pro rok v ukázce filmy:

```
year > 1980
```
