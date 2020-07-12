---
title: Funkce data a času ve Azure Cosmos DB dotazovacího jazyka
description: Přečtěte si o funkcích systému SQL data a času v Azure Cosmos DB k provádění operací DateTime a timestamp.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e3666f58b12855c19dd9b8ecf5519ab772c49743
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2020
ms.locfileid: "86246933"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>Funkce data a času (Azure Cosmos DB)

Funkce data a času umožňují provádět operace DateTime a timestamp v Azure Cosmos DB.

## <a name="functions-to-obtain-the-date-and-time"></a>Funkce pro získání data a času

Následující skalární funkce umožňují získat aktuální datum a čas UTC ve dvou formách: řetězec, který odpovídá formátu ISO 8601 nebo číselné časové razítko, jehož hodnota je epocha systému UNIX v milisekundách:

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)

## <a name="functions-to-work-with-datetime-values"></a>Funkce pro práci s hodnotami DateTime

Následující funkce umožňují snadno manipulovat s hodnotami DateTime:

* [DateTimeAdd](sql-query-datetimeadd.md)
* [DateTimeDiff](sql-query-datetimediff.md)
* [DateTimeFromParts](sql-query-datetimefromparts.md)

## <a name="next-steps"></a>Další kroky

- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
- [Uživatelsky definované funkce](sql-query-udfs.md)
- [Souhrny](sql-query-aggregates.md)
