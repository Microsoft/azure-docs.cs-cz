---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 08/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c68b56463625a09f9c8341881284706ab8250f46
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608143"
---
Vazby Azure Cosmos DB se podporují jenom pro použití s rozhraním SQL API. Pro všechny ostatní Azure Cosmos DB API, by měl přístup k databázi z funkce s použitím statické klienta pro vaše rozhraní API, včetně [rozhraní API služby Azure Cosmos DB pro MongoDB](../articles/cosmos-db/mongodb-introduction.md), [Apache Cassandra API](../articles/cosmos-db/cassandra-introduction.md), [ Gremlin API](../articles/cosmos-db/graph-introduction.md), a [Table API](../articles/cosmos-db/table-introduction.md).
