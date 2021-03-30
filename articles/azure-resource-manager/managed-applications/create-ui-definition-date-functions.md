---
title: Vytvoření funkcí data definice uživatelského rozhraní
description: Popisuje funkce, které se použijí při práci s hodnotami kalendářních dat.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 80484fd15e51bae7036c43bd3ca8fe8167387ede
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87096956"
---
# <a name="createuidefinition-date-functions"></a>Funkce data CreateUiDefinition

Funkce, které se mají použít při práci s hodnotami kalendářních dat.

## <a name="addhours"></a>addHours

Přidá celočíselný počet hodin do zadaného časového razítka.

Následující příklad vrátí `"1991-01-01T00:59:59.000Z"` :

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addminutes"></a>addMinutes

Přidá do zadaného časového razítka celočíselný počet minut.

Následující příklad vrátí `"1991-01-01T00:00:59.000Z"` :

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addseconds"></a>addSeconds
Přidá do zadaného časového razítka celočíselný počet sekund.

Následující příklad vrátí `"1991-01-01T00:00:00.000Z"` :

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

## <a name="utcnow"></a>utcNow

Vrátí řetězec ve formátu ISO 8601 aktuálního data a času v místním počítači.

Následující příklad může vracet `"1990-12-31T23:59:59.000Z"` :

```json
"[utcNow()]"
```

## <a name="next-steps"></a>Další kroky

* Úvod do Azure Resource Manager najdete v tématu [Azure Resource Manager Overview](../management/overview.md).
