---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 08/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c54aa861a47b11756f05e003e9b944df6c5b0e28
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132440"
---
Vazby Azure Cosmos DB se podporují jenom pro použití s rozhraním SQL API. Pro všechny ostatní Azure Cosmos DB API, by měl přístup k databázi z funkce s použitím statické klienta pro vaše rozhraní API, včetně [rozhraní API služby Azure Cosmos DB pro MongoDB](../articles/cosmos-db/mongodb-introduction.md), [Apache Cassandra API](../articles/cosmos-db/cassandra-introduction.md), [ Gremlin API](../articles/cosmos-db/graph-introduction.md), a [Table API](../articles/cosmos-db/table-introduction.md).
