---
title: Funkce data a času ve Azure Cosmos DB dotazovacího jazyka
description: Přečtěte si o funkcích systému SQL data a času v Azure Cosmos DB k provádění operací DateTime a timestamp.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 055fd5182920ebe15cb2f3ed4b7a8ff69bb94c16
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85549562"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>Funkce data a času (Azure Cosmos DB)

Funkce data a času umožňují provádět operace DateTime a timestamp v Azure Cosmos DB.

## <a name="functions"></a>Functions

Následující skalární funkce umožňují získat aktuální datum a čas UTC ve dvou formách. číselné časové razítko, jehož hodnota je epocha systému UNIX v milisekundách nebo jako řetězec, který odpovídá formátu ISO 8601:

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)

## <a name="next-steps"></a>Další kroky

- [Systémové funkce Azure Cosmos DB](sql-query-system-functions.md)
- [Úvod do Azure Cosmos DB](introduction.md)
- [Uživatelsky definované funkce](sql-query-udfs.md)
- [Agregace](sql-query-aggregates.md)
