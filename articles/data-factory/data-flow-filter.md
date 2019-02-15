---
title: Azure Data Factory mapování transformace filtr toku dat
description: Azure Data Factory mapování transformace filtr toku dat
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: b7e7b123560aae3a2d3086c8536969297d31f7ba
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271662"
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
