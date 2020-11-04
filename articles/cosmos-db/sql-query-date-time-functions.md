---
title: Funkce data a času ve Azure Cosmos DB dotazovacího jazyka
description: Přečtěte si o funkcích systému SQL data a času v Azure Cosmos DB k provádění operací DateTime a timestamp.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d8b6e932f4cd5f4119d93f3fed7db524f65cbc1f
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93338947"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>Funkce data a času (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
