---
title: Funkce data a času ve Azure Cosmos DB dotazovacího jazyka
description: Přečtěte si o funkcích systému SQL data a času v Azure Cosmos DB k provádění operací DateTime a timestamp.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1ea838224f9d91b000100d5fa9308289619fd963
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88605197"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>Funkce data a času (Azure Cosmos DB)

Funkce data a času umožňují provádět operace DateTime a timestamp v Azure Cosmos DB.

## <a name="functions-to-obtain-the-date-and-time"></a>Funkce pro získání data a času

Následující skalární funkce vám umožní získat aktuální datum a čas UTC ve třech formulářích: řetězec, který odpovídá formátu ISO 8601, číselné časové razítko, jehož hodnota je počet milisekund, které uplynuly od epocha systému UNIX, nebo číselných impulsů, jejichž hodnota je 100 počet milisekund, které uplynuly od epocha systému UNIX. :

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)
* [GetCurrentTicks](sql-query-getcurrentticks.md)

## <a name="functions-to-work-with-datetime-values"></a>Funkce pro práci s hodnotami DateTime

Následující funkce umožňují snadno manipulovat s hodnotami DateTime, timestamp a Tick:

* [DateTimeAdd](sql-query-datetimeadd.md)
* [DateTimeDiff](sql-query-datetimediff.md)
* [DateTimeFromParts](sql-query-datetimefromparts.md)
* [DateTimePart](sql-query-datetimepart.md)
* [DateTimeToTicks](sql-query-datetimetoticks.md)
* [DateTimeToTimestamp](sql-query-datetimetotimestamp.md)
* [TicksToDateTime](sql-query-tickstodatetime.md)
* [TimestampToDateTime](sql-query-timestamptodatetime.md)

## <a name="next-steps"></a>Další kroky

- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do služby Azure Cosmos DB](introduction.md)
- [Uživatelsky definované funkce](sql-query-udfs.md)
- [Agregace](sql-query-aggregates.md)
